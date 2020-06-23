# Zipack specification

## Intro

Zipack is a little bit complicated, in comparison to other binary format like [MessagePack](https://msgpack.org/). I imported some great ideas into zipack to replace UTF-8 and IEEE Floating Point. That's why I call zipack "the perfect encoding" with confidence.

## The biased VLQ natural number

the big-endian [VLQ](https://en.wikipedia.org/wiki/Variable-length_quantity) is a technique to encode variable-length quantities on top of 7 bits, it uses the most significant bit of every byte to indicate the continuation of current data. 0xxxxxxx stands for the last byte, 1xxxxxxx means there are bytes to come. The decoder won't stop until 0xxxxxxx.

- 0xxxxxxx
- 1xxxxxxx 0xxxxxxx
- 1xxxxxxx 1xxxxxxx 0xxxxxxx
- ......

The key utility in zipack is called the [biased VLQ natural](https://en.wikipedia.org/wiki/Variable-length_quantity#Removing_redundancy) number which is implemented by Git and of cause Zipack, we call it VLQ's natural for short. So, the VLQ natural is the bijective numeration for natural numbers(0,1,2...). The mapping between VLQ and natural numbers is listed below:

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

> Make sure u understand this concept cause Zipack is heavily based on VLQ's natural

## Zipack String: Unicode on VLQ's natural

I abandoned UTF-8 for better compression. Since every Unicode character has an uniq number from 0, so Zipack String maps Unicode to VLQ's natural. The Zipack String is used in 2 position: the String type and the key in Map.

## Zipack Precision

I abandoned IEEE's Floating Point for better compression. 

## The Huffman prefix

Zipack supports 21 data types(with 6 reserved types), their definition is in [spec.km](./zipack.km). Generally every data type contains 3 parts: Class, Length and Payload. 