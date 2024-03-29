# Kontakt 1 - NKI Format

| # Bytes | Name           | Description                                                                          |
| :-------|:---------------|:-------------------------------------------------------------------------------------|
| 4       | File ID        | 5E E5 6E B3 - Identifies the Kontakt 1 file format.                                  |
| 4       | ZLIB Start     | The offset (number of bytes) in the file where the ZLIB starts. Always 0x24.         |
| 2       | Header Version | **TODO** always "50 00"                                                              |
| 2       | **TODO**       | **TODO** valid in the range of 00 - 03                                               |
| 4       | **TODO**       | **TODO** always 00                                                                   |
| 4       | **TODO**       | **TODO** always 00                                                                   |
| 4       | **TODO**       | **TODO** always "01 00 00 00"                                                        |
| 4       | Timestamp      | Unix-Timestamp UTC+1, e.g. "0B 0B 64 4D" (BE) is 1298402059 is "22.02.2011 20:14:19" |
| 4       | Sample size    | The sum of the size of all used samples (only the content data block of a WAV without any headers). |
| 4       | **TODO**       | **TODO** always 00                                                                   |
| V       | Inst. data     | XML document with all the data of the instrument, ZLIB encoded with Compression Level 2 and CINFO=7. Each tag is on one line, indentation with 2 spaces. |

# Kontakt 1.5 - NKI Format

This is identical to the Kontakt 1 format except that it uses the *File ID* from the Kontakt 2 format. That means that it can be Big-Endian (BE) or Little-Endian (LE) as well. To differentiate from the real Kontakt 2 format one can check the *ZLIB Start* which is always 0x24 for Kontakt 1.

# Kontakt 2 - 4.1.x - NKI Format

The file can be stored in Big-Endian (BE) or Little-Endian (LE). This can be detected by the File ID.
The number of bytes for a value (given in the 1st column below) need then be read/written either BE or LE.

## Structure

| # Bytes | Name              | Description                                                                 |
| :-------|:------------------|:-------------------------------------------------------------------------------------|
| 4       | File ID           | 12 90 A8 7F (BE) / 7F A8 90 12 (LE) - Identifies the Kontakt 2 file format.          |
| 4       | Compressed length | The length of the compressed data block (ZLIB or FastLZ for Kontakt 4).              |
| 2       | Header Version    | Kontakt 2: 0x100 / 4.2: 0x101                                                        |
| 4       | Magic             | K2: "72 2A 01 3E" (BE) / "3E 01 2A 72" (LE) - 4.2: "1A 63 37 EA (BE)"                |
| 2       | Patch Type        | 0=NKM, 1=NKI, 2=NKB, 3=NKP, 4=NKG, 5=NKZ - NKI: "01 00" (BE) / "00 01" (LE)          |
| 4       | Version           | Version of Kontakt which created the file E.g. "02 01 00 02" (BE) is 2.0.1.002, FF as the first byte means that the PatchLevel is stored below. |
| 4       | Library ID        | An ID from the [library ID](#library-id) list.                                       |
| 4       | Creation          | Unix-Timestamp UTC+1, e.g. "0B 0B 64 4D" (BE) is 1298402059 is "22.02.2011 20:14:19" |
| 4       | **TODO**          | **TODO**                                                                             |
| 2       | Num. Zones        | The number of zones.                                                                 |
| 2       | Num. Groups       | The number of groups.                                                                |
| 2       | Num. Inst.        | The number of instruments.                                                           |
| 4       | Sample Size Total | The sum of the size of all used samples (only the content data block of a WAV without any headers). |
| 4       | Is Monolith?      | 1 if it is a monolith.                                                               |
| 4       | Req. Kontakt Ver. | The minimum Kontakt version required to open this file.                              |
| 4       | **TODO**          | **TODO**                                                                             |
| 4       | Icon              | The icon of the instrument ([list of icon IDs](./Icons.md)).                         |
| 9       | Author            | The author of the instrument (ISO Latin Alphabet ISO_8859_1).                        |
| 1       | Category 1        | Index into the [category 1](#kontakt-2---category-1) list.                                                      |
| 1       | Category 2        | Index into the [category 2](#kontakt-2---category-2) list.                                                      |
| 1       | Category 3        | Index into the [category 3](#kontakt-2---category-3) list.                                                      |
| 87      | Web Link          | A URL to the website of the creator (ISO Latin Alphabet ISO_8859_1). Null terminated.|
| 2       | **TODO**          | **TODO** - Padding?                                                                  |
| 4       | Flags             | **TODO** - seems to be some kind of flags in a bit array                             |
| 4       | Checksum          | **TODO** MD5 checksum of the decompressed chunk data?! Which algo and which values? Seems to contain only data before the ZLIB section incl. the patch level. Start is also unclear, could be from the beginning or after (4, 8 or 16 bytes). |
| 4       | Patchlevel        | Patchlevel of Kontakt version. One 32-bit value.                                     |
| V       | Inst. data        | XML document with all the data of the instrument, ZLIB encoded with Compression Level 1 and CINFO=7. Each tag is on one line, indentation with 2 spaces. |
| 4       | SI Header Magic   | 0xB00EE1AE (LE)                                                                      |
| 4       | SI Header Version | 0x000C0101 (LE)                                                                      |
| 4       | SI Size           | The size of the following sound info block.                                          |
| V       | Soundinfo         | [Soundinfo](./Soundinfo.md) block containing info to be stored in the database.      |
   
# Kontakt 2 - 4.1.x - NKI Monolith Format

The header up to the ZLIB section is identical to the non-monolith version. 
Instead of the ZLIB section the monolith sample information starts.

It starts with several [Directory Blocks](#directory-block), which contain several [Entries](#directory-entry).
After that, there are sections with [IR-Samples](#ir-samples-reference) (optional), [Samples](#sample), and [Wallpaper](#wallpaper) image (optional) data.
Then there is another Directory Header follow by the normal NKI content (even replicting the metadata).

## Directory Block

| # Bytes | Name            | Description                                                                          |
| :-------|:----------------|:-------------------------------------------------------------------------------------|
| 4       | Magic ID        | 54 AC 70 5E                                                                          |
| 2       | Version         | 10 01 - The version of the header (e.g. 1.10).                                       |
| 4       | **TODO**        | 00 00 00 00                                                                          |
| 4       | **TODO**        | FF 00 00 00                                                                          |
| 4       | Number of items | The number of Directory Entries contained in the block.                               |
| 4       | Padding         | 00 00 00 00                                                                          |

## Directory Entry

| # Bytes | Name      | Description                                                                               |
| :-------|:----------|:------------------------------------------------------------------------------------------|
| 2       | Length    | Length of the Entry (incl. these 2 bytes).                                                 |
| 4       | Pointer   | A reference pointer. Byte position in the file where the related data is stored.          |
| 2       | Type      | [Type](#Directory-item-reference-types) of the reference.                                |
| V       |	Content   | The content of the Item. Text stored as UTF-16 null terminated.                           |

## Directory Entry Types

| #     | Description                                                           |
| :-----|:----------------------------------------------------------------------|
| 00 00 | Marks the last (empty) item.                                          |
| 01 00 | [Reference to another Directory Block](#Directory-block-reference).   |
| 02 00 | [Reference to a (IR-)sample](#ir-sample-reference).                   |
| 03 00 | [Reference to the NKI](#nki-reference).                               |
| 04 00 | [Reference to a wallpaper image](#wallpaper-reference).               |

### Directory Block Reference

| # Bytes | Name      | Description                                                                           |
| :-------|:----------|:--------------------------------------------------------------------------------------|
| 2       | Length    | Length of the item.                                                                   |
| 4       | Pointer   | Points to another [Directory Block](#directory-block).                                |
| 2       | Type      | 01 00                                                                                 |
| 16      |	Content   | "Samples", "IR Samples" or "Wallpaper" tag.                                           |

### (IR-)Sample Reference

| # Bytes | Name      | Description                                                                           |
| :-------|:----------|:--------------------------------------------------------------------------------------|
| 2       | Length    | Length of the item.                                                                   |
| 4       | **TODO**  | **TODO** - it does not a point to the file and it is not a timestamp... Checksum?     |
| 2       | Type      | 02 00                                                                                 |
| V       |	Content   | The original filename of the sample.                                                  |

### NKI Reference

| # Bytes | Name      | Description                                                                           |
| :-------|:----------|:--------------------------------------------------------------------------------------|
| 2       | Length    | Length of the item.                                                                   |
| 4       | Pointer   | Points to the start of the [NKI Data](#nki-data).                                     |
| 2       | Type      | 03 00                                                                                 |
| V       |	Content   | The NKI-Filename.                                                                     |

### Wallpaper Reference

| # Bytes | Name      | Description                                                                          |
| :-------|:----------|:-------------------------------------------------------------------------------------|
| 2       | Length    | Length of the item.                                                                  |
| 4       | Pointer   | Points to the location where the [wallpaper image](#wallpaper-image-data) starts in the file. |
| 2       | Type      | 04 00                                                                                |
| V       |	Content   | The original filename of the wallpaper.                                              |

## (IR-)Sample Data

| # Bytes | Name           | Description                                                                      |
| :-------|:---------------|:---------------------------------------------------------------------------------|
| 4       | Magic ID       | 0x16CCF80A                                                                       |
| 2       | Version        | 10 01 - the version of the header.                                               |
| 4       | **TODO**       | 00 00 00 00                                                                      |
| 4       | **TODO**       | FF 00 00 00                                                                      |
| 4       | **TODO**       | 01 00 00 00                                                                      |
| 1       | **TODO**       | 00                                                                               |
| 8       | File size      | The number of bytes of the sample file to follow.                                |
| 4       | Padding        | 00 00 00 00                                                                      |
| V       | Sample Data    | The raw bytes from the original sample file.                                     |

## Additional Sample Data ?!

| # Bytes | Name           | Description                                                                      |
| :-------|:---------------|:---------------------------------------------------------------------------------|
| 4       | Magic ID       | 0x0040179F                                                                       |
| 2       | Version        | 10 01 - the version of the header.                                               |
| 2       | **TODO**       | 00 00                                                                            |
| 4       | **TODO**       | 10 00 00 00                                                                      |
| 4       | **TODO**       | 02 00 00 00                                                                      |
| 1       | **TODO**       | 65                                                                               |
| 4       | **TODO**       | 06 00 00 00                                                                      |
| 1       | **TODO**       | 44                                                                               |
| V       | Data           | **TODO**                                                                         |

## Wallpaper Image Data

| # Bytes | Name           | Description                                                                      |
| :-------|:---------------|:---------------------------------------------------------------------------------|
| 4       | Magic ID       | 0x2AE905FA                                                                       |
| 2       | Version        | 10 01 - the version of the header.                                               |
| 4       | **TODO**       | 00 00 00 00                                                                      |
| 4       | **TODO**       | FF 00 00 00                                                                      |
| 8       | File size      | The number of bytes of the image file to follow.                                 |
| V       | Image Data     | ...                                                                              |

## NKI Data

The file ends with the NKI section.

| # Bytes | Name           | Description                                                                       |
| :-------|:---------------|:----------------------------------------------------------------------------------|
| 4       | Magic ID       | 0x4916E63C                                                                        |
| 2       | Header Version | 10 01                                                                             |
| 4       | **TODO**       | 00 00 00 00                                                                       |
| 4       | **TODO**       | FF 00 00 00                                                                       |
| 4       | **TODO**       | 01 00 00 00                                                                       |
| 1       | **TODO**       | 00                                                                                |
| 8       | File size      | The number of bytes of the NKI file to follow.                                    |
| V       | NKI            | The full data of a non-monolith NKI file. Even repeats the metadata.              |

# Kontakt 4.2.x - NKI Format

## Structure

The header and sound info parts are (mostly) identical to the previous version but the actual instrument part is different which uses the same format as Kontkt 5.

| # Bytes | Name           | Description                                                                                      |
| :-------|:---------------|:-------------------------------------------------------------------------------------------------|
| ...     | as Kontakt 2   | Identical to the Kontakt 2 header structure up to (excl.) the checksum.                          |
| 16      | Checksum       | **TODO** MD5                                                                                     |
| 4       | Patchlevel     | Patchlevel of Kontakt version. One 32-bit value (big-endian).                                    |
| 4       | CRC32          | CRC32 checksum of the compressed binary data.                                                    |
| 4       | Length Decomp. | Length of the decompressed data.                                                                 |
| 32      | **TODO**       | Padding?!                                                                                        |
| V       | Preset         | The preset data same as [Kontakt 5 Instrument Preset](./Kontakt5-Preset.md) but compressed with FastLZ (see https://ariya.github.io/FastLZ/).|
| V       | Soundinfo      | [Soundinfo](./Soundinfo.md) block containing info to be stored in the database.                  |

# Kontakt 5, 6 and 7

Starting with Kontakt 5, Native Instruments introduced a container format which is used by all current plugins.
The specification of the generic container format is here: [NI Container Format](./NI-Container.md)

The actual Kontakt Instrument preset is stored in the *Data* section of a *Preset Data Chunk*.
The format is documented here: [Kontakt 5 Instrument Preset](./Kontakt5-Preset.md)

## Lookup tables

### Library ID

Known library IDs are:

| Bytes (ASCII in little-endian) | Type                                  |
|:-------------------------------|:--------------------------------------|
| Kon2                           | Kontakt 2                             |
| Kon3                           | Kontakt 3                             |
| Kon4		                     | Kontakt 4                             |
| AkPi                           | Akustik Piano from Kontakt 3 Library  |
| ElPi                           | Elektrik Piano from Kontakt 3 Library |

### Kontakt 2 - Category 1

| Index | Name          |
|:------|:--------------|
| 0     | Other         |
| 1     | Piano         |
| 2     | Guitar        |
| 3     | Bass          |
| 4     | Drums         |
| 5     | Keyboard      |
| 6     | Synthesizer   |
| 7     | Strings       |
| 8     | Brass         |
| 9     | Organ         |
| 10    | Vocal         |
| 11    | Mallet        |
| 12    | Athmosphere   |
| 13    | Loop/Beat     |
| 14    | Pad           |
| 15    | Lead          |
| 16    | Soundeffect   |
| 17    | Woodwinds     |
| 18    | Percussion    |

### Kontakt 2 - Category 2

| Index | Name          |
|:------|:--------------|
| 0     | Other			|
| 1     | Acoustic      |
| 2     | Electric      |
| 3     | Solo          |
| 4     | Ensemble      |
| 5     | Analog        |
| 6     | Digital       |
| 7     | Synthetic     |
| 8     | Mixed         |
| 9     | Ethnic        |
| 10    | Steel         |
| 11    | Surround      |
| 12    | Synced        |
| 13    | KSP           |
| 14    | Convoluted    |
| 15    | Sequenced     |
| 16    | Spacious      |

### Kontakt 2 - Category 3

| Index | Name          |
|:------|:--------------|
| 0     | Other			|
| 1     | Noisy         |
| 2     | Metallic      |
| 3     | Clean         |
| 4     | Distorted     |
| 5     | Dark          |
| 6     | Light         |
| 7     | Groovy        |
| 8     | Harmonic      |
| 9     | Melodic       |
| 10    | Full          |
| 11    | Hard          |
| 12    | Dissonant     |
| 13    | Intense       |
| 14    | Relaxed       |
| 15    | Big           |
| 16    | Small         |
| 17    | Soft          |
