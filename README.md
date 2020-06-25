# Zipack

Website: https://zipack.github.io/

Specification: [spec.md](./spec.md)

This repository manages spec of Zipack format. Zipack is an efficient serialization format which beats JSON, it's smaller, faster and richer with data types including Number, String, Bool, Null, ByteArray, List, Map and reserved types. In depth, Zipack has a better encoding than UTF-8 and IEEE Floating Point.

## Scenario

You can simply replace JSON with Zipack, getting 30% size off. Or embed files into Zipack stream without Base64. What's more, as Zipack is schema-less, you can extend Zipack's reserved types to exchange schema between peers. Common scenario includes memory cache, RPC protocol, config file with .zipack suffix, etc.

## Streaming

When dealing with large data, we can process and transmit Zipack at the same time. Each Zipack object can be concatenated seamlessly like a List.

## Motivation

The most popular serialization format today is JSON, which is based on text. However text format has drawbacks like slow compilation and larger size in comparison to prefix-based binary format. So I defined a compact, schema-less binary-based serialization format in compatibility with JSON, in order to provide better performance in storage / on wire.

## Ecosystem

Zipack is essentially a specification, so we need software to implement it into practice. I have already realize a JavaScript version [zipack.js](https://github.com/zipack/zipack-javascript), but I need your help to build the ecosystem of zipack. In order to build coder/decoder for different programing languages, jump to [spec.md](./spec.md).

## About me

location: Nanjing China

Email: jinhengyu666@qq.com or jinhengyu666@gmail.com

Blog (Chinese): https://jimmy.blog.csdn.net/

I am a programmer interested in Information Theory and Math. If you like Zipack, contact and join me.