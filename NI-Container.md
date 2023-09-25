# NI Container format

The container format is used by mostly all current NI plugins. Kontakt uses this format since version 5.

All numbers are stored little-endian.

## Container Item

The file starts with a Container Item.

| # Bytes | Name         | Description                                                           |
| :-------|:-------------|:----------------------------------------------------------------------|
| 8       | Size         | The size of the whole item incl. the size bytes themselves, which means the data content is the size minus 8. E.g. for the root item this is the size of the whole file. |
| 4       | Version      | Version check for NI Sound v1 (1)                                     |
| 4       | Domain ID    | Magic 4 characters 'nish' (Native Instruments Sound Header)           |
| 4       | **TODO**     | **TODO**   always 0?                                                  |
| 4       | Header Flags | Currently only uses 'deferred' as a flag.                             |
| 16      | UUID         | Universally Unique Identifier (UUID) for this item.                   |
| V       | Chunk List   | All chunks related to this item.                                      |
| 4       | Version      | The version (a number) of the child item format.                      |
| 4       | # Children   | The number of child items to follow.                                  |
| V       | Child Items  | The child items, if any.                                              |

## Container Child Item

| # Bytes | Name          | Description                                                                 |
| :-------|:--------------|:----------------------------------------------------------------------------|
| 4       | Sibling Index | The index of the child.                                                     |
| 4       | Domain ID     | 4 ASCII characters identifying the domain to which the data belongs.        |
| 4       | Chunk type    | The ID of the chunk type. The list of known chunk types can be found below. |
| V       | Item          | The Container Item structure of the child item.                             |

## Container Chunk

Each chunk can contain several other chunks before its' data section. The most inner chunk is indicated by 
a terminal chunk.

| # Bytes | Name         | Description                                                                 |
| :-------|:-------------|:----------------------------------------------------------------------------|
| 8       | Length       | The size of the bytes till the end of the chunk (incl. the size bytes themselves and all inner chunks), which means the 1st chunk contains the size of all chunks. |
| 4       | Domain ID    | 4 ASCII characters identifying the domain to which the data belongs. Most items are e.g. 'NISD' (Native Instruments Sound Domain. |
| 4       | Item ID      | The ID of the Item, within the domain.                                      |
| 4       | Version      | The version of the format used by the specific chunk type.                  |
| V       | Inner Chunks | The wrapped chunks, same structure starting with the Length field.          |
| V       | Data         | The actual data of the chunk. The size depends on the type of the chunk.    |

## Known Chunk Types

| # ID    | Description                                                                           |
| :-------|:--------------------------------------------------------------------------------------|
| 1       | [Terminator](#terminator-chunk). The last chunk in a chunk stack.                     |
| 3       | BNI Sound Preset (only used in NIK4 domain)                                           |
| 4       | BNI Sound Header (only used in NIK4 domain)                                           |
| 100     | Bank                                                                                  |
| 101     | Metadata information about the [Authoring Application](#authoring-application-chunk). |
| 102     | Bank Container                                                                        |
| 103     | Preset Container                                                                      |
| 104     | Binary Chunk Item                                                                     |
| 106     | [Authorization](#authorization-chunk).                                                |
| 108     | [Soundinfo](#soundinfo-chunk). Some metadata about the 'sound'.                       |
| 109     | [Preset Chunk Item](#preset-chunk-item) contains the actual preset data.              |
| 110     | External File Reference                                                               |
| 111     | Resources                                                                             |
| 112     | Audio Sample Item                                                                     |
| 113     | Internal Resource Reference Item                                                      |
| 114     | Picture Item                                                                          |
| 115     | A [Subtree Item](#subtree-item-chunk) stores another (compressed) Container Item.     |
| 116     | Encryption Item                                                                       |
| 117     | App Specific                                                                          |
| 118     | [Root](#root-chunk). The root chunk in a container.                                   |
| 120     | Automation Parameters                                                                 |
| 121     | Controller Assignments                                                                |
| 122     | Module                                                                                |
| 123     | Module Bank                                                                           |

## Additional Chunk Data

The following tables describe the additional data of the chunk types which are related to Kontakt.

### Authoring Application Chunk

| # Bytes | Name          | Description                                                                 |
| :-------|:--------------|:----------------------------------------------------------------------------|
| 4       | Version       | The version of the format used by the specific chunk type.                  |
| 1       | Compressed    | Is the data compressed?                                                     |
| 4       | Application   | The ID of the [authoring application](#authoring-application-ids) which created this file.|
| 4       | **TODO**      | Always 1.                                                                   |
| V       | App. Version  | The version of the authoring application which created the file (UTF-16LE with 4 byte length field).|

### Authorization Chunk

| # Bytes | Name          | Description                                                                 |
| :-------|:--------------|:----------------------------------------------------------------------------|
| 4       | Version       | The version of the format used by the specific chunk type.                  |
| 4       | **TODO**      | **TODO**                                                                    |
| 4       | **TODO**      | **TODO**                                                                    |
| 4       | **TODO**      | **TODO**                                                                    |
| V       | Serial        | The serial number of the sound (UTF-16LE with 4 byte length field).         |

### Preset Chunk Item

| # Bytes | Name          | Description                                                                 |
| :-------|:--------------|:----------------------------------------------------------------------------|
| 4       | Version       | The version of the format used by the specific chunk type.                  |
| 4       | **TBC**       | **TBC** Dictionary type.                                                    |
| 4       | **TBC**       | **TBC** Number of items in the Dictionary.                                  |
| 4       | Item Size     | The size of the item.                                                       |
| 4       | **TODO**      | **TODO**   00 00 00 00     does this belong to the size?                    |
| V       | Data          | The data of the preset. The content depends on the specfic preset.          |
| 4       | End Padding   | **TBC** 00 00 00 00                                                         |
| 4       | Checksum      | **TBC**                                                                     |

### Root Chunk

| # Bytes | Name          | Description                                                                 |
| :-------|:--------------|:----------------------------------------------------------------------------|
| 4       | Version       | The version of the format used by the specific chunk type.                  |
| 4       | Sound Version | The version of the NI sound format.                                         |
| 4       | Repo. Magic   | The magic number of the repository.                                         |
| 4       | Repo. Type    | The type of the repository.                                                 |
| 42      | **TODO**      | **TODO**                                                                    |

### Soundinfo Chunk

| # Bytes | Name            | Description                                                               |
| :-------|:----------------|:--------------------------------------------------------------------------|
| 4       | Version         | The version of the format used by the specific chunk type.                |
| 4       | Sound Version 1 | The version of the sound info format - Part 1                             |
| 4       | Sound Version 2 | The version of the sound info format - Part 2                             |
| 4       | Sound Version 3 | The version of the sound info format - Part 3                             |
| V       | Name            | The name of the sound (UTF-16LE with 4 byte length field)                 |
| V       | Author          | The name of the author (UTF-16LE with 4 byte length field)                |
| V       | Vendor          | The name of the vendor (UTF-16LE with 4 byte length field)                |
| 8       | **TODO**        | Always 0                                                                  |
| 8       | **TODO**        | Always -1 (FF FF FF FF FF FF FF FF)                                       |
| 8       | **TODO**        | Always 0                                                                  |
| 8       | **TODO**        | Always 0                                                                  |
| 4       | **TODO**        | Always 1                                                                  |
| 4       | **TODO**        | Always 1                                                                  |
| 4       | # Tags          | The number of tags to follow.                                             |
| V       | Tag 1-N         | Several tags (UTF-16LE with 4 byte length field).                         |
| 4       | # Attributes    | The number of attributes to follow.                                       |
| V       | Attribute 1-N   | Several attributes (UTF-16LE with 4 byte length field).                   |
| 4       | **TODO**        | Always 0                                                                  |
| 4       | # Properties    | The number of properties to follow.                                       |
| V       | Properties 1-N  | Each property consists of a key and value string (UTF-16LE with 4 byte length field).|

### Subtree Item Chunk

| # Bytes | Name          | Description                                                                 |
| :-------|:--------------|:----------------------------------------------------------------------------|
| 4       | Version       | The version of the format used by the specific chunk type.                  |
| 1       | Is Compressed | 1 if the item data is compressed otherwise 0.                               |
| 4       | Size uncompr. | The size of the uncompressed item data.                                     |
| 4       | Size compr.   | The size of the compressed item data. 0 if data is not compressed.          |
| V       | Item Data     | The un-/compressed data. Compression uses [FastLZ](https://ariya.github.io/FastLZ/). The uncompressed data is another [Container Item](#container-item). |

### Terminator Chunk

| # Bytes | Name          | Description                                                                 |
| :-------|:--------------|:----------------------------------------------------------------------------|
| 4       | Version       | The version of the format used by the specific chunk type.                  |

## Authoring Application IDs

| # ID    | Description      |
| :-------|:-----------------|
| 1       | Guitar Rig       |
| 2       | Kontakt          |
| 3       | Kore             |
| 4       | Reaktor          |
| 5       | Maschine         |
| 6       | Absynth          |
| 7       | Massive          |
| 8       | FM 8             |
| 9       | Battery          |
| 10      | Komplete Kontrol |
| 11      | SC               |
| 12      | FXF 01           |
| 13      | FXF 02           |
| 14      | FXF 03           |
| 15      | FXF 04           |
| 16      | FXF 05           |
| 17      | FXF 06           |
| 18      | FXF 07           |
| 19      | FXF 08           |
| 20      | FXF 09           |
| 21      | FXF 10           |
| 22      | FXF 11           |
| 23      | FXF 12           |
| 24      | FXF 13           |
| 25      | FXF 14           |
| 26      | FXF 15           |
| 27      | FXF 16           |
| 28      | FXF 17           |
| 29      | FXF 18           |
| 30      | FXF 19           |
| 31      | Traktor          |
