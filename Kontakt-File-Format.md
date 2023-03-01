# Kontakt File Format(s)

TODO = Not looked into yet or simply no idea
TBC  = Good guess but not confirmed yet

Each file format starts with a fixed 32 bit identifier (ID).

## File IDs

| Bytes         | Format                   | File Ending |
|:--------------|:-------------------------|:------------|
| 5E E5 6E B3   | Kontakt 1                | .nki        |
| 12 90 A8 7F   | Kontakt 2 - 4.2          | .nki        |
| 7F A8 90 12   | Battery 3                | .kt3        |
| 2D 6E 69 2D   | KORE                     | **TODO**    |

## Kontakt 1

| # Bytes | Name       | Description                             |
| :-------|:-----------|:----------------------------------------|
| 4       | File ID    | Identifies the type of the file format. |
| 4       | ZLIB Start | The number of bytes in the file where the ZLIB starts. **TBC** |
| X       | **TODO**   | **TODO** |

## Kontakt 2 - 4.2

### Structure

| # Bytes | Name       | Description                                   |
| :-------|:-----------|:----------------------------------------------|
| 4       | File ID    | Identifies the type of the file format.       |
| 14      | **TODO**   | **TODO**                                      |
| 3       | Version    | Version of Kontakt which created the file in reverse order. E.g. "01 01 04" is 4.1.1 |
| 4       | Block ID   | Type of the following file format.            |
| 4       | Timestamp  | Unix-Timestamp UTC+1, e.g. "0B 0B 64 4D" is 1298402059 is "22.02.2011 20:14:19" |
| 26      | **TODO**   | **TODO**                                      |
|         | Inst. Info | Information about the instrument.             |
| 4       | Icon       | The icon of the instrument.                   |
| 8       | Author     | The author of the instrument (ASCII only **TBC**) |
| 3       | **TODO**   | **TODO**                                      |
| **TODO**| Web Link   | A URL to the website of the creator.          |
| **TODO**| **TODO**   | **TODO**                                      |
| 4       | Patchlevel | Patchlevel of Kontakt version. One 32-bit value in reverse order. |
| X       | Inst. data | XML document with all the data of the instrument, ZLIB encoded. |
|         | **TODO**   | **TBC** check if there is anything more here  |
| Y       | Soundinfo  | Soundinfo block (only Kontakt 4).             |

- There is a checksum in there
- The Instrument Options dialog in Kontakt contains several settings which might be in there


#### Icons

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

### Soundinfo

Kontakt 4 files got an XML structure appended which contains the information to be stored in the Kontakt database.

- The XML document is fully formatted and not optimized for size. 
- The XML document starts with a proper XML header.
- The XML document is encoded with UTF-8.
- Each tag level is intended by 2 spaces (0x20). 
- Each line ends with a return character (0x0A).
- The soundinfo tag has 'version' attribute which seems to be always "400".

The following tags are known:

    soundinfo := properties, banks, attributes
    properties := name, author
    banks := bank
    attributes := attribute
    attribute := value

| XML Tag       | Description              |
|:--------------|:------------------|
| name          | The name of the instrument         |
| author        | The author of the instrument       |

Example:

    <?xml version="1.0" encoding="UTF-8" standalone="no" ?>
    <soundinfo version="400">

        <properties>
            <name>Lush Pad</name>
            <author>Klaus Meier</author>
        </properties>

        <banks>
            <bank>Lots of Pads</bank>
        </banks>

        <attributes>
            <attribute>
                <value>Bright Pad</value>
            </attribute>
            <attribute>
                <value>Evolving Pad</value>
            </attribute>
            <attribute>
                <value>KontaktInstrument</value>
            </attribute>
            <attribute>
                <value>Synth Pad</value>
            </attribute>
        </attributes>

    </soundinfo>

## Kontakt > 4.2

**TODO**

The metadata block is at the end of the file.

## Battery 3

**TODO**