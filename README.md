# MerkFarbe dictionary (German → English)

The German–English dictionary the **MerkFarbe** Android app downloads on
first start. It is a SQLite database built from Wiktionary, packed small for
phones. This repository holds nothing but that download: the app's code is
elsewhere.

## What is in a release

Each release is one version of the dictionary and carries two files:

| File | What it is |
|---|---|
| `wiktionary_de_phone-<date>.sqlite3.xz` | The dictionary, xz-compressed (about 34 MB; 181 MB unpacked). |
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
  "version": "2026-09-25T17:11:26",
  "schema": 5,
  "entries": 200925,
  "file": "wiktionary_de_phone-2026-09-25.sqlite3.xz",
  "url": "https://github.com/ali-padash/merkfarbe-dictionary-de-to-en/releases/download/dictionary-2026-09-25/wiktionary_de_phone-2026-09-25.sqlite3.xz",
  "tag": "dictionary-2026-09-25",
  "compressed_bytes": 33940636,
  "compressed_sha256": "20b8def49592eccd972a73527db40629d5fd5dd2ce1237a4e115c6d0d665e597",
  "bytes": 181043200,
  "sha256": "f94c33c9a12ebde58d3b38ea7c450141bb22f2a7c1328e33b75a2df66f5d80a2"
}
```

- `format` is the manifest's own layout; an app refuses a format it does not know.
- `version` is when the phone dictionary was built; a newer one replaces an older one.
- `schema` is the database layout; an app installs only a schema it can read.

## Using it without the app

Download the `.xz` from a release and unpack it with any xz tool
(`xz -d file.sqlite3.xz`, 7-Zip on Windows). The result is an ordinary SQLite
database.

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
