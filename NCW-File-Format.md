# The NCW format - KONTAKT Lossless Compression Audio File

Each file consists of 3 parts:

1. a header, 
2. a list of block addresses, and
3. several data blocks which contain the actual sample data.

## Header

| # Bytes | Name                            | Description                                                            |
| :-------|:--------------------------------|:-----------------------------------------------------------------------|
| 4       | NCW File Signature              | 0xD69EA801 Magic file type identifier  								 |
| 4       | Version                         | The version. Seen 0x131.                 								 |
| 2       | Number of channels              | The number of channels of the audio file (e.g. 2 for stereo). The data for the channel is not interleaved, instead there is 1 block for channel 1, then a block for channel 2, etc. |
| 2       | Bits per sample                 | The sample resolution of the original uncompressed file; 16, 24 or 32. |
| 4       | Samplerate                      | The sample rate in Hertz, e.g. 48000                                   |
| 4       | Number of samples               | Number of samples (frames).                                            |
| 4       | Offset block addresses          | Offset to the list of block addresses.                                 |
| 4       | Offset block data               | Offset to the block data section.                                      |
| 4       | Blocks size                     | Size of all blocks data in bytes.                                      |
| 88      | Padding                         | Always 0.                                                              |
| 4       | Offset 1                        | The offset to block 1.                                                 |
| 4       | Offset 2                        | The offset to block 2.                                                 |
|         | ...                             | ...                                                                    |

Offsets are from the **beginning of the Data Blocks**, not from the beginning of file. Therefore, the first entry is always zero and the last has the same value as 'Block size' from the main header.

The number of blocks can be calculated with this formula:

> Number of blocks = (Offset block data - Offset block addresses) / 4

## Data Block

Each block has a header of 16 bytes followed by data corresponding to 512 samples. The last block might be padded with zeroes to reach 512.
	
| # Bytes | Name              | Description                                                                          |
| :-------|:------------------|:-------------------------------------------------------------------------------------|
| 4       | Block signature   | 0x3E9A0C16                                                                           |
| 4       | Base value        | The initial start value for delta compression.                                       |
| 2       | Compression Type  | 0: [No compression](#no-compression), < 0: [Truncation Compression](#truncation-compression), > 0: [Delta Compression](#delta-compression).|
| 2       | Flags             | If value equals 1 then data is MID/SIDE. MID = (LEFT+RIGHT)/2, SIDE = (LEFT-RIGHT)/2. Therefore, the reverse operation is: LEFT = MID+SIDE, RIGHT = MID-SIDE.|
| 4       | Padding           | Padding with zeroes.                                                                 |

## Compression Types

### No Compression

If *Compression Type* is 0 then there's no compression. The **Bits per sample** field from the header gives the size of the sample values.

### Truncation Compression

If *Compression Type* is negative. The absolute value of the Compression type gives the bit precision. 

Concept: If the maximum amplitude in a current block requires a small number of bits, the whole block data can be stored with less bits. E.g. in a block with the maximum amplitude 238, 8 bit precision is enough to store all of it's samples.

Kontakt seems to choose the precisions 32, 16, or 8 bit which is the closest to the exact number (e.g. when the precision is 14 bit, 16 bit are used). Except, if the precision is less than 8 bits, then the exact number of bits is used, e.g. 6 bit.

### Delta Compression

If *Compression Type* is a positive value.

For blocks with a high value a different compression is chosen. The data stored in the block are not sample values, but the delta between 2 samples. The initial value to start the reverse calculation is the **Base value** from the block header.
