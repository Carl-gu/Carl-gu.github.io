## The best way to understand a technical is finding some documents.
These blogs explain the format of .hex file.
https://blog.csdn.net/xsx669/article/details/83795274
https://en.wikipedia.org/wiki/Intel_HEX

Following are simple records for rememberring this format.
| MARK | RECLEN | OFFSET | RECTYP | DATA | CHKSUM |

| MARK |--------> (1 Byte)Start Code: One character, an ASCII colon ':'
| RECLEN |-----> (1 Byte)Byte count: two hex digits (one hex digit pair), indicating the number of bytes (hex digit pairs) in the data field. 
| OFFSET |-----> (2 Byte)Address: four hex digits, representing the 16-bit beginning memory address offset of the data. 
| RECTYP |-----> (1 Byte)Record type: two hex digits, 00 to 05, defining the meaning of the data field.
| DATA |----------> (n Byte)Data, a sequence of n bytes of data, represented by 2n hex digits.
| CHKSUM |-----> (1 Byte)Checksum: two hex digits, a computed value that can be used to verify the record has no errors.