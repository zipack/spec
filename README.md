# Zipack Specification

> https://zipack.github.io/website

Zipack is an efficient serialization format, it's a replacement to JSON, but smaller, faster and richer(with more data types). Zipack borrows ideas from MessagePack, Protocol Buffer, Git's VLQ etc. but defines a better compression algorithm than all those before. This repository manages specification of zipack format, see [spec.md](./documents/spec.md).

## Motivation

The most popular serialization format today is JSON, which is based on text. However text format has drawbacks like slow compilation and larger size in comparison to binary format. So I defined a compact, schema-less binary-based serialization format in compatibility with JSON, in order to provide better performance in storage / on wire.

## Ecosystem

Zipack is essentially a specification, so we need software to implement it into practice. I have already realize a JavaScript version [zipack.js](https://github.com/zipack/zipack-javascript), but I need your help to build the ecosystem of zipack. In order to build coder/decoder for different programing languages, jump to [spec.md](documents/spec.md).
