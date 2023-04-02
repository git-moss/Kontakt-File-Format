# NI Container format

The container format is used by mostly all current NI plugins. Kontakt uses this format since version 5.

All numbers are stored little-endian.

## Container Item

| # Bytes | Name        | Description                                                           |
| :-------|:------------|:----------------------------------------------------------------------|
| 8       | Size        | The size of the whole item incl. the size bytes themselves, which means the data content is the size minus 8. E.g. for the root item this is the size of the whole file. |
| 4       | **TODO**    | **TODO**   always 1?                                                  |
| 4       | Domain ID   | 4 ASCII characters identifying the domain to which the data belongs. Most items are e.g. 'hsin'. |
| 4       | **TODO**    | **TODO**   always 1?                                                  |
| 4       | **TODO**    | **TODO**   always 0?                                                  |
| 16      | UUID        | Universally Unique Identifier (UUID) for this item.                   |
| V       | Chunk List  | All chunks related to this item.                                      |
| 4       | Version     | The version (a number) of the child item format.                      |
| 4       | # Children  | The number of child items to follow.                                  |
| V       | Child Items | The child items, if any.                                              |

## Container Child Item

| # Bytes | Name        | Description                                                                 |
| :-------|:------------|:----------------------------------------------------------------------------|
| 4       | **TODO**    | **TODO**    Seen 0, 1, 2 and 1001                                           |
| 4       | Domain ID   | 4 ASCII characters identifying the domain to which the data belongs.        |
| 4       | Chunk type  | The ID of the chunk type. The list of known chunk types can be found below. |
| V       | Item        | The Container Item structure of the child item.                             |

## Container Chunk

Each chunk can contain several other chunks before its' data section. The most inner chunk is indicated by 
a terminal chunk.

| # Bytes | Name         | Description                                                                 |
| :-------|:-------------|:----------------------------------------------------------------------------|
| 8       | Length       | The size of the bytes till the end of the chunk (incl. the size bytes themselves and all inner chunks), which means the 1st chunk contains the size of all chunks. |
| 4       | Domain ID    | 4 ASCII characters identifying the domain to which the data belongs. Most items are e.g. 'DSIN'. |
| 4       | Chunk type   | The ID of the chunk type. The list of known chunk types can be found below. |
| 4       | Version      | The version of the format used by the specific chunk type.                  |
| V       | Inner Chunks | The wrapped chunks, same structure starting with the Length field.          |
| V       | Data         | The actual data of the chunk. The size depends on the type of the chunk.    |

## Known Chunk Types

| # ID    | Description                                                                  |
| :-------|:-----------------------------------------------------------------------------|
| 1       | [Terminator](#terminator-chunk). The last chunk in a chunk stack.            |
| 3       | BNI Sound Preset                                                             |
| 4       | BNI Sound Header                                                             |
| 100     | Bank                                                                         |
| 101     | Preset                                                                       |
| 102     | Bank Container                                                               |
| 103     | Preset Container                                                             |
| 104     | Binary Chunk Item                                                            |
| 106     | Authorization                                                                |
| 108     | [Soundinfo](#soundinfo-chunk). Some metadata about the 'sound'.              |
| 109     | Preset Chunk Item                                                            |
| 110     | External File Reference                                                      |
| 111     | Resources                                                                    |
| 112     | Audio Sample Item                                                            |
| 113     | Internal Resource Reference Item                                             |
| 114     | Picture Item                                                                 |
| 115     | Subtree Item                                                                 |
| 116     | Encryption Item                                                              |
| 117     | App Specific                                                                 |
| 118     | [Root](#root-chunk). The root chunk in a container.                          |
| 120     | Automation Parameters                                                        |
| 121     | Controller Assignments                                                       |
| 122     | Module                                                                       |
| 123     | Module Bank                                                                  |

## Additional Chunk Data

The following tables describe the additional data of the different chunk types.

### Root Chunk

| # Bytes | Name          | Description                                                                 |
| :-------|:--------------|:----------------------------------------------------------------------------|
| 4       | Version       | The version of the format used by the specific chunk type.                  |
| 4       | Sound Version | The version of the sound format.                                            |
| 4       | Magic number  | **TODO**                                                                    |
| 4       | Repo. Type    | The type of the repository. **TODO**                                        |
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

### Terminator Chunk

| # Bytes | Name          | Description                                                                 |
| :-------|:--------------|:----------------------------------------------------------------------------|
| 4       | Version       | The version of the format used by the specific chunk type.                  |
