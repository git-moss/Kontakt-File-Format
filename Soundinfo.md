# Soundinfo

Kontakt 2-4 files got an XML structure appended which contains the information to be stored in the Kontakt database.

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

| XML Tag       | Description                        |
|:--------------|:-----------------------------------|
| name          | The name of the instrument         |
| author        | The author of the instrument       |
| bank          | A name to group some instruments   |
| value         | A tag to categorize the instrument |

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
