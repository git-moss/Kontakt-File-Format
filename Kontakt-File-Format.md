# Kontakt File Format(s)

| File Ending    | Since | Content                                                                            |
|:---------------|:------|------------------------------------------------------------------------------------|
| **.nki**       | 1.0   | *Native Instruments Kontakt Instrument*. It contains one Instrument. It can 
                           also optionally contain the samples the .nki refers to; this is called a 
                           *monolith* file.                                                                   |
| **.nkb**       | 1.0   | *Native Instruments Kontakt Bank*. Groups several instruments as a bank which 
                           can be addressed with different MIDI channels and program changes.                 |
| **.nkm**       | 1.0   | *Native Instruments Multi*. Contains multiple .nki's (up to 64) and .nkb's.        |
| **.nkp**       | ?     | *Native Instruments Kontakt Preset*.                                               |
| **.nkg**       | ?     | The information of about a Kontakt Group (can be ex-/imported from the groups 
                           area in Kontakt).                                                                  |
| **.ncw**       | ?     | A lossless compressed sample. Can be un-/compressed with the Kontakt Batch Compress
                           dialog.                                                                            |
| **.nkc**       | ?     | Kontakt Cache File. Libraries are downloaded and delivered in .nkc encrypted 
                           format.                                                                            |
| **.nkx**       | ?     | Monolith file format which only contains samples. Requires additional NKI files.   |
| **.nkr**       | 4.2   | A resource container. Contains helper resources for a Kontakt script. Configured 
                           in the Instrument settings of a Kontakt instrument.                                |

## Abbreviations

| Abbreviation   | Meaning                                |
|:---------------|:---------------------------------------|
| **TODO**       | Not looked into yet or simply no idea. |
| **TBC**        | Good guess but not confirmed yet.      |
| **V**          | Variable length.                       |
| **big-endian** | The most significant byte is at the smallest memory address and the least significant byte at the largest. |

## Kontakt 1 - NKI Format

| # Bytes | Name       | Description                                                     |
| :-------|:-----------|:----------------------------------------------------------------|
| 4       | File ID    | 5E E5 6E B3 - Identifies the Kontakt 1 file format.             |
| 4       | ZLIB Start | The number of bytes in the file where the ZLIB starts. **TBC**  |
| 28 **TBC** | **TODO**   | **TODO**                                                     |
| V       | Inst. data | XML document with all the data of the instrument, ZLIB encoded with Compression Level 2 and CINFO=7. Each tag is on one line, indentation with 2 spaces. |

## Kontakt 2 - 4.1.x - NKI Format

### Structure

| # Bytes | Name       | Description                                                       |
| :-------|:-----------|:------------------------------------------------------------------|
| 4       | File ID    | 12 90 A8 7F - Identifies the Kontakt 2 file format.               |
| 4       | ZLIB length| The length of the ZLIB block (big-endian).                        |
| 8       | **TODO**   | **TODO**                                                          |
| 4       | Version    | Version of Kontakt which created the file in reverse order. E.g. "02 01 00 02" is 2.0.1.002, FF as the first byte means that the PatchLevel is stored below. |
| 4       | Block ID   | Type of the following file format.                                |
| 4       | Timestamp  | Unix-Timestamp UTC+1, e.g. "0B 0B 64 4D" (big-endian) is 1298402059 is "22.02.2011 20:14:19" |
| 26      | **TODO**   | **TODO**                                                          |
| -       | Inst. Info | Information about the instrument.                                 |
| 4       | Icon       | The icon of the instrument (big-endian).                          |
| 8       | Author     | The author of the instrument (ISO Latin Alphabet ISO_8859_1)      |
| 3       | **TODO**   | **TODO**                                                          |
| 86      | Web Link   | A URL to the website of the creator. Null terminated UTF-8.       |
| 7       | **TODO**   | **TODO**                                                          |
| 4       | **TODO**   | **TODO**   Checksum - but which algo and which values? Seems to contain only data before the ZLIB section incl. the patch level |
| 4       | Patchlevel | Patchlevel of Kontakt version. One 32-bit value (big-endian)).    |
| V       | Inst. data | XML document with all the data of the instrument, ZLIB encoded. Each tag is on one line, no indentation. |
| 12      | **TODO**   | **TODO**                                                          |
| V       | Soundinfo  | [Soundinfo](./Soundinfo.md) block containing info to be stored in the database. |

- There is a checksum in there
- The Instrument Options dialog in Kontakt contains several settings which might be in there

## Kontakt 2 - 4.1.x - NKI Monolith Format

### Structure

The header up to the ZLIB section is identical to the non-monolith version. Instead of the ZLIB section the monolith sample information starts.

| # Bytes | Name           | Description                                                       |
| :-------|:---------------|:------------------------------------------------------------------|
| 22      | **TODO**       | **TODO** starts with "54 AC 70 5E"                                |
| 8       | **TODO**       | **TODO**                                                          |
| 16      |	Samples Tag    | Text stored as UTF-8 Null-terminated.                             |
| 8       | **TODO**       | 4 byte offset + 4 byte length	?!                                 |
| V       |	NKI-Filename   | The NKI-Filename as UTF-8 Null-terminated.                        |
| 22      | **TODO**       | **TODO** starts with "54 AC 70 5E"                                |
| 8       | **TODO**       | **TODO**                                                          |
| 22      |	IR-Samples Tag | Text stored as UTF-8 Null-terminated.                             |
| 8       | **TODO**       | **TODO**  (optional)                                              |
| 22      |	Wallpaper Tag  | Text stored as UTF-8 Null-terminated (optional).                  |
|         | Samples Desc.  | All names and some info about the included samples. As many blocks as samples. |
| 2       | Length         | Length of block.                                                  |
| 2       | **TODO**       | **TODO**                                                          |
| 4       | **TODO**       | **TODO** Length of Sample / Frequency ?!                          |
| V       |	Filename       | The original file name of the sample.                             |
|         | Sample Data    |                                                                   |
| 22      | **TODO**       | **TODO** starts with "54 AC 70 5E"                                |
| 31      | **TODO**       | **TODO**                                                          |
| V       | **TODO**       | **TODO** Data                                                     |
| **TODO**| **TODO**       | **TODO**                                                          |

## Kontakt 4.2.x - NKI Format

### Structure

The header and sound info parts are (mostly) identical to the previous version but the actual instrument part is different.

| # Bytes | Name       | Description                                                       |
| :-------|:-----------|:------------------------------------------------------------------|
| ...     | as 4.1     | Identical to 4.1 structure up to and incl. the 7 bytes after the website info. |
| 12      | **TODO**   | **TODO** - 12 more bytes introduced in 4.2                        |
| 7       | **TODO**   | **TODO**                                                          |
| 4       | **TODO**   | **TODO**   Length of ZLIB or checksum?!                           |
| 4       | Patchlevel | Patchlevel of Kontakt version. One 32-bit value (big-endian).     |
| V       | Inst. data | **TODO**                                                          |
| 12      | **TODO**   | **TODO**                                                          |
| V       | Soundinfo  | [Soundinfo](./Soundinfo.md) block containing info to be stored in the database. |

## Kontakt 5, 6 and 7

**TODO**

The metadata block is at the end of the file.

## Battery 3

**TODO**

## Lookup tables

### Icons

There are 29 icons.

| Byte | Description     |
| :----|:----------------|
| 00   | Organ           |
| 01   | Cello           |
| 02   | Drum Kit        |
| 03   | Bell            |
| 04   | Trumpet         |
| 05   | Guitar          |
| 06   | Piano           |
| 07   | Marimba         |
| 08   | Record Player   |
| 09   | E-Piano         |
| 0A   | Drum Pads       |
| 0B   | Bass Guitar     |
| 0C   | Electric Guitar |
| 0D   | Wave            |
| 0E   | Asian Symbol    |
| 0F   | Flute           |
| 10   | Speaker         |
| 11   | Score           |
| 12   | Conga           |
| 13   | Pipe Organ      |
| 14   | Wave            |
| 15   | FX              |
| 16   | Computer        |
| 17   | Violin          |
| 18   | Surround        |
| 19   | Synthesizer     |
| 1A   | Oboe            |
| 1B   | Saxophone       |
| 1C   | New             |

### Block ID

| Bytes (as ASCII) | Type              |
|:-----------------|:------------------|
| 2noK             | Kontakt 2         |
| Kon3             | Kontakt 3         |
| 3noK             | Kontakt 3         |
| 4noK		       | Kontakt 4         |
| iPkA             | Akustik Piano from Kontakt 3 Library |
