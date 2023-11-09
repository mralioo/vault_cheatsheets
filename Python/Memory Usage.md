
References:

* https://code.tutsplus.com/understand-how-much-memory-your-python-objects-use--cms-25609t
* https://realpython.com/python-data-types/


|Unit|Equivalent|
|---|---|
|1 kilobyte (KB)|1,024 bytes|
|1 megabyte (MB)|1,048,576 bytes|
|1 gigabyte (GB)|1,073,741,824 bytes|
|1 terabyte (TB)|1,099,511,627,776 bytes|
|1 petabyte (PB)|1,125,899,906,842,624 bytes|



### Angabe der Datenmenge in Byte

|Einheit|Abkürzung|Umrechnung zur nächst kleineren Einheit|
|---|---|---|
|Byte|B|1 Byte = 8 bit|
|Kilobyte|kB|1 kB = 1024 Byte|
|Megabyte|MB|1 MB = 1024 kB|
|Gigabyte|GB|1 GB = 1024 MB|
|Terabyte|TB|1 TB = 1024 GB|
|Petabyte|PB|1 PB = 1000 TB|

Einheiten für die Angabe von Datenmengen in Byte

### Angabe der Datenmenge in Bit

| Einheit | Abkürzung | Umrechnung zur nächst kleineren Einheit |
| ------- | --------- | --------------------------------------- |
| bit     | b         | 1 bit                                   |
| Kilobit | kb        | 1 kb = 1024 bit                         |
| Megabit | Mb        | 1 Mb = 1024 kb                          |
| Gigabit | Gb        | 1 Gb = 1024 Mb                          |
| Terabit | Tb        | 1 Tb = 1024 Gb                          |
| Petabit | Pb        | 1 Pb = 1024 Tb                          |
|         |           |                                         |
|         |           |                                         |



## Numpy 

|                      |                                    |                  |                                                                                           |
| -------------------- | ---------------------------------- | ---------------- | ----------------------------------------------------------------------------------------- |
| **Numpy data type**  | **Closely associated C data type** | **Storage Size** | **Description**                                                                           |
| np.bool_             | bool                               | 1 byte           | can hold boolean values, like (True or False) or (0 or 1)                                 |
| np.byte              | signed char                        | 1 byte           | can hold values from 0 to 255                                                             |
| np.ubyte             | unsigned char                      | 1 byte           | can hold values from -128 to 127                                                          |
| np.short             | signed short                       | 2 bytes          | can hold values from -32,768 to 32,767                                                    |
| np.ushort            | unsigned short                     | 2 bytes          | can hold values from 0 to 65,535                                                          |
| np.uintc             | unsigned int                       | 2 or 4 bytes     | can hold values from 0 to 65,535 or 0 to 4,294,967,295                                    |
| np.int_              | long                               | 8 bytes          | can hold values from -9223372036854775808 to 9223372036854775807                          |
| np.uint              | unsigned long                      | 8 bytes          | 0 to 18446744073709551615                                                                 |
| np.longlong          | long long                          | 8 bytes          | can hold values from -9223372036854775808 to 9223372036854775807                          |
| np.ulonglong         | unsigned long long                 | 8 bytes          | 0 to 18446744073709551615                                                                 |
| np.half / np.float16 | —                                  |                  | allows half float precision with  <br>Format: sign bit, 5 bits exponent, 10 bits mantissa |
| np.single            | float                              | 4 bytes          | allows single float precision  <br>Format: sign bit, 8 bits exponent, 23 bits mantissa    |
| np.double            | double                             | 8 bytes          | allows double float precision  <br>Format: sign bit, 11 bits exponent, 52 bits mantissa.  |
| np.longdouble        | long double                        | 8 bytes          | extension of float                                                                        |
| np.csingle           | float complex                      | 8 bytes          | can hold complex with real and imaginary parts up to  <br>single-precision float          |
| np.cdouble           | double complex                     | 16 bytes         | can hold complex with real and imaginary parts up to  <br>double-precision float          |
| np.clongdouble       | long double complex                | 16 bytes         | extension of float for complex number                                                     |
| np.int8              | int8_t                             | 1 byte           | can hold values from -128 to 127                                                          |
| np.int16             | int16_t                            | 2 bytes          | can hold values from -32,768 to 32,767                                                    |
| np.int32             | int32_t                            | 4 bytes          | can hold values from -2,147,483,648 to 2,147,483,647                                      |
| np.int64             | int64_t                            | 8 bytes          | can hold values from -9223372036854775808 to 9223372036854775807                          |
| np.uint8             | uint8_t                            | 1 byte           | can hold values from 0 to 255                                                             |
| np.uint16            | uint16_t                           | 2 bytes          | can hold values from 0 to 65,535                                                          |
| np.uint32            | uint32_t                           | 4 bytes          | can hold values from 0 to 4,294,967,295                                                   |
| np.uint64            | uint64_t                           | 8 bytes          | can hold values from 0 to 18446744073709551615                                            |
| np.intp              | intptr_t                           | 4 bytes          | a signed integer used for indexing                                                        |
| np.uintp             | uintptr_t                          | 4 bytes          | an unsigned integer used for holding a pointer                                            |
| np.float32           | float                              | 4 bytes          | single float precision                                                                    |
| np.float64           | double                             | 8 bytes          | double float precision                                                                    |
| np.complex64         | float complex                      | 8 bytes          | single float precision in complex numbers                                                 |
| np.complex128        | double complex                     | 16 bytes         | double float precision in complex numbers                                                 |


YypeBytesRangenp.int81-128 to 127np.int162-32768 to 32767np.int324-2147483648 to 2147483647np.int648-9223372036854775808 to 9223372036854775807