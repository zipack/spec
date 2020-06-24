# zipack 格式规范

官网：https://zipack.github.io/website

Zipack是一套高效的二进制序列化格式，比JSON更小更快，支持更多的数据类型包括Number，String，Bool，Null，ByteArray，List，Map以及保留类型。在字符串和小数的编码上，Zipack采用原创的算法来取代拥有诸多缺点的UTF-8和IEEE浮点数。

## 动机

当今最流行的序列化格式无疑是JSON，但是基于文本的JSON有许多缺点，比如解析速度慢，体积较大。而只有基于前缀的二进制格式能克服这些问题。所以我设计了一个紧凑的、无协议的二进制序列化格式Zipack用来取代JSON，为数据的存储和传输提供更好的方案。

## 生态系统

Zipack只是一个格式，想要投入使用，我们需要开发相应的软件。我已经开发了JavaScript版本的Zipack编解码器：[Zipack.js](https://github.com/zipack/zipack-javascript).
