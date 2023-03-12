# Kontakt File Format(s)

Kontakt is a sampler from Native Instruments which uses a plethora of file formats which all are sadly proprietary
and therefore no documentation is publicly available. This documentation tries to collect different pieces of
information which are already available on the internet and adds some findings based on trial and error as well
as common sense.

* Christian Schoenebeck from LinuxSampler found out about the ZLIB encoding of the instrument description and wrote
  tool which can export and import the human readable XML file from and to Kontakt .nki instrument        *
  files (Kontakt formats v1 to v4 are supported): http://www.linuxsampler.org/nkitool/
* Monomadic works on understanding the Kontakt 5+ formats and first findings are here: 
  https://github.com/monomadic/ni-file
* Maxton wrote a tool to extract NKX files which can be found here: https://github.com/maxton/nkxtract
* Jimi Ford wrote another NKX extractor which can also handle NKS archives: https://github.com/JimiHFord/unnks

Let's start with the known file types used by Kontakt:

| File Ending    | Since | Content                                                                            |
|:---------------|:------|------------------------------------------------------------------------------------|
| **.nki**       | 1.0   | *Native Instruments Kontakt Instrument*. It contains one Instrument. It can also optionally contain the samples the .nki refers to; this is called a *monolith* file. |
| **.nkb**       | 1.0   | *Native Instruments Kontakt Bank*. Groups several instruments as a bank which can be addressed with different MIDI channels and program changes. |
| **.nkm**       | 1.0   | *Native Instruments Multi*. Contains multiple .nki's (up to 64) and .nkb's.        |
| **.nkp**       | ?     | *Native Instruments Kontakt Preset*.                                               |
| **.nkg**       | ?     | The information of about a Kontakt Group (can be ex-/imported from the groups area in Kontakt). |
| **.ncw**       | ?     | A lossless compressed sample. Can be un-/compressed with the Kontakt Batch Compress dialog. |
| **.nkc**       | ?     | Kontakt Cache File. Libraries are downloaded and delivered in .nkc encrypted format. |
| **.nkx**       | ?     | Monolith file format which only contains samples. Requires additional NKI files.   |
| **.nkr**       | 4.2   | A resource container. Contains helper resources for a Kontakt script. Configured in the Instrument settings of a Kontakt instrument. |

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
| 8       | **TODO**   | **TODO** always "50 00 01 00 00 00 00 00"                         |
| 20 **TBC** | **TODO**   | **TODO**                                                     |
| V       | Inst. data | XML document with all the data of the instrument, ZLIB encoded with Compression Level 2 and CINFO=7. Each tag is on one line, indentation with 2 spaces. |

## Kontakt 2 - 4.1.x - NKI Format

### Structure

| # Bytes | Name       | Description                                                       |
| :-------|:-----------|:------------------------------------------------------------------|
| 4       | File ID    | 12 90 A8 7F - Identifies the Kontakt 2 file format.               |
| 4       | ZLIB length| The length of the ZLIB block (big-endian).                        |
| 8       | **TODO**   | **TODO** always "00 01 72 2A 01 3E 01 00"                         |
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
| 4       | **TODO**   | **TODO**   Checksum - but which algo and which values? Seems to contain only data before the ZLIB section incl. the patch level. Start is also unclear, could be from the beginning or after (4, 8 or 16 bytes). |
| 4       | Patchlevel | Patchlevel of Kontakt version. One 32-bit value (big-endian)).    |
| V       | Inst. data | XML document with all the data of the instrument, ZLIB encoded. Each tag is on one line, no indentation. |
| 12      | **TODO**   | **TODO**                                                          |
| V       | Soundinfo  | [Soundinfo](./Soundinfo.md) block containing info to be stored in the database. |

- There is a checksum in there
- The Instrument Options dialog in Kontakt contains several settings which might be in there

## Kontakt 2 - 4.1.x - NKI Monolith Format

The header up to the ZLIB section is identical to the non-monolith version. 
Instead of the ZLIB section the monolith sample information starts.

It consists of several Dictionary blocks. Each Dictionary block contains several Items.
After that, there are sections with the Sample, IR-Sample and Wallpaper image data. The file ends with the normal NKI
content (even replicting the metadata).

A Dictionary block looks like this:

| # Bytes | Name           | Description                                                                          |
| :-------|:---------------|:-------------------------------------------------------------------------------------|
| 14      | Main header    | Seems to be always "54 AC 70 5E  10 01 00 00  00 00 FF 00  00 00"                    |
| 4       | # sub-blocks   | The number of Dictionary Items contained in the block.                               |
| 4       | Padding        | **TBC**                                                                              |

A Dictionary Item looks like this:

| # Bytes | Name      | Description                                                                               |
| :-------|:----------|:------------------------------------------------------------------------------------------|
| 2       | Length    | Length of the Item (incl. these 2 bytes).                                                 |
| 4       | Pointer   | A reference pointer. Byte position in the file where the related data is stored.          |
| 2       | Type      | Type of the reference.                                                                    |
| V       |	Content   | The actual content of the Item.                                                           |

Known types of Dictionary Item references:

| #     | Description                        |
| :-----|:-----------------------------------|
| 01 00 | Reference to another Dictionary.   |
| 02 00 | Reference to a sample.             |
| 03 00 | Reference to the NKI start.        |
| 04 00 | Reference to a wallpaper image.    |

The known Dictionary Items are:

#### Samples Reference

| # Bytes | Name      | Description                                                                           |
| :-------|:----------|:--------------------------------------------------------------------------------------|
| 2       | Length    |                                                                                       |
| 4       | Pointer   | Points to the Dictionary block which contains the sample information (one IR-Sample reference and N-Sample references).|
| 2       | Type      | 01 00                                                                                 |
| 16      |	Content   | "Samples" tag. Text stored as UTF-16 null terminated.                                 |

#### NKI-Filename

| # Bytes | Name      | Description                                                                           |
| :-------|:----------|:--------------------------------------------------------------------------------------|
| 2       | Length    |                                                                                       |
| 4       | Pointer   | Beginning of NKI Instrument block.                                                    |
| 2       | Type      | 03 00                                                                                 |
| V       |	Content   | The NKI-Filename as UTF-16 Null-terminated.                                           |

#### IR-Samples Reference

| # Bytes | Name      | Description                                                                           |
| :-------|:----------|:--------------------------------------------------------------------------------------|
| 2       | Length    |                                                                                       |
| 4       | Pointer   | Points to the Dictionary block which contains the IR-Sample information.              |
| 2       | Type      | 01 00                                                                                 |
| 16      |	Content   | "IR Samples" tag. Text stored as UTF-16 null terminated.                              |

#### Sample

| # Bytes | Name      | Description                                                                           |
| :-------|:----------|:--------------------------------------------------------------------------------------|
| 2       | Length    |                                                                                       |
| 4       | Pointer   | Points to the Sample block which contains the Sample Data. **TBC**                    |
| 2       | Type      | 02 00                                                                                 |
| V       |	Content   | The original filename of the sample. Text stored as UTF-16 null terminated.           |

#### Wallpaper Reference

| # Bytes | Name           | Description                                                                      |
| :-------|:---------------|:---------------------------------------------------------------------------------|
| 2       | Length         |                                                                                  |
| 4       | Pointer        | Points to the Dictionary block which contains the Wallpaper information.         |
| 2       | Type           | 01 00                                                                            |
| 20      |	Content        | "Wallpaper" tag. Text stored as UTF-16 null terminated.                          |

#### Wallpaper

| # Bytes | Name           | Description                                                                      |
| :-------|:---------------|:---------------------------------------------------------------------------------|
| 2       | Length         |                                                                                  |
| 4       | Pointer        | Points to the location where the image starts in the file.                       |
| 2       | Type           | 04 00                                                                            |
| V       |	Content        | The original filename of the wallpaper. Text stored as UTF-16 null terminated.   |


### Sample/IR-Sample Sections

| # Bytes | Name           | Description                                                                      |
| :-------|:---------------|:---------------------------------------------------------------------------------|
| 31      | WAV Data header| Always "0A F8 CC 16  10 01 00 00  00 00 FF 00  00 00 01 00  00 00 00"            |
| 4       | Filelength     | Length of the sample file.                                                       |
| 4       | **TODO**       | **TBC** Offset to next WAV?                                                      |
| V       | Sample Data    | The raw bytes from the original sample file.                                     |

### Wallpaper Section

| # Bytes | Name           | Description                                                                      |
| :-------|:---------------|:---------------------------------------------------------------------------------|
| V       | Image Data     | Maybe here is a header before it, too.   **TODO**                                |

### NKI Section

The file ends with the NKI section.

| # Bytes | Name           | Description                                                                           |
| :-------|:---------------|:--------------------------------------------------------------------------------------|
| 27      | Header         | Always "3C E6 16 49  10 01 00 00  00 00 FF 00  00 00 01 00  00 00 00 22  0D 00 00 00  00 00 00" |
| V       | NKI            | The full data of a non-monolith NKI file. Even repeats the metadata.                  |


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

## NKX Format

**TODO**

## NCW Format

NCW File Signature can be 0x01A89ED631010000 or 0x01A89ED630010000.
The last DWORD value is different which likely indicates different compression algorithms.
NCW is ADPCM (-> more correctly DPCM since there is no adaptive or prediction algorithm like A-law or mu-law used.That's why it's loseless).

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
| 4noK		         | Kontakt 4         |
| iPkA             | Akustik Piano from Kontakt 3 Library |
