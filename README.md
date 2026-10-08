# MerkFarbe dictionary (German → English)

The German–English dictionary the **MerkFarbe** Android app downloads on
first start. It is a SQLite database built from Wiktionary, packed small for
phones. This repository holds nothing but that download: the app's code is
elsewhere.

## What is in a release

Each release is one version of the dictionary and carries two files:

| File | What it is |
|---|---|
| `dictionary_de_en-<date>.sqlite3.xz` | The dictionary, xz-compressed (about 30 MB; 140 MB unpacked). Called `wiktionary_de_phone-<date>.sqlite3.xz` up to schema 5. |
| `manifest.json` | Which dictionary is current: its version, schema, download URL, sizes and SHA-256 checksums. |

The app reads `manifest.json` from the latest release, at an address that
never changes:

```
https://github.com/ali-padash/merkfarbe-dictionary-de-to-en/releases/latest/download/manifest.json
```

From it the app learns where the `.xz` is, downloads it (resuming if the
connection drops), checks its checksum, unpacks it, checks the unpacked
database's checksum, and deletes the `.xz`. A phone that already has this
version downloads nothing.

### manifest.json

```json
{
  "format": 1,
  "version": "2026-10-08T03:36:58",
  "schema": 6,
  "entries": 201595,
  "file": "dictionary_de_en-2026-10-08.sqlite3.xz",
  "url": "https://github.com/ali-padash/merkfarbe-dictionary-de-to-en/releases/download/dictionary-2026-10-08/dictionary_de_en-2026-10-08.sqlite3.xz",
  "tag": "dictionary-2026-10-08",
  "compressed_bytes": 30095412,
  "compressed_sha256": "c9e37a64351b0847c2718d2d1582bfc0eccc2f3c2d9ead533d8b6b0c184268f7",
  "bytes": 139722752,
  "sha256": "54f006316d1b8c6f8d1475dbccd691614df14b5cbb55f10fd36966e4fe77418e"
}
```

- `format` is the manifest's own layout; an app refuses a format it does not know.
- `version` is when the phone dictionary was built; a newer one replaces an older one.
- `schema` is the database layout; an app installs only a schema it can read.
  Schema 6 (October 2026) stores each inflected form once with the entries it
  belongs to, packed; an app that reads schema 5 needs updating first.

## Using it without the app

Download the `.xz` from a release and unpack it with any xz tool
(`xz -d file.sqlite3.xz`, 7-Zip on Windows). The result is an ordinary SQLite
database. Most of it reads as it is; the one packed column is `forms.refs`,
each spelling's entries and notes as pairs of LEB128 varints (the entry
number less the previous pair's, then the note's number in `notes`, 0 for
none).

## Where the words come from, and the licence

The dictionary's content comes from **Wiktionary**:

- **English Wiktionary**, extracted by **[kaikki.org](https://kaikki.org/dictionary/German/)**
  with *wiktextract* (Tatu Ylonen: *Wiktextract: Wiktionary as Machine-Readable
  Structured Data*, LREC 2022, pp. 1317–1325);
- **German Wiktionary**, for German nouns English Wiktionary does not have.

Wiktionary's text is available under the
[Creative Commons Attribution-ShareAlike 4.0 licence (CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/),
and so is this dictionary, which is adapted from it: it has been reduced and
reorganised for a phone. The one thing added is a short table of glosses for
word parts such as *-ung* ("-ing, -tion: the act or its result"), written for
MerkFarbe, which explain the pieces of a compound.
You may share and adapt it, also commercially, as long as you credit
Wiktionary and its contributors and share what you make under the same
licence. See [LICENSE](LICENSE).

Every entry links back to its Wiktionary page, where its authors are listed.

## Making a release (for the maintainer)

From the MerkFarbe project:

```
python -m dictionary.publish_phone --out E:/merkfarbe-dictionary-de-to-en/dist
```

Then create a release on GitHub with the tag the command prints
(`dictionary-<date>`) and upload both files from `dist/`. Mark it as the
latest release: that is where the app looks. The `dist/` folder itself is
not committed.
