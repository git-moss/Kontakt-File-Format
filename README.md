# Introduction

Kontakt is a sampler from Native Instruments which uses a plethora of file formats which all are sadly proprietary
and therefore no documentation is publicly available. This documentation tries to collect different pieces of
information which are already available on the internet and adds some findings based on trial and error as well
as common sense.

Many thanks to all the people who did the hard work:

* Christian Schoenebeck from LinuxSampler found out about the ZLIB encoding of the instrument description and wrote tool which can export and import the human readable XML file from and to Kontakt .nki instrument files (Kontakt formats v1 to v4 are supported): http://www.linuxsampler.org/nkitool/
* Monomadic works on understanding the Kontakt 5+ formats and his findings are here: https://github.com/monomadic/ni-file
* Maxton wrote a tool to extract NKX files which can be found here: https://github.com/maxton/nkxtract
* Jimi Ford wrote another NKX extractor which can also handle NKS archives: https://github.com/JimiHFord/unnks
* ASTN on KVR documented the NCW format: https://www.kvraudio.com/forum/viewtopic.php?t=537033

Let's start with the known file types used by Kontakt:

| File Ending    | Since | Content                                                                            |
|:---------------|:------|------------------------------------------------------------------------------------|
| **.nki**       | 1.0   | [*Native Instruments Kontakt Instrument*](#known-kontakt-file-formats). It contains one Instrument. It can also optionally contain the samples the .nki refers to; this is called a *monolith* file. |
| **.nkm**       | 1.0   | *Native Instruments Multi*. Contains multiple .nki's (up to 64) and .nkb's.        |
| **.nkp**       | 1.0   | *Native Instruments Kontakt Module Preset*. All Kontakt modules can have their own presets. |
| **.nkb**       | 2.0   | *Native Instruments Kontakt Bank*. Groups several instruments as a bank which can be addressed with different MIDI channels and program changes. |
| **.nkg**       | ?     | The information of about a Kontakt Group (can be ex-/imported from the groups area in Kontakt). |
| **.ncw**       | 4     | [*Native Instruments Compressed Wave*](./NCW-File-Format.md). A lossless compressed sample. Can be un-/compressed with the Kontakt Batch Compress dialog. |
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

# Known Kontakt File Format(s)

* [Kontakt 1](./Kontakt-File-Format.md#kontakt-1---nki-format)
* [Kontakt 1.5](./Kontakt-File-Format.md#kontakt-15---nki-format)
* [Kontakt 2 - 4.1.x](./Kontakt-File-Format.md#kontakt-2---41x---nki-format)
* [Kontakt 2 - 4.1.x Monolith](./Kontakt-File-Format.md#kontakt-2---41x---nki-monolith-format)
* [Kontakt 4.2.x](./Kontakt-File-Format.md#kontakt-42x---nki-format)
* [Kontakt 5 - 7](./Kontakt-File-Format.md#kontakt-5-6-and-7)
