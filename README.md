# Zipack

Website: https://zipack.github.io/

Specification: [spec.md](./spec.md)

This repository manages spec of Zipack format. Zipack is an efficient serialization format which beats JSON, it's smaller, faster and supporting more data types. In depth, Zipack has a better encoding than UTF-8 and IEEE Floating Point.

filename extension: .zipack

mime type: application/zipack

## Features

1. small: Zipack is ~30% smaller than JSON.
2. fast: prefix based Zipack parses faster than text based formats.
3. rich types: Number, String, Bool, Null, ByteArray, List, Map and reserved types.
4. Huffman encoding: more common types use fewer bits, see [tree.km](./doc/tree.km).
5. new algorithm: Zipack has more compact encoding than UTF8 and IEEE Floating Point, see detail in [spec.md](./spec.md).
6. extendable: developers can add new data types into Zipack's reserved types.
7. streaming: with large data, process and transmit Zipack at the same time. Each Zipack object can be concatenated seamlessly like a List.

## Scenario

You can simply replace JSON with Zipack. Enjoy embedding files into Zipack without using Base64. What's more, as Zipack is schema-less, you can extend Zipack's reserved types to exchange schema between peers. Common scenario includes memory cache, RPC protocol, config file, etc.

## Motivation

The most popular serialization format today is JSON, which is based on text. However text format has drawbacks like slow compilation and larger size(cannot handle binary data directly) in comparison to prefix-based binary format. So I defined a compact, schema-less binary-based serialization format in compatibility with JSON, in order to provide better performance in storage / on wire.

## Ecosystem & Cooperation

Zipack is just a specification, we need software to implement it into practice. I have already realized a JavaScript version [zipack.js](https://github.com/zipack/zipack-javascript), but I need your help to build the entire ecosystem of Zipack. In order to build coder/decoder for different programing languages, jump to [spec.md](./spec.md) and leave any questions to me.

## About me

Location: Nanjing China

Email: jinhengyu666@qq.com or jinhengyu666@gmail.com

Blog (Chinese): https://jimmy.blog.csdn.net/

I am a programmer interested in Information Theory and Math. If you like Zipack, contact and join me, make it great together.

## License

Apache 2.0
