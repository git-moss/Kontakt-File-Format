# Kontakt5 Instrument Format

| # Bytes | Name         | Description                                                                     |
| :-------|:-------------|:--------------------------------------------------------------------------------|
| 1       | ID           | The ID for a Kontakt 5 Preset. Always 0x28.                                     |
| 4       | Size Block 1 | The size of the first data block.                                               |
| V       | Block 1      | The data of the first data block.                                               |
| 4       | Size Block 2 | The size of the second data block.                                              |
| V       | Block 2      | The data of the second data block.                                              |

## Block 1

| # Bytes | Name          | Description                                                                     |
| :-------|:--------------|:--------------------------------------------------------------------------------|
| 1       | ID            | The ID for the block. Always 1.                                                 |
| 2       | Checksum      | **TBC** It is no length or offset...                                            |
| 4       | Length        | The next of the next data section.                                              |
| V       | **TODO**      | **TODO** The next data section.                                                 |
| 4       | Size Metadata | The size of the metadata section (till the end of Web Link).                    |
| V       | Name          | The name of the sound (UTF-16LE with 4 byte length field).                      |
| 44      | **TODO**      | **TODO**                                                                        |
| 4       | Icon          | The icon of the instrument ([list of icon IDs](./Icons.md)).                    |
| V       | Description   | The description (instrument info) (UTF-16LE with 4 byte length field).          |
| V       | Author        | The name of the author (UTF-16LE with 4 byte length field).                     |
| V       | Web Link      | A URL to the website of the creator (UTF-16LE with 4 byte length field).        |
| V       | **TODO**      | The actual patch section. Groups, zones, etc.                                   |

## Block 2

| # Bytes | Name          | Description                                                                     |
| :-------|:--------------|:--------------------------------------------------------------------------------|

**TODO**