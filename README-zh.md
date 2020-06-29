# Zipack

官网：https://zipack.gitee.io/

规范（英文）：[spec.md](./spec.md)

这个仓库保存Zipack的规范，并且和Github仓库同步更新。Zipack是一套高效的二进制序列化格式，比JSON更小更快，支持更多的数据类型。在字符串和小数的编码上，Zipack采用原创的算法来取代臃肿的UTF-8和IEEE浮点数。

文件后缀名：.zipack

mime类型：application/zipack

## 优点

1. 体积更小：可以将JSON压缩至70%左右。
2. 速度更快：基于前缀的二进制格式无须编译，比文本格式更快。
3. 类型丰富：支持Number，String，Bool，Null，ByteArray，List，Map（字典）以及保留类型。
4. 变长编码：根据Huffman编码，常用的类型更短，如小整数只占1个字节。参考[tree.km](./doc/tree.km)
5. 原创算法：在处理字符串和浮点数上，Zipack采用压缩率更高的编码来取代标准的UTF8和IEEE浮点数，具体原理请参考[spec.md](./spec.md)。
6. 自由扩展：Zipack提供保留前缀，开发者可借此添加新的类型。
7. 流化传输：处理大数据的时候，Zipack可以无缝拼接，边传输边处理。

## 应用场景

你可以直接用Zipack取代JSON，同时ByteArray类型让你可以插入二进制文件而无须使用臃肿的Base64编码。由于Zipack是无格式的，你也可以选择利用保留类型来预交换格式。常见的使用场景包括内存缓存、RPC通信协议、配置文件等。

## 动机

当今最流行的序列化格式无疑是JSON，但是基于文本的JSON有许多缺点，比如解析速度慢，体积较大。而只有基于前缀的二进制格式能克服这些问题。所以我设计了一个紧凑的、无协议的二进制序列化格式Zipack用来取代JSON，为数据的存储和传输提供更好的方案。

## 生态系统：I need you

Zipack只是一个格式，想要投入使用，我们需要开发相应的软件。目前作者已经开发出JavaScript版本的编解码器：[zipack.js](https://github.com/zipack/zipack-javascript)。但是我需要你的帮助来共同建设Zipack的生态，请参考[spec.md](./spec.md)开发其他编程语言的zipack工具。

## 关于作者

坐标：江苏南京

Github：https://github.com/jinhengyu

Gitee：https://gitee.com/jinhengyu

博客：https://jimmy.blog.csdn.net/
