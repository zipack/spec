# Zipack specification

## Intro

Zipack is a little bit complicated in comparison to other binary format like [MessagePack](https://msgpack.org/) and [BSON](http://bsonspec.org/). I imported some great ideas into zipack to replace UTF-8 and IEEE Floating Point. That's why I call zipack "the perfect encoding" with confidence.

## Serialize and parse

Serialization is conversion from application objects into Zipack types. For example, JavaScript's Array can be converted into Zipack's List. Parse is inversed process of serialization.

## The biased VLQ natural number

The big-endian [VLQ](https://en.wikipedia.org/wiki/Variable-length_quantity) is a technique to encode variable-length quantities on top of 7 bits, it uses the most significant bit of every byte to indicate the continuation of current data. 0xxxxxxx stands for the last byte, 1xxxxxxx means there are bytes to come. The decoder won't stop until 0xxxxxxx, different length of VLQ shall like this:

- 0xxxxxxx
- 1xxxxxxx 0xxxxxxx
- 1xxxxxxx 1xxxxxxx 0xxxxxxx
- ......

The key utility in zipack is called the [biased VLQ natural](https://en.wikipedia.org/wiki/Variable-length_quantity#Removing_redundancy) number which is implemented by Git and of cause Zipack, we call it VLQ's Natural for short. So, the VLQ Natural is the [bijective numeration](https://en.wikipedia.org/wiki/Bijective_numeration) for natural numbers(0,1,2...). The mapping between VLQ and natural numbers is listed below:

| natural(decimal) | base128 VLQ (binary)
| ----   | ---  
| 0      | 0000000
| 1      | 0000001
| ...... | ......
| 127    | 1111111
| 128    | 0000000 0000000
| 129    | 0000000 0000001
| ...... | ......
| 16511  | 1111111 1111111

> Make sure u understand this concept cause Zipack is heavily based on VLQ's Natural

## Zipack's String: Unicode on VLQ's Natural

I abandoned UTF-8 for better compression. Since every Unicode character has an unique number from 0, so Zipack String maps Unicode to VLQ's natural. For example, char 'A' in Unicode is 65, so it's Zipack format is 01000001. Zipack String is used in 2 ways: the String type and the key in Map.

## Zipack's Precision: reversed bijective algorithm

I abandoned IEEE's Floating Point for better compression. Literally every non-integer can be splited by dot(.) into 2 parts: integer part and precision part. Since the trailing '0' in precision part is meaningless(e.g. 0.120=0.12), the heading '0' in natural number is also meaningless(e.g. 012=12). So when we reverse every bit of the precision part, the outcome can be uniquely mapped to a natural number(counting from 1, because 0 is integer). Here is an example encoding 110.0101 in binary:

1. remove meaningless '0' in both sides, no changes
2. split "110.0101" into "110" and "0101"
3. encode 110 into a VLQ's Natural (A)
4. reverse "0101" into "1010"
5. 1010 - 1 = 1001
6. encode 1001 into a VLQ's Natural (B)
7. concat A and B seamlessly and output (AB)

## The Huffman prefix

Zipack supports 21 data types(including 6 reserved types), their definition is [zipack.km](./zipack.km). Generally every data type contains 3 parts: CLASS, LENGTH and PAYLOAD. Here is their definition table:

|               |   CLASS | LENGTH | meaning of LENGTH | PAYLOAD
| --- | --- | --- | --- | ---
| Mini Natural  | 0  | none | \  | 7bit (0~127)
| VLQ +Int      | 1111 1000 | none          | \ | VLQ's natural + 128
| VLQ -Int      | 1111 1001 | none          | \ | -1 - VLQ's natural
| +Precision    | 1111 0010 | none          | \ | Zipack's Precision
| -Precision    | 1111 0011 | none          | \ | Zipack's Precision
| Mini String   | 100       | 5bit (0~31)         | count of chars | Zipack's String
| VLQ String    | 1111 0101 | VLQ's natural + 32 | count of chars | Zipack's String
| (key in Map)  | none      | VLQ's natural | count of key's chars | Zipack's String
| Bytes         | 1111 0100 | VLQ's natural | count of bytes    | the pure bytes
| Boolean       | 1111 0000, 1111 0001      | none | \ | \
| Null          | 1111 1010 | none          | \ | \
| Mini List     | 101       | 5bit (0~31)   | count of elements | List elements
| VLQ List      | 1111 0110 | VLQ's natural + 32 | count of elements | List elements
| Mini Map      | 110       | 5bit (0~31)   | count of key-value pairs | key-value pairs
| VLQ Map       | 1111 0111 | VLQ's natural + 32 | count of key-value pairs | key-value pairs
| reserved types | FB, FC, FD, FE, FF | \ | \ | \
| reserved types | 1110     | 4bit(0~15)    | count of objects | countable objects

## Notation in diagrams

```text
a single byte:
+--------+
|        |
+--------+

zero or more bytes:
+--------+
|       ....
+--------+

a VLQ's Natural:(contains one or more bytes)
+========+
|        |
+========+

zero or more VLQ's Natural:
+========+
|       ....
+========+

an element that can be any of basic/complex types:
++++++++++
|        |
++++++++++

zero or more elements:
++++++++++
|       ....
++++++++++

a key-value pair:
+########+
|        |
+########+

zero or more key-value pairs:
+########+
|       ....
+########+
```

## Basic types

The Basic types refer to True, False, Null, Integer Family, Precision Family, String, Bytes.

### Single byte types: True, False, Null

```text
True:
+--------+
|   F0   |
+--------+

False:
+--------+
|   F1   |
+--------+

Null:
+--------+
|   FA   |
+--------+
```

### Integer Family

```text
Mini Natural: stores 7-bit unsigned integer ranging from 0~127, in a single byte.
+--------+
|0xxxxxxx|
+--------+

VLQ +Int: stores a positive integer on top of Mini Natural(counting from 128), uses a VLQ's Natural to represent the value.
+--------+========+
|   F8   |  +128  |
+--------+========+

VLQ -Int: stores a negative integer counting from -1.
+--------+========+
|   F9   |   -1   |
+--------+========+
```

> xxxxxxx is a 7-bit natural unsigned integer.

> +128 means the real value of this VLQ should be added 128.

> -1 means the real value of this VLQ should be added 1 and negative it.

### Precision Family

The non-integer number is encoded using the "reversed bijective" algorithm I mentioned before. A Zipack's Precision number can be stored by 2 VLQ's Natural, one for Integer part(left), the other for Precision part(right).

```text
+Precision: stores a positive non-integer.
+--------+========+========+
|   F2   |  left  |  right |
+--------+========+========+

-Precision: stores a negative non-integer.
+--------+========+========+
|   F3   |  left  |  right |
+--------+========+========+
```

> left: the integer part.

> right: the precision part counting from 1.

### Pure Bytes

Bytes stores pure binary data like files, images, etc. Zipack has NO "Mini Bytes" for this type usually stores large data. It need a VLQ's Natural to represent the count of bytes.

```text
+--------+========+--------+
|   F4   |   +0   |       ....
+--------+========+--------+
```

> +0 means the real value of this VLQ is the count of pure bytes.

### String

As mentioned before, a Unicode char can be encoded by a VLQ's Natural, so String is seamlessly concatenated chars.

```text
Mini String: stores a Unicode string whose length < 32.
+--------+========+
|100xxxxx|       ....
+--------+========+

VLQ String: stores a Unicode string whose length >= 32, the LENGTH part should count from 32.
+--------+========+========+
|   F5   |   +32  |       ....
+--------+========+========+
```

> xxxxx is a 5-bit unsigned integer representing the count of chars.

> +32 means the real value of this VLQ representing the count of chars should be added 32.

## Complex types

Complex types refer to List, Map and reserved types. In comparison to basic types, complex types can nest other basic/complex types like JSON.

### List

Similar to String, Zipack List also defers Mini List and VLQ List.

```text
Mini List: stores a list whose length < 32.
+--------++++++++++
|101xxxxx|       ....
+--------++++++++++

VLQ List: stores a list whose length >= 32, the LENGTH part should count from 32.
+--------+========++++++++++
|   F6   |   +32  |       ....
+--------+========++++++++++
```

> xxxxx is a 5-bit unsigned integer representing the count of elements.

> +32 means the real value of this VLQ representing the count of elements should be added 32.

### Map

Zipack Map is key-value pairs where the key is a string and the value can be any of Zipack's basic/complex types. Similar to String and List, Map also defers Mini Map and VLQ Map.

```text
key-value pair: stores a string without prefix and a element.
+========+============++++++++++
|   +0   |    ....    |        |
+========+============++++++++++
or:
+########+
|        |
+########+

Mini Map: stores a map where the count of key-value pairs < 32.
+--------+########+
|110xxxxx|       ....
+--------+########+

VLQ Map: stores a map where the count of key-value pairs >= 32.
+--------+========+########+
|   F7   |   +32  |       ....
+--------+========+########+
```

> +0 means the real value of this VLQ represents the length of key.

> xxxxx is a 5-bit unsigned integer representing the count of key-value pairs.

> +32 means the real value of this VLQ representing the count of key-value pairs should be added 32.

### Reserved types

Zipack reserves 6 undefined prefix which can be extended by users:

- 1110
- 1111 1011
- 1111 1100
- 1111 1101
- 1111 1110
- 1111 1111

## 