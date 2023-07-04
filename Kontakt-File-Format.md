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
| **.nkm**       | 1.0   | *Native Instruments Multi*. Contains multiple .nki's (up to 64) and .nkb's.        |
| **.nkp**       | 1.0   | *Native Instruments Kontakt Module Preset*. All Kontakt modules can have their own presets. |
| **.nkb**       | 2.0   | *Native Instruments Kontakt Bank*. Groups several instruments as a bank which can be addressed with different MIDI channels and program changes. |
| **.nkg**       | ?     | The information of about a Kontakt Group (can be ex-/imported from the groups area in Kontakt). |
| **.ncw**       | 4     | A lossless compressed sample. Can be un-/compressed with the Kontakt Batch Compress dialog. |
| **.nkx**       | 2.1   | Monolith file format which only contains samples. NKX files are the Kontakt Player 2 version of monolithic sample containers (formerly called NKS files). |
| **.nkc**       | 2.1   | Kontakt Cache File. Libraries are downloaded and delivered in .nkc encrypted format. NKC files save a content overview of monolithic sample containers (NKS / NKX) and are stored next to the monolith files. |
| **.nkr**       | 4.2   | A resource container. Contains helper resources for a Kontakt script. Configured in the Instrument settings of a Kontakt instrument. |

## Abbreviations

| Abbreviation  | Meaning                                |
|:--------------|:---------------------------------------|
| **TODO**      | Not looked into yet or simply no idea. |
| **TBC**       | Good guess but not confirmed yet.      |
| **V**         | Variable length.                       |
| **BE**        | Big-endian. The most significant byte is at the smallest memory address and the least significant byte at the largest. |

## Kontakt 1 - NKI Format

| # Bytes | Name        | Description                                                         |
| :-------|:------------|:--------------------------------------------------------------------|
| 4       | File ID     | 5E E5 6E B3 - Identifies the Kontakt 1 file format.                 |
| 4       | ZLIB Start  | The number of bytes in the file where the ZLIB starts. Always 0x24. |
| 2       | **TODO**    | **TODO** always "50 00"                                             |
| 1       | **TODO**    | **TODO** valid in the range of 00 - 03                              |
| 9       | **TODO**    | **TODO** always 00                                                  |
| 4       | **TODO**    | **TODO** always "01 00 00 00"                                       |
| 4       | Timestamp   | Unix-Timestamp UTC+1, e.g. "0B 0B 64 4D" (BE) is 1298402059 is "22.02.2011 20:14:19" |
| 4       | Sample size | The sum of the size of all used samples (only the content data block of a WAV without any headers). |
| 4       | **TODO**    | **TODO** always 00                                                  |
| V       | Inst. data  | XML document with all the data of the instrument, ZLIB encoded with Compression Level 2 and CINFO=7. Each tag is on one line, indentation with 2 spaces. |

## Kontakt 2 - 4.1.x - NKI Format

The file can be stored in Big-Endian (BE) or Little-Endian (LE). This can be detected by the File ID.
The number of bytes for a value (given in the 1st column below) need then be read/written either BE or LE.

### Structure

| # Bytes | Name           | Description                                                                 |
| :-------|:---------------|:-------------------------------------------------------------------------------------|
| 4       | File ID        | 12 90 A8 7F (BE) / 7F A8 90 12 (LE) - Identifies the Kontakt 2 file format.          |
| 4       | ZLIB length    | The length of the ZLIB block.                                                        |
| 2       | Header Version | Kontakt 2: "00 01" (BE) / "01 00" (LE) - 4.2: "10 01 (BE)"                           |
| 4       | Patch Version  | K2: "72 2A 01 3E" (BE) / "3E 01 2A 72" (LE) - 4.2: "1A 63 37 EA (BE)"                |
| 2       | Patch Type     | 0=nkm, 1=nki, 2=nkb, 3=nkp, 4=nkg, nkz=5 - NKI: "01 00" (BE) / "00 01" (LE)          |
| 4       | Version        | Version of Kontakt which created the file E.g. "02 01 00 02" (BE) is 2.0.1.002, FF as the first byte means that the PatchLevel is stored below. |
| 4       | Block ID       | Type of the following file format, e.g. "Kon4".                                      |
| 4       | Creation       | Unix-Timestamp UTC+1, e.g. "0B 0B 64 4D" (BE) is 1298402059 is "22.02.2011 20:14:19" |
| 4       | **TODO**       | **TODO**                                                                             |
| 2       | Num.Zones      | The number of zones.                                                                 |
| 2       | Num.Groups     | The number of groups.                                                                |
| 2       | Num.Inst.      | The number of instruments.                                                           |
| 16      | **TODO**       | **TODO**                                                                             |
| 4       | Icon           | The icon of the instrument ([list of icon IDs](./Icons.md)).                         |
| 8       | Author         | The author of the instrument (ISO Latin Alphabet ISO_8859_1)                         |
| 3       | **TODO**       | **TODO**                                                                             |
| 86      | Web Link       | A URL to the website of the creator. Null terminated UTF-8.                          |
| 7       | **TODO**       | **TODO**                                                                             |
| 4       | **TODO**       | **TODO**   Checksum - but which algo and which values? Seems to contain only data before the ZLIB section incl. the patch level. Start is also unclear, could be from the beginning or after (4, 8 or 16 bytes). |
| 4       | Patchlevel     | Patchlevel of Kontakt version. One 32-bit value.                                     |
| V       | Inst. data     | XML document with all the data of the instrument, ZLIB encoded with Compression Level 1 and CINFO=7. Each tag is on one line, indentation with 2 spaces. |
| 12      | SI Header      | **TODO** AE E1 0E B0 01 01 0C 00 D9 00 00 00 (BE)                                    |
| V       | Soundinfo      | [Soundinfo](./Soundinfo.md) block containing info to be stored in the database.      |

## Kontakt 2 - 4.1.x - NKI Monolith Format

The header up to the ZLIB section is identical to the non-monolith version. 
Instead of the ZLIB section the monolith sample information starts.

It consists of several Dictionary blocks. Each Dictionary block contains several Items.
After that, there are sections with the Sample, IR-Sample and Wallpaper image data. The file 
ends with the normal NKI content (even replicting the metadata).

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

The header and sound info parts are (mostly) identical to the previous version but the actual instrument part is different which uses the same format as Kontkt 5.

| # Bytes | Name         | Description                                                                                      |
| :-------|:-------------|:-------------------------------------------------------------------------------------------------|
| ...     | as 4.1       | Identical to 4.1 structure up to and incl. the 7 bytes after the website info.                   |
| 12      | **TODO**     | **TODO**                                                                                         |
| 7       | **TODO**     | **TODO**                                                                                         |
| 4       | **TODO**     | **TODO**   Checksum?!                                                                            |
| 4       | Patchlevel   | Patchlevel of Kontakt version. One 32-bit value (big-endian).                                    |
| 6       | **TODO**     | **TODO**                                                                                         |
| 34      | **TODO**     | Padding?!                                                                                        |
| V       | Preset       | The preset data same as [Kontakt 5 Instrument Preset](./Kontakt5-Preset.md) ?!                   |
| 12      | **TODO**     | **TODO**                                                                                         |
| V       | Soundinfo    | [Soundinfo](./Soundinfo.md) block containing info to be stored in the database.                  |

## Kontakt 5, 6 and 7

Starting with Kontakt 5, Native Instruments introduced a container format which is used by all current plugins.
The specification of the generic container format is here: [NI Container Format](./NI-Container.md)

The actual Kontakt Instrument preset is stored in the *Data* section of a *Preset Data Chunk*.
The format is documented here: [Kontakt 5 Instrument Preset](./Kontakt5-Preset.md)

## Battery 3

**TODO**

## NKX Format

**TODO**

## NCW Format

NCW File Signature can be 0x01A89ED631010000 or 0x01A89ED630010000.
The last DWORD value is different which likely indicates different compression algorithms.
NCW is ADPCM (-> more correctly DPCM since there is no adaptive or prediction algorithm like A-law or mu-law used.That's why it's loseless).

## Lookup tables

### Block ID

Known block IDs are:

| Bytes (ASCII in little-endian) | Type                                 |
|:-------------------------------|:-------------------------------------|
| Kon2                           | Kontakt 2                            |
| Kon3                           | Kontakt 3                            |
| Kon4		                       | Kontakt 4                            |
| AkPi                           | Akustik Piano from Kontakt 3 Library |
