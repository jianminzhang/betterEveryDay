#Protobuf

##参考

官方网址：

* https://developers.google.com/protocol-buffers/
* https://github.com/google/protobuf

介绍网址：

* http://www.yanyaozhen.com/archives/241/
* https://www.ibm.com/developerworks/cn/linux/l-cn-gpb/
* http://blog.csdn.net/losophy/article/details/17006573

##是什么

Protobuf是google推出的一种高效的数据编码方式，类似于XML、json等，都是把某种数据结构的数据以某种方式保存起来，主要用于数据的传输，在传输的两端进行数据的序列化与反序列化。

##优点

1. Protobuf 有如 XML，不过它更小、更快、也更简单。你可以定义自己的数据结构，然后使用代码生成器生成的代码来读写这个数据结构。你甚至可以在无需重新部署程序的情况下更新数据结构。只需使用 Protobuf 对数据结构进行一次描述，即可利用各种不同语言或从各种不同数据流中对你的结构化数据轻松读写。

2. 它有一个非常棒的特性，即“向后”兼容性好，人们不必破坏已部署的、依靠“老”数据格式的程序就可以对数据结构进行升级。这样您的程序就可以不必担心因为消息结构的改变而造成的大规模的代码重构或者迁移的问题。因为添加新的消息中的 field 并不会引起已经发布的程序的任何改变。

3. Protobuf 语义更清晰，无需类似 XML 解析器的东西（因为 Protobuf 编译器会将 .proto 文件编译生成对应的数据访问类以对 Protobuf 数据进行序列化、反序列化操作）。

4. 使用 Protobuf 无需学习复杂的文档对象模型，Protobuf 的编程模式比较友好，简单易学，同时它拥有良好的文档和示例，对于喜欢简单事物的人们而言，Protobuf 比其他的技术更加有吸引力。

##不足
1. 通用性不足：
Protbuf 与 XML 相比也有不足之处。它功能简单，无法用来表示复杂的概念。XML 已经成为多种行业标准的编写工具，Protobuf 只是 Google 公司内部使用的工具，在通用性上还差很多。

2. Protobuf 不适合用来对基于文本的标记文档（如 HTML）建模，由于文本并不适合用来描述数据结构。

3. 由于 XML 具有某种程度上的自解释性，它可以被人直接读取编辑，在这一点上 Protobuf 不行，它以二进制的方式存储，除非你有 .proto 定义，否则你没法直接读出 Protobuf 的任何内容

##本地配置使用
见上面参考

1. 首先定义自己的.proto文件，即message的内容
2. 安装编译器并且将.proto文件编译生成对应的python代码（例如 AdBuff_pb2.py）
3. 在自己的python代码里面直接import AdBuff_pb2即可调用先前定义的message的对象

##Tesla上的使用

* protobuf-3.2.0.zip解压出来的python\google文件夹打包成.zip
* 编译生成的描述.proto文件的.py文件，例如 AdBuff_pb2.py
* 下载six.py文件（运行时出现无法import six的问题）

以上一并上传至tesla的依赖包文件即可。

##出现的问题

* 如果出现在本地无法编译，可以将编译好的protoc文件复制过来，然后再生成对应.proto文件的.py文件即可