# Kontakt 5 - 7 Instrument Format

This new preset format was first used in the Kontakt 4.2 format. It consists of hierarchical chunks, a bit similar to the NI Container format. Until there is a better name let's call these chunks 'Preset Chunks'.

The top data structure is one or more Preset Chunks which are simply appended to each other.

## Preset Chunk

| # Bytes | Name         | Description                                                                               |
| :-------|:-------------|:------------------------------------------------------------------------------------------|
| 2       | ID           | The ID of the data type contained in the chunk. See [Preset Chunk IDs](#preset-chunk-ids) |
| 4       | Data size    | The size of the data contained in the chunk.                                              |
| V       | Data Section | The data of the chunk.                                                                    |

## Default Data Section

Most of the chunks have the following format but there are some exceptions which need to be parsed individually.
The ones marked with 'Y' in the [Preset Chunk IDs](#preset-chunk-ids) table can be parsed normally. If there is a 'N' the format is described in a specific section below. If there is no comment, it is not yet researched.

| # Bytes | Name          | Description                                                                    |
| :-------|:--------------|:-------------------------------------------------------------------------------|
| 1       | Compressed    | Is the data compressed?                                                        |
| 4       | Block 1 Size  | The size of the 1st data block (private data block).                           |
| V       | Block 1 Data  | The data of the 1st block.                                                     |
| 4       | Block 2 Size  | The size of the 2nd data block (public data block).                            |
| V       | Block 2 Data  | The data of the 2nd block. Here is the relevant data.                          |
| 4       | Children Size | The size of the 2nd data block (public data block).                            |
| V       | Children Data | This are again 1 or more Preset Chunks.                                        |

## Preset Chunk IDs

| # ID    | Name                  | Description                                     | Default Data Section |
| :-------|:----------------------|:------------------------------------------------|:---------------------|
| 0x00    | BParModBase           |                                                 |                      |
| 0x01    | BAutomationObject     |                                                 |                      |
| 0x02    | OutputPartition       |                                                 |                      |
| 0x03    | BBank                 |                                                 |                      |
| 0x04    | BGroup                |                                                 |                      |
| 0x05    | BLoop                 |                                                 |                      |
| 0x06    | BParScript            |                                                 | Y                    |
| 0x07    | BParEnv               |                                                 |                      |
| 0x08    | BParLFO               |                                                 |                      |
| 0x09    | BParArp               |                                                 |                      |
| 0x0a    | BParEnvF              |                                                 |                      |
| 0x0b    | BParGlide             |                                                 |                      |
| 0x0c    | BParExternalMod       |                                                 |                      |
| 0x0d    | BParInternalMod       |                                                 |                      |
| 0x0e    | BParSrcMode           |                                                 |                      |
| 0x0f    | BParStartCriteria     |                                                 |                      |
| 0x10    | BParFXDelay           |                                                 |                      |
| 0x11    | BParFXChorus          |                                                 |                      |
| 0x12    | BParFXFlanger         |                                                 |                      |
| 0x13    | BParFXGainer          |                                                 |                      |
| 0x14    | BParFXPhaser          |                                                 |                      |
| 0x15    | BParFXReverb          |                                                 |                      |
| 0x16    | BParFXIRC             |                                                 |                      |
| 0x17    | BParFXSendLevels      |                                                 | Y                    |
| 0x18    | BParFXFilter          |                                                 |                      |
| 0x19    | BParFXCompressor      |                                                 |                      |
| 0x20    | BParFXLoFi            |                                                 |                      |
| 0x1a    | BParFXInverter        |                                                 |                      |
| 0x1b    | BParFXDYX             |                                                 |                      |
| 0x1c    | BParFXLimiter         |                                                 |                      |
| 0x1d    | BParFXSurroundPanner  |                                                 |                      |
| 0x1d    | BParFXShaper          |                                                 |                      |
| 0x1e    | BParFXDistortion      |                                                 |                      |
| 0x1f    | BParFXStereoSpread    |                                                 |                      |
| 0x20    | BParFXLofi            |                                                 |                      |
| 0x21    | BParFXSkreamer        |                                                 |                      |
| 0x22    | BParFXRotator         |                                                 |                      |
| 0x23    | BParFXTwang           |                                                 |                      |
| 0x24    | BParFXCabinet         |                                                 |                      |
| 0x25    | BParFX                |                                                 | N                    |
| 0x26    | BDyxMorphGroup        |                                                 |                      |
| 0x27    | BDyxMorphMap          |                                                 |                      |
| 0x28    | Program               | A Kontakt instrument program.                   | Y                    |
| 0x29    | BProgramContainer     |                                                 |                      |
| 0x2a    | BSample               |                                                 |                      |
| 0x2b    | VoiceGroup            |                                                 |                      |
| 0x2c    | BZone                 |                                                 |                      |
| 0x2d    | BZoneLevelEnv         |                                                 |                      |
| 0x2e    | BZoneArraySer         |                                                 |                      |
| 0x2f    | BGroupCompleteSer     |                                                 |                      |
| 0x30    | PresetImpl            |                                                 |                      |
| 0x32    | VoiceGroups           |                                                 | Y                    |
| 0x33    | GroupList             |                                                 | N                    |
| 0x34    | ZoneList              | A list with all sample zones.                   | N                    |
| 0x35    | PrivateRawObject      |                                                 |                      |
| 0x36    | ProgramList           |                                                 |                      |
| 0x37    | SlotList              |                                                 |                      |
| 0x38    | StarCritList          |                                                 |                      |
| 0x39    | LoopArray             | A loop for a zone.                              | N                    |
| 0x3a    | BParameterArraySer8   |                                                 | Y                    |
| 0x3b    | BParameterArraySer16  |                                                 | N                    |
| 0x3c    | BParameterArraySer32  |                                                 |                      |
| 0x3d    | FileNameList          |                                                 | N                    |
| 0x3e    | BOutputConfiguration  |                                                 |                      |
| 0x3f    | BParEnv_AHDSR         |                                                 |                      |
| 0x40    | BParEnv_FM7           |                                                 |                      |
| 0x41    | BParEnv_DBD           |                                                 |                      |
| 0x42    | BParFXTape            |                                                 |                      |
| 0x43    | BParFXTrans           |                                                 |                      |
| 0x44    | BParFXSSLGEQ          |                                                 |                      |
| 0x45    | BInsertBus            |                                                 | Y                    |
| 0x46    | BParFXSSLGBusComp     |                                                 |                      |
| 0x47    | SaveSettings          |                                                 | Y                    |
| 0x48    | PrivateRawObject      |                                                 |                      |
| 0x49    | PrivateRawObject      |                                                 |                      |
| 0x4a    | BParGroupDynamics     |                                                 |                      |
| 0x4b    | FileNameListEx        |                                                 | N                    |
| 0x4c    | BParFXFBComp          |                                                 |                      |
| 0x4d    | BParFXJump            |                                                 |                      |
| 0x4e    | QuickBrowseData       |                                                 | Y                    |
| 0x4f    | BSnapshot             |                                                 |                      |
| 0x50    | BGroupSnapshot        |                                                 |                      |
| 0x51    | BSnapshotMetaData     |                                                 |                      |
| 0x52    | BParFXVan51           |                                                 |                      |
| 0x53    | BParFXACBox           |                                                 |                      |
| 0x54    | BParFXHotSolo         |                                                 |                      |
| 0x54    | BParFXBassInvader     |                                                 |                      |
| 0x55    | BParFXCat             |                                                 |                      |
| 0x56    | BParFXDStortion       |                                                 |                      |
| 0x57    | BParFXPlateReverb     |                                                 |                      |
| 0x58    | BParFXCryWah          |                                                 |                      |
| 0x5a    | BParFXReplikaDelay    |                                                 |                      |
| 0x5b    | BParFXPhasis          |                                                 |                      |
| 0x5c    | BParFXFlair           |                                                 |                      |
| 0x5d    | BParFXChoral          |                                                 |                      |
| 0x5e    | BParFXCoreCell        |                                                 |                      |
| 0x5f    | BParFXHilbertLimiter  |                                                 |                      |
| 0x59    | BParFXGaloisReverb    |                                                 |                      |
| 0x60    | BParFXSupercharger    |                                                 |                      |
| 0x61    | BParFXBassPro         |                                                 |                      |
| 0x63    | BParFXPsycheDelay     |                                                 |                      |
| 0x64    | BParFXRingModulator   |                                                 |                      |

## Preset Chunk - Program (0x28)

The following format is used in the *Block 2 Data* section of a Preset Chunk. This is normally the top and first chunk.

| # Bytes | Name               | Description                                                                         |
| :-------|:-------------------|:------------------------------------------------------------------------------------|
| V       | Name               | The name of the instrument (UTF-16LE with 4 byte length field).                     |
| 8       | Samplesize         | The size of all samples of the preset (a float value).                              |
| 1       | MIDI transpose     | Instrument MIDI transpose in the range of -24.                                      |
| 4       | Volume             | Instrument volume (float).                                                          |
| 4       | Panorama           | Instrument panorama (float).                                                        |
| 4       | Tune               | Instrument tuning (float).                                                          |
| 1       | Low velocity       | Lower velocity bound of the instrument.                                             |
| 1       | High velocity      | Upper velocity bound of the instrument.                                             |
| 1       | Low key            | Lower key bound of the instrument.                                                  |
| 1       | High key           | Upper key bound of the instrument.                                                  |
| 2       | Default Key Switch | Default Key Switch.                                                                 |
| 4       | DFD Pre-load       | DFD Channel Pre-load Size.                                                          |
| 4       | Library ID         | Library ID. **TODO**                                                                |
| 4       | Fingerprint        | Fingerprint. **TODO**                                                               |
| 4       | Loading Flags      | Loading Flags. **TODO**                                                             |
| 1       | Group Solo         | 1 if group solo is turned on.                                                       |
| 4       | Instrument Icon    | The index of the icon. See the table in the Kontakt 2 format description.           |
| V       | Instrument Credits | Credits (UTF-16LE with 4 byte length field).                                        |
| V       | Instrument Author  | The author of the instrument (UTF-16LE with 4 byte length field).                   |
| V       | Instrument URL     | The website URL of the author's company (UTF-16LE with 4 byte length field).        |
| 2       | Category 1         | A category to classify the instrument. **TODO**                                     |
| 2       | Category 2         | A category to classify the instrument. **TODO**                                     |
| 2       | Category 3         | A category to classify the instrument. **TODO**                                     |
| **TODO**| **TODO**           | 8 (v5.3+), 20 (5.4+), or 24 (v7) more bytes available.                              |

## Preset Chunk - Zone List (0x34)

The following format is the full data section of the Preset Chunk. This Preset Chunk is normally found in a child chunk of a Program chunk.

| # Bytes | Name               | Description                                                                         |
| :-------|:-------------------|:------------------------------------------------------------------------------------|
| 4       | Array Length       | The number of zones in the list.                                                    |
| 4       | Children           | The number of children of the chunk (e.g. loop data).                               |
| 1       | Is structured?     | Is the data structured (has 2 blocks and children).                                 |
| 4       | Version            | The version of the zone structure.                                                  |
| 4       | Block 1 Size       | The size of the 1st data block (private data block).                                |
| V       | Block 1 Data       | The data of the 1st block.                                                          |
| 4       | Block 2 Size       | The size of the 2nd data block (public data block).                                 |
| V       | Block 2 Data       | The data of the 2nd block. Here is the relevant data.                               |
| 4       | Children Size      | The size of the 2nd data block (public data block).                                 |
| V       | Children Data      | This are again 1 or more Preset Chunks.                                             |

### Zone

This is the *Block 2 Data* of a *Zone List*. This structure is slightly different for certain versions (version attribute of a Zone List). If no specific version is noted for an attribute it is present in all versions.

| # Bytes | Version | Name               | Description                                                               |
| :-------|:--------|:-------------------|:--------------------------------------------------------------------------|
| 4       |         | Sample Start       | The start of the sample playback.                                         |
| 4       |         | Sample End         | The end of the sample playback. The value is the negative offset from the end of the sample (numFrames).|
| 4       |         | Sample Modulation  | The range of the sample modulation.                                       |
| 2       |         | Low velocity       | Lower velocity bound of the zone in the range of [1..127].                |
| 2       |         | High velocity      | Upper velocity bound of the zone in the range of [1..127].                |
| 2       |         | Low key            | Lower key bound of the zone [0..127].                                     |
| 2       |         | High key           | Upper key bound of the zone [0..127].                                     |
| 2       |         | Fade Low Velocity  | The number of velocity steps to crossfade in the range of [0..127].       |
| 2       |         | Fade High Velocity | The number of velocity steps to crossfade in the range of [0..127].       |
| 2       |         | Fade Low Key       | The number of semitones to crossfade in the range of [0..127].            |
| 2       |         | Fade High Key      | The number of semitones to crossfade in the range of [0..127].            |
| 2       |         | Root Key           | The root key of the sample (the assigned MIDI note) in the range of [0..127]. |
| 4       |         | Volume             | The volume, 0.0dB = 1.0, 0.015625 = -36dB, 64 = +36dB (float).            |
| 4       |         | Panorama           | The panorama in the range of [-1..1] (float)                              |
| 4       |         | Tune               | The tune setting in the range of [-32..32] semitones which is returned as 2^octave which maps to the range [0.125, 8.0], 2^(-32/12) = 0.125 (float). |
| 1       | 0x9A    | **TODO**           | **TODO**                                                                  |
| 1       | 0x9A    | **TODO**           | **TODO**                                                                  |
| 4       | 0x9A    | **TODO**           | **TODO**                                                                  |
| 1       |         | Filename Index     | The index in the filename list which contains the path and sample name to be used for this zone. |
| 4       |         | Sample Data Type   | The type: 2 = 16 bit, 3 = 24 bit                                          |
| 4       |         | Sample Rate        | The sample rate in Hertz, e.g. 44100                                      |
| 1       |         | Number of Channels | The number of audio channels, e.g. 2 for a stereo file.                   |
| 4       |         | Number of Frames   | The number of frames (number of samples of 1 channel).                    |
| 4       | <= 0x93 | **TODO**           | **TODO**                           |
| 4       |         | Root Note          | The root note at which the sample was recorded. Ignore if set to 0. Range of [0..127]. |
| 4       |         | Tuning             | The tuning (float) **TODO**                                               |
| 1       |         | **TODO**           | **TODO**                                                                  |
| 4       |         | **TODO**           | **TODO**                                                                  |

## Preset Chunk - Filename List (0x3D) / Filename List Ex (0x4B)

The following format is used in the *Block 2 Data* section of a Preset Chunk. This chunk is normally among the top chunks.

### Preset Chunk - Filename List (0x3D)

| # Bytes | Name               | Description                                                                         |
| :-------|:-------------------|:------------------------------------------------------------------------------------|
| 4       | Version            | The version of the data. Always 0.                                                  |
| 4       | File Count         | The number of files in the list.                                                    |
| V       | Segments           | For each file follows one [Segment Block](#segment-block).                          |
| V       | File Timestamps    | For each file follows one [Timestamp Block](#timestamp-block).                      |
| 4       | Padding            | Always 0.                                                                           |

### Filename List Ex (0x4B)

This is similar to 0x3D but with more options and parameters.

| # Bytes | Name               | Description                                                                         |
| :-------|:-------------------|:------------------------------------------------------------------------------------|
| 2       | Version            | The version of the Ex data. Always 2.                                               |
| 4       | File Category      | The file category: 1 = File or Folder, 2 = NKR file  **TODO**                       |
| 4       | Type (o)           | If it is not 0 it indicates a special file, e.g. NKR.                               |
| V       | E.g. NKR file (o)  | One [Segment Block](#segment-block) containing a NKR file or a folder.              |
| 4       | Version            | The version of the data. Always 0.                                                  |
| 4       | File Count         | The number of files in the list.                                                    |
| V       | Segments           | For each file follows one [Segment Block](#segment-block).                          |
| V       | File Timestamps    | For each file follows one [Timestamp Block](#timestamp-block).                      |
| 4       | Padding            | Always 0.                                                                           |
| V       | **TODO** (o)       | There is 1 integer for each file. The file at the last index is set to 0, 1, 2 or 3. All others seem to be always 0.|
| V       | NKI filename (o)   | The full path of the NKI file.                                                      |
| **TODO**| **TODO**           | There is more data for reverb samples (and maybe wallpaper). **TODO**               |
| 2       | End                | Always 1.                                                                           |

### Segment Block

| # Bytes | Name               | Description                                                               |
| :-------|:-------------------|:--------------------------------------------------------------------------|
| 4       | Segment Count      | The number of segments of the path.                                       |
| V       | Segment            | For each segment follows one [Segment](#segment).                |

### Segment

| # Bytes | Name               | Description                                                               |
| :-------|:-------------------|:--------------------------------------------------------------------------|
| 1       | Segment Type       | 2 = a part of the path (normally a sub-folder name), 4 = the file name.   |
| V       | Segment Text       | Either a path or the file name (UTF-16LE with 4 byte length field).       |

### Timestamp Block

| # Bytes | Name               | Description                                                               |
| :-------|:-------------------|:--------------------------------------------------------------------------|
| 4       | Last change        | The timestamp of the last change of the file. Unix-Timestamp UTC+1.       |
| 4       | Padding            | Always 0.                                                                 |

## Preset Chunk - Loop Array (0x39)

The following format is used in the *Block 2 Data* section of a Preset Chunk. This chunk is a child chunk of a zone chunk.

| # Bytes | Name               | Description                                                               |
| :-------|:-------------------|:--------------------------------------------------------------------------|
| 2       | Number of loops    | The number of loops for the zone (1-8).                                   |
| 2       | **TODO**           | Seems to be always 0x60.                                                  |
| V       | Loops              | The data of 0 to 8 loops.                                                 |

### Loop Data

| # Bytes | Name               | Description                                                               |
| :-------|:-------------------|:--------------------------------------------------------------------------|
| 4       | Mode               | The loop mode. 0x1: Until End, 0x1006000: Until End <->, 0x0: Until Release, 0x3F80: Until Release <->, 0x80000001: Oneshot|
| 4       | Loop Start         | The start of the loop in samples.                                         |
| 4       | Loop Length        | The length of the loop in samples.                                        |
| 4       | Loop Count         | How often the loop should be repeated.                                    |
| 1       | Alternating        | 1 if the loop is alternating.                                             |
| 4       | Tuning             | The tuning of the loop.                                                   |
| 4       | Crossfade Length   | The length of the crossfade in samples.                                   |
