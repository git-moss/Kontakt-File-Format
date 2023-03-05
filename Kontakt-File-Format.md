# Kontakt File Format(s)

## Abbreviations

| Abbreviation | Meaning                               |
|:-------------|:--------------------------------------|
| **TODO**     | Not looked into yet or simply no idea |
| **TBC**      | Good guess but not confirmed yet      |
| **V**        | Variable length                       |

## Kontakt 1

| # Bytes | Name       | Description                                                     |
| :-------|:-----------|:----------------------------------------------------------------|
| 4       | File ID    | 5E E5 6E B3 - Identifies the Kontakt 1 file format.             |
| 4       | ZLIB Start | The number of bytes in the file where the ZLIB starts. **TBC**  |
| 28 **TBC** | **TODO**   | **TODO**                                                     |
| V       | Inst. data | XML document with all the data of the instrument, ZLIB encoded. Each tag is on one line, indentation with 2 spaces. |

## Kontakt 2 - 4.1.x

### Structure

| # Bytes | Name       | Description                                                       |
| :-------|:-----------|:------------------------------------------------------------------|
| 4       | File ID    | 12 90 A8 7F - Identifies the Kontakt 2 file format.               |
| 12      | **TODO**   | **TODO**                                                          |
| 4       | Version    | Version of Kontakt which created the file in reverse order.       |
|         |            | E.g. "02 01 00 02" is 2.0.1.002, FF as the first byte means that the PatchLevel is stored below. |
| 4       | Block ID   | Type of the following file format.                                |
| 4       | Timestamp  | Unix-Timestamp UTC+1, e.g. "0B 0B 64 4D" is 1298402059 is "22.02.2011 20:14:19" |
| 26      | **TODO**   | **TODO**                                                          |
| -       | Inst. Info | Information about the instrument.                                 |
| 4       | Icon       | The icon of the instrument.                                       |
| 8       | Author     | The author of the instrument (ASCII only **TBC**)                 |
| 3       | **TODO**   | **TODO**                                                          |
| 86      | Web Link   | A URL to the website of the creator. Null terminated UTF-8.       |
| 7       | **TODO**   | **TODO**                                                          |
| 4       | **TODO**   | **TODO**   Length of ZLIB or checksum?!                           |
| 4       | Patchlevel | Patchlevel of Kontakt version. One 32-bit value in reverse order. |
| V       | Inst. data | XML document with all the data of the instrument, ZLIB encoded. Each tag is on one line, no indentation. |
| 12      | **TODO**   | **TODO**                                                          |
| V       | Soundinfo  | [Soundinfo](./Soundinfo.md) block containing info to be stored in the database. |

- There is a checksum in there
- The Instrument Options dialog in Kontakt contains several settings which might be in there

## Kontakt 4.2.x

### Structure

| # Bytes | Name       | Description                                                       |
| :-------|:-----------|:------------------------------------------------------------------|
| ...     | as 4.1     | Identical to 4.1 structure up to and incl. the 7 bytes after the website info. |
| 12      | **TODO**   | **TODO** - 12 mnore bytes introduced in 4.2                       |
| 7       | **TODO**   | **TODO**                                                          |
| 4       | **TODO**   | **TODO**   Length of ZLIB or checksum?!                           |
| 4       | Patchlevel | Patchlevel of Kontakt version. One 32-bit value in reverse order. |
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
| 4noK		       | Kontakt 4         |
| iPkA             | Kontakt 3 Library **TBC** |
