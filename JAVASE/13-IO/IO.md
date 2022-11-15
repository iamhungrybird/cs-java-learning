- ## **同步/异步关注的是消息通知的机制，而阻塞/非阻塞关注的是程序（线程）等待消息通知时的状态**。

- ```
  https://mp.weixin.qq.com/s/5iTAaEZUWkqa5ESZ9lPL6Q
  ```


![](images/IO/IO%E6%80%BB%E7%BB%93)

# BIO 面向流：字节流，字符流，转换流和处理流；

## **什么是流** 

知识科普：我们知道任何一个文件都是以二进制形式存在于设备中，计算机就只有 0 和 1，你能看见的东西全部都是由这两个数字组成，你看这篇文章时，这篇文章也是由01组成，只不过这些二进制串经过各种转换演变成一个个文字、一张张图片跃然屏幕上。

而流就是将这些二进制串在各种设备之间进行传输，如果你觉得有些抽象，我举个例子就会好理解一些：

**下图是一张图片，它由01串组成，我们可以通过程序把一张图片拷贝到一个文件夹中，把图片转化成二进制数据集，把数据一点一点地传递到文件夹中 , 类似于水的流动 , 这样整体的数据就是一个数据流。**

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/libYRuvULTdWBF8jpTZAOVApArmTUraFlz7I08lbN1q5iaJMc1LwcncXQ8rQ06pVk7lRdgx49djqCBMCLcq6AZog/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

**IO 流读写数据的特点：**

- **顺序读写****。**读写数据时，大部分情况下都是按照顺序读写，读取时从文件开头的第一个字节到最后一个字节，写出时也是也如此（RandomAccessFile 可以实现随机读写）；
- **字节数组。**读写数据时本质上都是对字节数组做读取和写出操作，即使是字符流，也是在字节流基础上转化为一个个字符，所以字节数组是 IO 流读写数据的本质。

## **流的分类** 



根据数据流向不同分类：输入流 和 输出流。

- **输入流：**从磁盘或者其它设备中将数据输入到进程中；
- **输出流：**将进程中的数据输出到磁盘或其它设备上保存。

![图片](images/IO/640)

图示中的硬盘只是其中一种设备，还有非常多的设备都可以应用在IO流中，例如：打印机、硬盘、显示器、手机······

根据**处理数据的基本单位**不同分类：字节流 和 字符流。

- 字节流：以字节（8 bit）为单位做数据的传输
- 字符流：以字符为单位（1字符 = 2字节）做数据的传输

**字符流的本质也是通过字节流读取，Java 中的字符采用 Unicode 标准，在读取和输出的过程中，通过以字符为单位，查找对应的码表将字节转换为对应的字符。**

面对字节流和字符流，很多读者都有疑惑：什么时候需要用字节流，什么时候又要用字符流？

我这里做一个简单的概括，你可以按照这个标准去使用：

字符流只针对字符数据进行传输，所以如果是文本数据，优先采用字符流传输；除此之外，其它类型的数据（图片、音频等），最好还是以字节流传输。

根据这两种不同的分类，我们就可以做出下面这个表格，里面包含了 IO 中最核心的 4 个顶层抽象类：

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqBtPpRpX8NnHTsdXk92dicrADAeruwulZLLHlPFndBe20f30PpD4ee5A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

现在看 IO 是不是有一些思路了，不会觉得很混乱了，我们来看这四个类下的所有成员。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlNMvibGN9AUaWk9QQAeMpr2lkmINhS3qeR3ZL0sibORwbl3YsJdpu4UpA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1))

[来自于 cxuan 的 《Java基础核心总结》]

看到这么多的类是不是又开始觉得混乱了，不要慌，字节流和字符流下的输入流和输出流大部分都是一一对应的，有了上面的表格支撑，我们不需要再担心看见某个类会懵逼的情况了。

看到 Stream 就知道是字节流，看到 Reader / Writer 就知道是字符流。

这里还要额外补充一点：Java IO 提供了**字节流转换为字符流的转换类**，称为转换流。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5Tq5q911yHXROemUvc4v5ia0prgbic2dGes646MGD5qjd5iaKRDCM7ic1soicA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

注意字节流与字符流之间的转换是有严格定义的：

- 输入流：可以将字节流 => 字符流
- 输出流：可以将字符流 => 字节流

为什么在输入流不能字符流 => 字节流，输出流不能字节流 => 字符流？

在存储设备上，所有数据都是**以字节为单位存储的，所以输入到内存时必定是以字节为单位输入，输出到存储设备时必须是以字节为单位输出**，字节流才是计算机最根本的存储方式，而字符流是在字节流的基础上对数据进行转换，输出字符，但每个字符依旧是以字节为单位存储的。

## **节点流和处理流** 



在这里需要额外插入一个小节讲解节点流和处理流。

- **节点流：**节点流是**真正传输数据**的流对象，用于向特定的一个地方（节点）读写数据，称为节点流。例如 FileInputStream
- **处理流：**处理流是**对节点流的封装**，使用外层的处理流读写数据，本质上是利用节点流的功能，外层的处理流可以提供额外的功能。处理流的基类都是以 Filter 开头。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/libYRuvULTdWBF8jpTZAOVApArmTUraFlmyD34Ay7W8SBPRKD0I3UPVXGIHCL3PInyF31hS8GE7icExNYFp7aibPw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

上图将 ByteArrayInputStream 封装成 DataInputStream，可以将输入的字节数组转换为对应数据类型的数据。例如希望读入int类型数据，就会以2个字节为单位转换为一个数字。

## **Java IO 的核心类 File** 



Java 提供了 File类，它指向计算机操作系统中的文件和目录，通过该类只能访问文件和目录，无法访问内容。它内部主要提供了 3 种操作：

- **访问文件的属性：**绝对路径、相对路径、文件名······
- **文件检测：**是否文件、是否目录、文件是否存在、文件的读/写/执行权限······
- **操作文件：**创建目录、创建文件、删除文件······

上面举例的操作都是在开发中非常常用的，File 类远不止这些操作，更多的操作可以直接去 API 文档中根据需求查找。

访问文件的属性：

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqjL6dnIkn4dkKqRT8H330qUhn42K7hFScBm3jzwSoToMeG8uoGex1sw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

文件检测：

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5Tq5UFutX7oic0VCAWhW43KM0oW2HVRZv70K2YDCg3icEfZXm28LtmBuLqA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

操作文件：

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqL64DlJKWjKRpBbrHtPos1XrhTOoovFEmYYoGiaUAJtjkMPFauibibcqvA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**多了解一些：**

文件的读/写/执行权限，在 Windows 中通常表现不出来，而在 Linux 中可以很好地体现这一点，原因是 Linux 有严格的用户权限分组，不同分组下的用户对文件有不同的操作权限，所以这些方法在 Linux 下会比在 Windows 下更好理解。

下图是 redis 文件夹中的一些文件的详细信息，被红框标注的是不同用户的执行权限：

- r（Read）：代表该文件可以被当前用户读，操作权限的序号是 4
- w（Write）：代表该文件可以被当前用户写，操作权限的序号是 2
- x（Execute）：该文件可以被当前用户执行，操作权限的序号是 1

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlHffB9VOwotrsIhXEjuPPZ7VnmZicEGWYiaXce76V2gXa4vKoxaN7EbLA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

root root 分别代表：**当前文件的所有者，当前文件所属的用户分组。**Linux 下文件的操作权限分为三种用户：

- **文件所有者：**拥有的权限是红框中的前三个字母，-代表没有某个权限；
- **文件所在组的所有用户：**拥有的权限是红框中的中间三个字母；
- **其它组的所有用户：**拥有的权限是红框中的最后三个字母；

## **Java IO 流对象** 



回顾流的分类有2种：

- 根据**数据流向**分为输入流和输出流；
- 根据**数据类型**分为字节流和字符流。

所以，本小节将以字节流和字符流作为主要分割点，在其内部再细分为输入流和输出流进行讲解。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlNMvibGN9AUaWk9QQAeMpr2lkmINhS3qeR3ZL0sibORwbl3YsJdpu4UpA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### **字节流对象**

字节流对象大部分输入流和输出流都是成双成对地出现，所以学习的时候可以将输入流和输出流一一对应的流对象关联起来，输入流和输出流只是数据流向不同，而处理数据的方式可以是相同的。

注意不要认为用什么流读入数据，就需要用对应的流写出数据，在 Java 中没有这么规定，下图只是各个对象之间的一个对应关系，不是两个类使用时必须强制关联使用。

**下面有非常多的类，我会介绍基类的方法，了解这些方法是非常有必要的，子类的功能基于父类去扩展，只有真正了解父类在做什么，学习子类的成本就会下降。**

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlibnhIzOGrMiaT4IptsLzFbyW98W0Qob1fgsmB85hnt7VqvHke3M72gTw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### **InputStream**

InputStream 是字节输入流的抽象基类，提供了通用的读方法，让子类使用或重写它们。下面是 InputStream 常用的重要的方法。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqVnMHVr5zcVHJ1tYrs3PgmP3wcgvawvN0nYgCrOYYibhXZtuAI5PwkEA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

还有其它一些不太常用的方法，我也列出来了。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqoDNP41xWhe1tXG37sj0rY0ibLiavhdEYglplENc01UsBmx2ibSPiabtqLQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](images/IO/640)

**（1）ByteArrayInputStream**

ByteArrayInputStream 内部包含一个 buf 字节数组缓冲区，该缓冲区可以从流中读取的字节数，使用 pos 指针指向读取下一个字节的下标位置，内部还维护了一个count 属性，代表能够读取 count 个字节。

![图片](images/IO/640)

bytearrayinputstream

**必须保证 pos 严格小于 count，而 count 严格小于 buf.length 时，才能够从缓冲区中读取数据。**

**（2）FileInputStream**

文件输入流，从文件中读入字节，通常对文件的拷贝、移动等操作，可以使用该输入流把文件的字节读入内存中，然后再利用输出流输出到指定的位置上。

**（3）PipedInputStream**

管道输入流，它与 PipedOutputStream 成对出现，可以实现多线程中的**管道通信**。PipedOutputStream 中指定与特定的 PipedInputStream 连接，PipedInputStream 也需要指定特定的 PipedOutputStream 连接，之后输出流不断地往输入流的 buffer 缓冲区写数据，而输入流可以从缓冲区中读取数据。

**（4）ObjectInputStream**

对象输入流，用于对象的反序列化，将读入的字节数据反序列化为一个对象，实现对象的持久化存储。

**（5）PushBackInputStream**

它是 FilterInputStream 的子类，是一个处理流，它内部维护了一个缓冲数组buf。

- 在读入字节的过程中可以将**读取到的字节数据回退给缓冲区中保存**，下次可以再次从缓冲区中读出该字节数据。所以**PushBackInputStream 允许多次读取输入流的字节数据**，只要将读到的字节放回缓冲区即可。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlvAYag4d7GZIFq3Yj8Cy2txibmaSSicIsN7ibOwFvU66kKRCIN9iazmib9zQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlHorNZs4bX2DaQ2RRXLVBCAqnZMtL2ApV0rmG70hichXxED2bXZZgWgA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlicIzYzrOCequ5QvfKA8lBFfdCLIm9w8qpcGpg9pyECwpgnNh8gCvPrA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlRDYzwyCLcgpZymTSgx68rbOf5N6UqVI5znlMtjXLRfWmGadLb4GSzg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlZzicxGB9AaEy4pVXgFibmoNLuGgTxF4vY77vASG6C2EdR8lqHZpOFic3A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlCHibFEQtbzlIFJrFpicltOLMcicYCyxnvWh0tw6YhrPLNTaV6fJyYZ1eQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlYFkvkrTNMl58wLEvyHkib5zWIU0dicHoeBcTFf88SKzOpYoEcKzEMUSw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

需要注意的是如果回推字节时，如果缓冲区已满，会抛出 IOException 异常。

它的应用场景：**对数据进行分类规整。**

假如一个文件中存储了数字和字母两种类型的数据，我们需要将它们交给两种线程各自去收集自己负责的数据，如果采用传统的做法，把所有的数据全部读入内存中，再将数据进行分离，面对大文件的情况下，例如1G、2G，传统的输入流在读入数组后，**由于没有缓冲区，只能对数据进行抛弃，这样每个线程都要读一遍文件。**

使用 PushBackInputStream 可以让一个专门的线程读取文件，唤醒不同的线程读取字符：

- 第一次读取缓冲区的数据，判断该数据由哪些线程读取
- 回退数据，唤醒对应的线程读取数据
- 重复前两步
- 关闭输入流

到这里，你是否会想到 AQS 的 Condition 等待队列，多个线程可以在不同的条件上等待被唤醒。

**（6）BufferedInputStream**

缓冲流，它是一种处理流，对节点流进行封装并增强，其内部拥有一个 buffer 缓冲区，用于缓存所有读入的字节，**当缓冲区满时，才会将所有字节发送给客户端读取**，而不是每次都只发送一部分数据，提高了效率。

**（7）DataInputStream**

数据输入流，它同样是一种处理流，对节点流进行封装后，能够在内部对读入的字节转换为对应的 Java 基本数据类型。

**（8）SequenceInputStream**

将两个或多个输入流看作是一个输入流依次读取，该类的存在与否并不影响整个 IO 生态，在程序中也能够做到这种效果。

**（9）StringBufferInputStream**

将字符串中每个字符的低 8 位转换为字节读入到字节数组中，目前已过期。

**InputStream 总结：**

InputStream 是所有输入字节流的抽象基类。

- ByteArrayInputStream 和 FileInputStream 是两种基本的节点流，他们分别从字节数组 和 本地文件中读取数据。
- DataInputStream、BufferedInputStream 和 PushBackInputStream 都是处理流，对基本的节点流进行封装并增强。
- PipiedInputStream 用于多线程通信，可以与其它线程公用一个管道，读取管道中的数据。
- ObjectInputStream 用于对象的反序列化，将对象的字节数据读入内存中，通过该流对象可以将字节数据转换成对应的对象。

#### **OutputStream**

OutputStream 是字节输出流的抽象基类，提供了通用的写方法，让继承的子类重写和复用。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqUpx5sOR5a2ic27G04lVfA0Lqx0fe5gJJlAPOc6Ag8bCw8OFibKm9gpKg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](images/IO/640)

OutputStream 中大多数的类和 InputStream 是对应的，只不过数据的流向不同而已。从上面的图可以看出：

- OutputStream 是所有输出字节流的抽象基类。
- ByteArrayOutputStream 和 FileOutputStream 是两种基本的节点流，它们分别向字节数组和本地文件写出数据。
- DataOutputStream、BufferedOutputStream 是处理流，前者可以将字节数据转换成基本数据类型写出到文件中；后者是缓冲字节数组，只有在缓冲区满时，才会将所有的字节写出到目的地，减少了 IO 次数。
- PipedOutputStream 用于多线程通信，可以和其它线程共用一个管道，向管道中写入数据。
- ObjectOutputStream 用于对象的序列化，将对象转换成字节数组后，将所有的字节都写入到指定位置中。
- PrintStream 在 OutputStream 基础之上提供了增强的功能，即可以方便地输出各种类型的数据（而不仅限于byte型）的格式化表示形式，且 PrintStream 的方法从不抛出 IOEception，其原理是写出时将各个数据类型的数据统一转换为 String 类型，我会在讲解完。

### **字符流对象**

字符流对象也会有对应关系，大多数的类可以认为是操作的数据从字节数组变为字符，类的功能和字节流对象是相似的。

**字符输入流和字节输入流的组成非常相似，字符输入流是对字节输入流的一层转换，所有文件的存储都是字节的存储，在磁盘上保留的不是文件的字符，而是先把字符编码成字节，再保存到文件中。**

**在读取文件时，读入的也是一个一个字节组成的字节序列，而 Java 虚拟机通过将字节序列，按照2个字节为单位转换为 Unicode 字符，实现字节到字符的映射。**

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlwQhgf3hvyYTurLmibgsRGv3lDHq7wVYBMozbpQY5PmFhwhdibZBzOqicQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### **Reader**

Reader 是字符输入流的抽象基类，它内部的重要方法如下所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqElYSD2VRy4fVP0aqLXgd0EbLibDxxJuLmuzDxicPicocgGbEPib3a4gxHQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

还有其它一些额外的方法，与字节输入流基类提供的方法是相同的，只是作用的对象不再是字节，而是字符。

![图片](images/IO/640)

- Reader 是所有字符输入流的抽象基类；
- CharArrayReader 和 StringReader 是两种基本的节点流，它们分别从读取 字符数组 和 字符串 数据，StringReader 内部是一个 String 变量值，通过遍历该变量的字符，实现读取字符串，本质上也是在读取字符数组；
- PipedReader 用于多线程中的通信，从共用地管道中读取字符数据；
- BufferedReader 是字符输入缓冲流，将读入的数据放入字符缓冲区中，实现高效地读取字符；
- InputStreamReader 是一种转换流，可以实现从字节流转换为字符流，将字节数据转换为字符；

#### **Writer**

Reader 是字符输出流的抽象基类，它内部的重要方法如下所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqIj3RuL5QsbKM9wICLWiaCeT0BicTdqnPvLraOoFbDzWufuDpqGk3iaYHg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlE1G3srzSdbKRsiarjlNU4kLib9JjWmDI03DYUbafdTugFjyBOAA86nibw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- Writer 是所有的输出字符流的抽象基类；
- CharArrayWriter、StringWriter 是两种基本的节点流，它们分别向Char 数组、字符串中写入数据。StringWriter 内部保存了 StringBuffer 对象，可以实现字符串的动态增长；
- PipedWriter 可以向共用的管道中写入字符数据，给其它线程读取。
- BufferedWriter 是缓冲输出流，可以将写出的数据缓存起来，缓冲区满时再调用 flush() 写出数据，减少 IO 次数。
- PrintWriter 和 PrintStream 类似，功能和使用也非常相似，只是写出的数据是字符而不是字节。
- OutputStreamWriter 将字符流转换为字节流，将字符写出到指定位置；

### **字节流与字符流的转换** 

编码就是把字符转换为字节，而解码是把字节重新组合成字符。

如果编码和解码过程使用不同的编码方式那么就出现了乱码。

- GBK 编码中，中文字符占 2 个字节，英文字符占 1 个字节；
- UTF-8 编码中，中文字符占 3 个字节，英文字符占 1 个字节；
- UTF-16be 编码中，中文字符和英文字符都占 2 个字节。

UTF-16be 中的 be 指的是 Big Endian，也就是大端。相应地也有 UTF-16le，le 指的是 Little Endian，也就是小端。

Java 使用双字节编码 UTF-16be，这不是指 Java 只支持这一种编码方式，而是说 char 这种类型使用 UTF-16be 进行编码。char 类型占 16 位，也就是两个字节，Java 使用这种双字节编码是为了让一个中文或者一个英文都能使用一个 char 来存储。

![img](images/IO/java-io-1.png)



从任何地方把数据读入到内存都是先以字节流形式读取，即使是使用字符流去读取数据，依然成立，因为数据永远是以字节的形式存在于互联网和硬件设备中，字符流是通过字符集的映射，才能够将字节转换为字符。

所以 Java 提供了两种转换流：

- InputStreamReader：从字节流转换为字符流，将字节数据转换为字符数据读入到内存；
- OutputStreamWriter：从字符流转换为字节流，将字符数据转换为字节数据写出到指定位置。

**了解了 Java 传统的 BIO 中字符流和字节流的主要成员之后，至少要掌握以下两个关键点：**

（1）传统的 BIO 是以流为基本单位处理数据的，想象成水流，一点点地传输字节数据，IO 流传输的过程永远是以字节形式传输。

（2）字节流和字符流的区别在于操作的数据单位不相同，字符流是通过将字节数据通过字符集映射成对应的字符，字符流本质上也是字节流。

接下来我们再继续学习 NIO 知识，NIO 是当下非常火热的一种 IO 工作方式，它能够解决传统 BIO 的痛点：阻塞。

- BIO 如果遇到 IO 阻塞时，线程将会被挂起，直到 IO 完成后才唤醒线程，线程切换带来了额外的开销。
- BIO 中每个 IO 都需要有对应的一个线程去专门处理该次 IO 请求，会让服务器的压力迅速提高。

我们希望做到的是**当线程等待 IO 完成时能够去完成其它事情，当 IO 完成时线程可以回来继续处理 IO 相关操作，不必干干的坐等 IO 完成。**

**在 IO 处理的过程中，能够有一个专门的线程负责监听这些 IO 操作**，通知服务器该如何操作。所以，我们聊到 IO，不得不去接触 NIO 这一块硬骨头。

# NIO 面向缓冲区：Buffer，Channel，Selector

## **新潮的 NIO** 



我们来看看 BIO 和 NIO 的区别，BIO 是面向流的 IO，它建立的通道都是单向的，所以输入和输出流的通道不相同，必须建立2个通道，通道内的都是传输==0101001···==的字节数据。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/libYRuvULTdWBF8jpTZAOVApArmTUraFlHRV8KISVicnok3gCfx9lCVV3YPDqoggQzEmtOXATYHjj627a3C3uqKg/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

而在 NIO 中，不再是面向流的 IO 了，而是面向缓冲区，它会建立一个通道（Channel），该通道我们可以理解为铁路，该铁路上可以运输各种货物，而通道上会有一个**缓冲区（Buffer）用于存储真正的数据，缓冲区我们可以理解为一辆火车。**

**通道（铁路）只是作为运输数据的一个连接资源，而真正存储数据的是缓冲区（火车）。即通道负责传输，缓冲区负责存储。**

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFloqib4Wjhib27MicGRZrk8fFtJhWXa9HO9DoreZ0hCqqicEFt3uia3iaw7nYA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

理解了上面的图之后，BIO 和 NIO 的主要区别就可以用下面这个表格简单概括。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqVsX7ofnCiahotCxw6anKucAcgHhambnibcKx1RcQicApHibtDibCP7Uib7bw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## **缓冲区（Buffer）** 

缓冲区是**存储数据**的区域，在 Java 中，缓冲区就是数组，为了可以操作不同数据类型的数据，Java 提供了许多不同类型的缓冲区，**除了布尔类型以外**，其它基本数据类型都有对应的缓冲区数组对象。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlrv74eR0DOEDic9kJ2KpStichaGdrpH8VKCgtgj1hPYXAIuzibiaicvDNVvg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

为什么没有布尔类型的缓冲区呢？

在 Java 中，boolean 类型数据只占用 1 bit，而在 IO 传输过程中，都是以字节为单位进行传输的，所以 boolean 的 1 bit 完全可以使用 byte 类型的某一位，或者 int 类型的某一位来表示，没有必要为了这 1 bit 而专门提供多一个缓冲区。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5Tq0BLuFp8qF3SZFt7o36w2Ifz1ebgcrhm8icCY6w3EoSz1O9npicgUyLuw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

分配一个缓冲区的方式都高度一致：使用allocate(int capacity)方法。

例如需要分配一个 1024 大小的字节数组，代码就是下面这样子。

```
ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
```

缓冲区读写数据的两个核心方法：

- put()：将数据写入到缓冲区中
- get()：从缓冲区中读取数据

缓冲区的重要属性：

- capacity：缓冲区中最大存储数据的容量，一旦声明则无法改变
- limit：表示缓冲区中可以操作数据的大小，limit 之后的数据无法进行读写。必须满足 limit <= capacity
- position：当前缓冲区中正在操作数据的下标位置，必须满足 position <= limit
- mark：标记位置，调用 reset() 将 position 位置调整到 mark 属性指向的下标位置，实现多次读取数据

缓冲区为高效读写数据而提供的其它辅助方法：

- flip()：可以实现读写模式的切换，我们可以看看里面的源码

```
public final Buffer flip() {    limit = position;    position = 0;    mark = -1;    return this;}
```

调用 flip() 会将可操作的大小 limit 设置为当前写的位置，操作数据的起始位置 position 设置为 0，即从头开始读取数据。

- rewind()：可以将 position 位置设置为 0，再次读取缓冲区中的数据；
- clear()：清空整个缓冲区，它会将 position 设置为 0，limit 设置为 capacity，可以写整个缓冲区；

更多的方法可以去查阅 API 文档，本文碍于篇幅原因就不贴出其它方法了，主要是要理解缓冲区的作用。

我们来看一个简单的例子：

```java
public Class Main {    public static void main(String[] args) {         
    // 分配内存大小为11的整型缓存区        
    IntBuffer buffer = IntBuffer.allocate(11);       
    // 往buffer里写入2个整型数据       
    for (int i = 0; i < 2; ++i) {            int randomNum = new SecureRandom().nextInt();            buffer.put(randomNum);        }     
    
    // 将Buffer从写模式切换到读模式      
    buffer.flip();        System.out.println("position >> " + buffer.position()                           + "limit >> " + buffer.limit()                            + "capacity >> " + buffer.capacity());       
    // 读取buffer里的数据       
    while (buffer.hasRemaining()) {            System.out.println(buffer.get());        }        System.out.println("position >> " + buffer.position()                           + "limit >> " + buffer.limit()                            + "capacity >> " + buffer.capacity());    }}
```

执行结果如下图所示，首先我们往缓冲区中写入 2 个数据，position 在写模式下指向下标 2，然后调用 flip() 方法切换为读模式，limit 指向下标 2，position 从 0 开始读数据，读到下标为 2 时发现到达 limit 位置，不可继续读。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlQlrtsSLzF547CmZqztIkr9dwnz32ibBnhUmdT0B84SMBb87AqdZBeFQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

整个过程可以用下图来理解，调用 flip() 方法以后，读出数据的同时 position 指针不断往后挪动，到达 limit 指针的位置时，该次读取操作结束。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFl7CEVK5Aj4jURosEHrybZjicjS1rseAyE7nT7QBmByO9fcNaYibaybM9Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**介绍完缓冲区后，我们知道它是存储数据的空间，进程可以将缓冲区中的数据读取出来，也可以写入新的数据到缓冲区，那缓冲区的数据从哪里来，又怎么写出去呢？接下来我们需要学习传输数据的介质：通道（Channel）。**



## **通道（Channel）**

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlokmkpG3jiaw6icwUicHj9dUW2vB2FCAkC2Wkh7qYYxsdzyouBfGvyDaEQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

上面我们介绍过，通道是作为一种连接资源，作用是传输数据，而真正存储数据的是缓冲区，所以介绍完缓冲区后，我们来学习通道这一块。

通道是可以**双向读写**的，传统的 BIO 需要使用输入/输出流表示数据的流向，在 NIO 中可以减少通道资源的消耗。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqiaHjxt1ZtC7KrERFOswdGv4icNoHEo0Vmw0afzibn2zcKuZ4XY6sPeFOA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

通道类都保存在 java.nio.channels 包下，我们日常用到的几个重要的类有 4 个：

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqiaHjxt1ZtC7KrERFOswdGv4icNoHEo0Vmw0afzibn2zcKuZ4XY6sPeFOA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

可以通过 getChannel() 方法获取一个通道，支持获取通道的类如下：

- 文件 IO：FileInputStream、FileOutputStream、RandomAccessFile
- TCP 网络 IO：Socket、ServerSocket
- UDP 网络 IO：DatagramSocket

### **示例：文件拷贝案例。**

我们来看一个利用通道拷贝文件的例子，需要下面几个步骤：

- 打开原文件的输入流通道，将字节数据读入到缓冲区中
- 打开目的文件的输出流通道，将缓冲区中的数据写到目的地
- 关闭所有流和通道（重要！）

这是一张小菠萝的照片，它存在于d:\小菠萝\文件夹下，我们将它拷贝到 d:\小菠萝分身\ 文件夹下。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlicYUaMgibmqWTvkSDfoHADACvRtxZuQVIojR8BmeO8FDaTz8etjo4llg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```
public class Test { /** 缓冲区的大小 */    public static final int SIZE = 1024;
    public static void main(String[] args) throws IOException {        // 打开文件输入流        FileChannel inChannel = new FileInputStream("d:\小菠萝\小菠萝.jpg").getChannel();        // 打开文件输出流        FileChannel outChannel = new FileOutputStream("d:\小菠萝分身\小菠萝-拷贝.jpg").getChannel();        // 分配 1024 个字节大小的缓冲区        ByteBuffer dsts = ByteBuffer.allocate(SIZE);        // 将数据从通道读入缓冲区        while (inChannel.read(dsts) != -1) {            // 切换缓冲区的读写模式            dsts.flip();            // 将缓冲区的数据通过通道写到目的地            outChannel.write(dsts);            // 清空缓冲区，准备下一次读            dsts.clear();        }        inChannel.close();        outChannel.close();    }
}
```

我画了一张图帮助你理解上面的这一个过程。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlcVXXtqJVQJCqVTswrbDo06yOUIHnNpCA95FeqJAKAm6uaxsIljJh3A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

有人会问，NIO 的文件拷贝和传统 IO 流的文件拷贝有何不同呢？我们在编程时感觉它们没有什么区别呀，貌似只是 API 不同罢了，我们接下来就去看看这两者之间的区别吧。

### **BIO 和 NIO 拷贝文件的区别**

这个时候就要来了解了解操作系统底层是怎么对 IO 和 NIO 进行区别的，我会用尽量通俗的文字带你理解，可能并不是那么严谨。

操作系统最重要的就是内核，它既可以访问受保护的内存，也可以访问底层硬件设备，所以为了保护内核的安全，操作系统将底层的虚拟空间分为了**用户空间**和**内核空间**，其中用户空间就是给用户进程使用的，内核空间就是专门给操作系统底层去使用的。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFliciaQxuxTk2zQgnVrpibWqRBJoWD5X4TgFjsC5pjeNLDYziaTBeWPFdVCg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

接下来，有一个 Java 进程希望把小菠萝这张图片从磁盘上拷贝，那么内核空间和用户空间都会有一个缓冲区。

- 这张照片就会从磁盘中读出到内核缓冲区中保存，然后操作系统将内核缓冲区中的这张图片字节数据拷贝到用户进程的缓冲区中保存下来，对应着下面这幅图：

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFln7Uj6arEfmqWtwo5ujOplxJawH3icKlDomKw1pPXtDEzaAzDp2EHzZQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- 然后用户进程会希望把缓冲区中的字节数据写到磁盘上的另外一个地方，会将数据拷贝到 Socket 缓冲区中，最终操作系统再将 Socket 缓冲区的数据写到磁盘的指定位置上。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFl6jMGdOZ1MZDicHVf4LiaWFLqibhByb0Z7uiacJrUGTgaPoe2Jg07Xs7aicg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

这一轮操作下来，我们数数经过了几次数据的拷贝？4 次。有 2 次是内核空间和用户空间之间的数据拷贝，这两次拷贝涉及到用户态和内核态的切换，需要CPU参与进来，进行上下文切换。

而另外 2 次是硬盘和内核空间之间的数据拷贝，这个过程利用到 DMA与系统内存交换数据，不需要 CPU 的参与。

导致 IO 性能瓶颈的原因：内核空间与用户空间之间数据过多无意义的拷贝，以及多次上下文切换。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqUicfc5WykROFP7yJNCZcAcGuYNx5XSDMjHHWv7QX5Czfwp1s2XV6sbA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**在用户空间与内核空间之间的操作，会涉及到上下文的切换，这里需要 CPU 的干预，而数据在两个空间之间来回拷贝，也需要 CPU 的干预，这无疑会增大 CPU 的压力，NIO 是如何减轻 CPU 的压力？运用操作系统的零拷贝技术。**

### **操作系统的零拷贝**

所以，操作系统出现了一个全新的概念，解决了 IO 瓶颈：零拷贝。零拷贝指的是**内核空间与用户空间之间的零次拷贝**。

零拷贝可以说是 IO 的一大救星，操作系统底层有许多种零拷贝机制，我这里仅针对 Java NIO 中使用到的其中一种零拷贝机制展开讲解。

在 Java NIO 中，零拷贝是通过**用户空间和内核空间的缓冲区共享一块物理内存**实现的，也就是说上面的图可以演变成这个样子。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlDqwPjvvyF2fCichOaRXZ7ljbd0aIh4N6fAibvoibWJunu5dJIn0TSqbDA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

这时，无论是用户空间还是内核空间操作自己的缓冲区，本质上都是**操作这一块共享内存**中的缓冲区数据，**省去了用户空间和内核空间之间的数据拷贝操作**。

现在我们重新来拷贝文件，就会变成下面这个步骤：

- 用户进程通过系统调用 read() 请求读取文件到用户空间缓冲区（第一次上下文切换），用户态 -> 核心态，数据从硬盘读取到内核空间缓冲区中（第一次数据拷贝）；
- 系统调用返回到用户进程（第二次上下文切换），此时用户空间与内核空间共享这一块内存（缓冲区），所以不需要从内核缓冲区拷贝到用户缓冲区；
- 用户进程发出 write() 系统调用请求写数据到硬盘上（第三次上下文切换），此时需要将内核空间缓冲区中的数据拷贝到内核的 Socket 缓冲区中（第二次数据拷贝）；
- 由 DMA 将 Socket 缓冲区的内容写到硬盘上（第三次数据拷贝），write() 系统调用返回（第四次上下文切换）；

整个过程就如下面这幅图所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlUQClW2bu5OTKura8Uu0ts4gGCicEGMpXXBWOYDYjrlbcJ0uAeuUFQxQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图中，需要 CPU 参与工作的步骤只有第③个步骤，对比于传统的 IO，CPU 需要在用户空间与内核空间之间参与拷贝工作，需要无意义地占用 2 次 CPU 资源，导致 CPU 资源的浪费。

下面总结一下操作系统中零拷贝的优点：

降低 CPU 的压力：避免 CPU 需要参与内核空间与用户空间之间的数据拷贝工作；

减少不必要的拷贝：避免用户空间与内核空间之间需要进行数据拷贝；

上面的图示可能并不严谨，对于你理解零拷贝会有一定的帮助，关于零拷贝的知识点可以去查阅更多资料哦，这是一门大学问。

**介绍完通道后，我们知道它是用于传输数据的一种介质，而且是可以双向读写的，那么如果放在网络 IO 中，这些通道如果有数据就绪时，服务器是如何发现并处理的呢？接下来我们去学习 NIO 中的最后一个重要知识点：选择器（Selector）。**

## **选择器（Selectors）** 

选择器是提升 IO 性能的灵魂之一，它底层利用了多路复用 IO机制，让选择器可以监听多个 IO 连接，根据 IO 的状态响应到服务器端进行处理。通俗地说：**选择器可以监听多个 IO 连接，而传统的 BIO 每个 IO 连接都需要有一个线程去监听和处理。**

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFldMkO2CW8ibtay8LhwqiaIaoeUjyGE05PUfyOrB5fyCbEPGnERukSaiaQA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图中很明显的显示了在 BIO 中，每个 Socket 都需要有一个专门的线程去处理每个请求，而在 NIO 中，只需要一个 Selector 即可监听各个 Socket 请求，而且 Selector 并不是阻塞的，所以**不会因为多个线程之间切换导致上下文切换带来的开销。**

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlveQblVDRjMZ9emMsxtVaOLdFVQtFWDnxE73hwZQpM2gk16nv6rXiaAA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

在 Java NIO 中，选择器是使用 Selector 类表示，Selector 可以接收各种 IO 连接，在 IO 状态准备就绪时，会通知该通道注册的 Selector，Selector 在**下一次轮询**时会发现该 IO 连接就绪，进而处理该连接。

Selector 选择器主要用于网络 IO当中，在这里我会将传统的 BIO Socket 编程和使用 NIO 后的 Socket 编程作对比，分析 NIO 为何更受欢迎。首先先来了解 Selector 的基本结构。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Pn4Sm0RsAugFokPhCtbfE90h7yVtE5TqlRP8AWbcoFdqy4hNRUeOlrBxPz7UgkMFBiahZYeUSy0NxAojfd6OaXQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

在这里，你会看到 select() 和它的重载方法是会阻塞的，如果用户进程轮询时发现没有就绪的通道，操作系统有两种做法：

- 一直等待直到一个就绪的通道，再返回给用户进程
- 立即返回一个错误状态码给用户进程，让用户进程继续运行，不会阻塞

这两种方法对应了同步阻塞 IO 和 同步非阻塞 IO ，这里读者的一点小的观点，请各位大神批判阅读。

**Java 中的 NIO 不能真正意义上称为 Non-Blocking IO，我们通过 API 的调用可以发现，select() 方法还是会存在阻塞的现象，根据传入的参数不同，操作系统的行为也会有所不同，不同之处就是阻塞还是非阻塞，所以我更倾向于把 NIO 称为 New IO，因为它不仅提供了 Non-Blocking IO，而且保留原有的 Blocking IO 的功能。**

了解了选择器之后，它的作用就是：**监听多个 IO 通道，当有通道就绪时选择器会轮询发现该通道，并做相应的处理**。那么 IO 状态分为很多种，我们如何去识别就绪的通道是处于哪种状态呢？在 Java 中提供了选择键（SelectionKey）。

### **选择键（SelectionKey）**

在 Java 中提供了 4 种选择键：

- SelectionKey.OP_READ：套接字通道准备好进行读操作
- SelectionKey.OP_WRITE：套接字通道准备好进行写操作
- SelectionKey.OP_ACCEPT：服务器套接字通道接受其它通道
- SelectionKey.OP_CONNECT：套接字通道准备完成连接

在 SelectionKey 中包含了许多属性：

- channel：该选择键绑定的通道
- selector：轮询到该选择键的选择器
- readyOps：当前就绪选择键的值
- interesOps：该选择器对该通道感兴趣的所有选择键

选择键的作用是：**在选择器轮询到有就绪通道时，会返回这些通道的就绪选择键（SelectionKey），通过选择键可以获取到通道进行操作。**

简单了解了选择器后，我们可以结合缓冲区、通道和选择器来完成一个简易的聊天室应用。

### 示例：简易的客户端服务器通信。

**先说明，这里的代码非常的臭和长，不推荐细看，直接看注释附近的代码即可。**

我们在服务器端会开辟两个线程：

- Thread1：专门监听客户端的连接，并把通道注册到客户端选择器上；
- Thread2：专门监听客户端的其它 IO 状态（读状态），当客户端的 IO 状态就绪时，该选择器会轮询发现，并作相应处理。

```java
public class NIOServer {
    Selector serverSelector = Selector.open();    Selector clientSelector = Selector.open();
    public static void main(String[] args) throws IOException {        NIOServer server = nwe NIOServer();        new Thread(() -> {            try {                // 对应IO编程中服务端启动                
        ServerSocketChannel listenerChannel = ServerSocketChannel.open();                listenerChannel.socket().bind(new InetSocketAddress(3333));                listenerChannel.configureBlocking(false);                listenerChannel.register(serverSelector, SelectionKey.OP_ACCEPT);    server.acceptListener();            } catch (IOException ignored) {            }        }).start();        new Thread(() -> {            try {                server.clientListener();            } catch (IOException ignored) {            }        }).start();    }}// 监听客户端连接
public void acceptListener() {    while (true) {        if (serverSelector.select(1) > 0) {            Set<SelectionKey> set = serverSelector.selectedKeys();            Iterator<SelectionKey> keyIterator = set.iterator();            while (keyIterator.hasNext()) {                SelectionKey key = keyIterator.next();                if (key.isAcceptable()) {                    try {                        // (1) 每来一个新连接，注册到clientSelector                        
    SocketChannel clientChannel = ((ServerSocketChannel) key.channel()).accept();                        clientChannel.configureBlocking(false);                        clientChannel.register(clientSelector, SelectionKey.OP_READ);                    } finally {                        // 从就绪的列表中移除这个key                       
    keyIterator.remove();                    }                }            }        }    }}
// 监听客户端的 IO 状态就绪
public void clientListener() {    while (true) {        
    // 批量轮询是否有哪些连接有数据可读        
    if (clientSelector.select(1) > 0) {            Set<SelectionKey> set = clientSelector.selectedKeys();            Iterator<SelectionKey> keyIterator = set.iterator();            while (keyIterator.hasNext()) {                SelectionKey key = keyIterator.next();    
    // 判断该通道是否读就绪状态                
    if (key.isReadable()) {                    try {                        // 获取客户端通道读入数据                        
        SocketChannel clientChannel = (SocketChannel) key.channel();                        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);                        clientChannel.read(byteBuffer);                        byteBuffer.flip();                        System.out.println(                            LocalDateTime.now().toString() + " Server 端接收到来自 Client 端的消息: " +                            Charset.defaultCharset().decode(byteBuffer).toString());                    } finally {                        
        // 从就绪的列表中移除这个key   
        keyIterator.remove();                        key.interestOps(SelectionKey.OP_READ);                    }                }            }        }    }}
```

在客户端，我们可以简单的输入一些文字，发送给服务器：

```java
public class NIOClient {
    public static final int CAPACITY = 1024;
    public static void main(String[] args) throws Exception {        ByteBuffer dsts = ByteBuffer.allocate(CAPACITY);        SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 3333));        socketChannel.configureBlocking(false);        Scanner sc = new Scanner(System.in);        while (true) {            String msg = sc.next();            dsts.put(msg.getBytes());            dsts.flip();            socketChannel.write(dsts);            dsts.clear();        }    }
}
```

下图可以看见，在客户端给服务器端发送信息，服务器接收到消息后，可以将该条消息分发给其它客户端，就可以实现一个简单的群聊系统，我们还可以给这些客户端贴上标签例如用户姓名，聊天等级······就可以标识每个客户端啦。

在这里由于篇幅原因，我没有写出所有功能，因为使用原生的 NIO 实在是不太便捷。

![图片](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdWBF8jpTZAOVApArmTUraFlfWD3Ec1Bm8YhBvRRcsjRJqRbNXHrOnlicso5RngxEYS0ia6DhU5GJH4Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

我相信你们都是直接滑下来看这里的，我在写这段代码的时候也非常痛苦，甚至有点厌烦 Java 原生的 NIO 编程。

实际上我们在日常开发中很少直接用 NIO 进行编程，通常都会用 Netty，Mina 这种服务器框架，它们都是很好地 NIO 技术，对 Java 原生的 NIO 进行了上层的封装、优化，简化开发难度，但是**在学习框架之前，我们需要了解它底层原生的技术，就像 Spring AOP 的动态代理，Spring IOC 容器的 Map 容器存储对象，Netty 底层的 NIO 基础······**



# IO设计模式

## 装饰器模式  `FilterOutputStream/InputStream` 

**装饰器（Decorator）模式** 可以在不改变原有对象的情况下拓展其功能。

装饰器模式通过组合替代继承来扩展原始类的功能，在一些继承关系比较复杂的场景（IO 这一场景各种类的继承关系就比较复杂）更加实用。

对于字节流来说， `FilterInputStream` （对应输入流）和`FilterOutputStream`（对应输出流）是装饰器模式的核心，分别用于增强 `InputStream` 和`OutputStream`子类对象的功能。

我们常见的`BufferedInputStream`(字节缓冲输入流)、`DataInputStream` 等等都是`FilterInputStream` 的子类，`BufferedOutputStream`（字节缓冲输出流）、`DataOutputStream`等等都是`FilterOutputStream`的子类。

举个例子，我们可以通过 `BufferedInputStream`（字节缓冲输入流）来增强 `FileInputStream` 的功能。

`BufferedInputStream` 构造函数如下：

```java
public BufferedInputStream(InputStream in) {
    this(in, DEFAULT_BUFFER_SIZE);
}

public BufferedInputStream(InputStream in, int size) {
    super(in);
    if (size <= 0) {
        throw new IllegalArgumentException("Buffer size <= 0");
    }
    buf = new byte[size];
}
```

可以看出，`BufferedInputStream` 的构造函数其中的一个参数就是 `InputStream` 。

`BufferedInputStream` 代码示例：



```java
try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream("input.txt"))) {
    int content;
    long skip = bis.skip(2);
    while ((content = bis.read()) != -1) {
        System.out.print((char) content);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

这个时候，你可以会想了：**为啥我们直接不弄一个`BufferedFileInputStream`（字符缓冲文件输入流）呢？**



```java
BufferedFileInputStream bfis = new BufferedFileInputStream("input.txt");
```

如果 `InputStream`的子类比较少的话，这样做是没问题的。不过， `InputStream`的子类实在太多，继承关系也太复杂了。如果我们为每一个子类都定制一个对应的缓冲输入流，那岂不是太麻烦了。

如果你对 IO 流比较熟悉的话，你会发现`ZipInputStream` 和`ZipOutputStream` 还可以分别增强 `BufferedInputStream` 和 `BufferedOutputStream` 的能力。



```java
BufferedInputStream bis = new BufferedInputStream(new FileInputStream(fileName));
ZipInputStream zis = new ZipInputStream(bis);

BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(fileName));
ZipOutputStream zipOut = new ZipOutputStream(bos);
```

`ZipInputStream` 和`ZipOutputStream` 分别继承自`InflaterInputStream` 和`DeflaterOutputStream`。



```java
public
class InflaterInputStream extends FilterInputStream {
}

public
class DeflaterOutputStream extends FilterOutputStream {
}
```

这也是装饰器模式很重要的一个特征，那就是可以对原始类嵌套使用多个装饰器。

为了实现这一效果，装饰器类需要跟原始类继承相同的抽象类或者实现相同的接口。上面介绍到的这些 IO 相关的装饰类和原始类共同的父类是 `InputStream` 和`OutputStream`。

对于字符流来说，`BufferedReader` 可以用来增加 `Reader` （字符输入流）子类的功能，`BufferedWriter` 可以用来增加 `Writer` （字符输出流）子类的功能。



```java
BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(fileName), "UTF-8"));
```

IO 流中的装饰器模式应用的例子实在是太多了，不需要特意记忆，完全没必要哈！搞清了装饰器模式的核心之后，你在使用的时候自然就会知道哪些地方运用到了装饰器模式。

## 适配器模式 `Input/OutputStreamReader` 

**适配器（Adapter Pattern）模式** 主要用于接口互不兼容的类的协调工作，你可以将其联想到我们日常经常使用的电源适配器。

适配器模式中存在被适配的对象或者类称为 **适配者(Adaptee)** ，作用于适配者的对象或者类称为**适配器(Adapter)** 。适配器分为对象适配器和类适配器。类适配器使用继承关系来实现，对象适配器使用组合关系来实现。

IO 流中的字符流和字节流的接口不同，它们之间可以协调工作就是基于适配器模式来做的，更准确点来说是对象适配器。通过适配器，我们可以将字节流对象适配成一个字符流对象，这样我们可以直接通过字节流对象来读取或者写入字符数据。

`InputStreamReader` 和 `OutputStreamWriter` 就是两个适配器(Adapter)， 同时，它们两个也是字节流和字符流之间的桥梁。`InputStreamReader` 使用 `StreamDecoder` （流解码器）对字节进行解码，**实现字节流到字符流的转换，** `OutputStreamWriter` 使用`StreamEncoder`（流编码器）对字符进行编码，实现字符流到字节流的转换。

`InputStream` 和 `OutputStream` 的子类是被适配者， `InputStreamReader` 和 `OutputStreamWriter`是适配器。



```java
// InputStreamReader 是适配器，FileInputStream 是被适配的类
InputStreamReader isr = new InputStreamReader(new FileInputStream(fileName), "UTF-8");
// BufferedReader 增强 InputStreamReader 的功能（装饰器模式）
BufferedReader bufferedReader = new BufferedReader(isr);
```

`java.io.InputStreamReader` 部分源码：



```java
public class InputStreamReader extends Reader {
	//用于解码的对象
	private final StreamDecoder sd;
    public InputStreamReader(InputStream in) {
        super(in);
        try {
            // 获取 StreamDecoder 对象
            sd = StreamDecoder.forInputStreamReader(in, this, (String)null);
        } catch (UnsupportedEncodingException e) {
            throw new Error(e);
        }
    }
    // 使用 StreamDecoder 对象做具体的读取工作
	public int read() throws IOException {
        return sd.read();
    }
}
```

`java.io.OutputStreamWriter` 部分源码：



```java
public class OutputStreamWriter extends Writer {
    // 用于编码的对象
    private final StreamEncoder se;
    public OutputStreamWriter(OutputStream out) {
        super(out);
        try {
           // 获取 StreamEncoder 对象
            se = StreamEncoder.forOutputStreamWriter(out, this, (String)null);
        } catch (UnsupportedEncodingException e) {
            throw new Error(e);
        }
    }
    // 使用 StreamEncoder 对象做具体的写入工作
    public void write(int c) throws IOException {
        se.write(c);
    }
}
```

**适配器模式和装饰器模式有什么区别呢？**

**装饰器模式** 更侧重于动态地增强原始类的功能，装饰器类需要跟原始类继承相同的抽象类或者实现相同的接口。并且，装饰器模式支持对原始类嵌套使用多个装饰器。

**适配器模式** 更侧重于让接口不兼容而不能交互的类可以一起工作，当我们调用适配器对应的方法时，适配器内部会调用适配者类或者和适配类相关的类的方法，这个过程透明的。就比如说 `StreamDecoder` （流解码器）和`StreamEncoder`（流编码器）就是分别基于 `InputStream` 和 `OutputStream` 来获取 `FileChannel`对象并调用对应的 `read` 方法和 `write` 方法进行字节数据的读取和写入。



```java
StreamDecoder(InputStream in, Object lock, CharsetDecoder dec) {
    // 省略大部分代码
    // 根据 InputStream 对象获取 FileChannel 对象
    ch = getChannel((FileInputStream)in);
}
```

适配器和适配者两者不需要继承相同的抽象类或者实现相同的接口。

另外，`FutrueTask` 类使用了适配器模式，`Executors` 的内部类 `RunnableAdapter` 实现属于适配器，用于将 `Runnable` 适配成 `Callable`。

`FutureTask`参数包含 `Runnable` 的一个构造方法：



```java
public FutureTask(Runnable runnable, V result) {
    // 调用 Executors 类的 callable 方法
    this.callable = Executors.callable(runnable, result);
    this.state = NEW;
}
```

`Executors`中对应的方法和适配器：

```java
// 实际调用的是 Executors 的内部类 RunnableAdapter 的构造方法
public static <T> Callable<T> callable(Runnable task, T result) {
    if (task == null)
        throw new NullPointerException();
    return new RunnableAdapter<T>(task, result);
}
// 适配器
static final class RunnableAdapter<T> implements Callable<T> {
    final Runnable task;
    final T result;
    RunnableAdapter(Runnable task, T result) {
        this.task = task;
        this.result = result;
    }
    public T call() {
        task.run();
        return result;
    }
}
```

## 工厂模式

工厂模式用于创建对象，NIO 中大量用到了工厂模式，比如 `Files` 类的 `newInputStream` 方法用于创建 `InputStream` 对象（静态工厂）、 `Paths` 类的 `get` 方法创建 `Path` 对象（静态工厂）、`ZipFileSystem` 类（`sun.nio`包下的类，属于 `java.nio` 相关的一些内部实现）的 `getPath` 的方法创建 `Path` 对象（简单工厂）。

```java
InputStream is Files.newInputStream(Paths.get(generatorLogoPath))
```

## [#](https://javaguide.cn/java/io/io-design-patterns.html#观察者模式)观察者模式 `Watchable` 

NIO 中的文件目录监听服务使用到了观察者模式。

NIO 中的文件目录监听服务基于 `WatchService` 接口和 `Watchable` 接口。`WatchService` 属于观察者，`Watchable` 属于被观察者。

`Watchable` 接口定义了一个用于将对象注册到 `WatchService`（监控服务） 并绑定监听事件的方法 `register` 。



```java
public interface Path
    extends Comparable<Path>, Iterable<Path>, Watchable{
}

public interface Watchable {
    WatchKey register(WatchService watcher,
                      WatchEvent.Kind<?>[] events,
                      WatchEvent.Modifier... modifiers)
        throws IOException;
}
```

`WatchService` 用于监听文件目录的变化，同一个 `WatchService` 对象能够监听多个文件目录。



```java
// 创建 WatchService 对象
WatchService watchService = FileSystems.getDefault().newWatchService();

// 初始化一个被监控文件夹的 Path 类:
Path path = Paths.get("workingDirectory");
// 将这个 path 对象注册到 WatchService（监控服务） 中去
WatchKey watchKey = path.register(
watchService, StandardWatchEventKinds...);
```

`Path` 类 `register` 方法的第二个参数 `events` （需要监听的事件）为可变长参数，也就是说我们可以同时监听多种事件。



```java
WatchKey register(WatchService watcher,
                  WatchEvent.Kind<?>... events)
    throws IOException;
```

常用的监听事件有 3 种：

- `StandardWatchEventKinds.ENTRY_CREATE` ：文件创建。
- `StandardWatchEventKinds.ENTRY_DELETE` : 文件删除。
- `StandardWatchEventKinds.ENTRY_MODIFY` : 文件修改。

`register` 方法返回 `WatchKey` 对象，通过`WatchKey` 对象可以获取事件的具体信息比如文件目录下是创建、删除还是修改了文件、创建、删除或者修改的文件的具体名称是什么。



```java
WatchKey key;
while ((key = watchService.take()) != null) {
    for (WatchEvent<?> event : key.pollEvents()) {
      // 可以调用 WatchEvent 对象的方法做一些事情比如输出事件的具体上下文信息
    }
    key.reset();
}
```

`WatchService` 内部是通过一个 daemon thread（守护线程）采用定期轮询的方式来检测文件的变化，简化后的源码如下所示。



```java
class PollingWatchService
    extends AbstractWatchService
{
    // 定义一个 daemon thread（守护线程）轮询检测文件变化
    private final ScheduledExecutorService scheduledExecutor;

    PollingWatchService() {
        scheduledExecutor = Executors
            .newSingleThreadScheduledExecutor(new ThreadFactory() {
                 @Override
                 public Thread newThread(Runnable r) {
                     Thread t = new Thread(r);
                     t.setDaemon(true);
                     return t;
                 }});
    }

  void enable(Set<? extends WatchEvent.Kind<?>> events, long period) {
    synchronized (this) {
      // 更新监听事件
      this.events = events;

        // 开启定期轮询
      Runnable thunk = new Runnable() { public void run() { poll(); }};
      this.poller = scheduledExecutor
        .scheduleAtFixedRate(thunk, period, period, TimeUnit.SECONDS);
    }
  }
}
```

## [#](https://javaguide.cn/java/io/io-design-patterns.html#参考)参考

- Patterns in Java APIs：http://cecs.wright.edu/~tkprasad/courses/ceg860/paper/node26.html
- 装饰器模式：通过剖析 Java IO 类库源码学习装饰器模式：https://time.geekbang.org/column/article/204845
- sun.nio 包是什么，是 java 代码么？ - RednaxelaFX https://www.zhihu.com/question/29237781/answer/43653953

# IO模型

## **一、****相关概念讲解**



### **1、****同步与异步**



**同步**就是一个任务的完成需要依赖另外一个任务时，只有等待被依赖的任务完成后，依赖的任务才能算完成，这是一种可靠的任务序列。要么成功都成功，失败都失败，两个任务的状态可以保持一致。

**
**

**异步**是不需要等待被依赖的任务完成，只是通知被依赖的任务要完成什么工作，依赖的任务也立即执行，只要自己完成了整个任务就算完成了。至于被依赖的任务最终是否真正完成，依赖它的任务无法确定，所以它是不可靠的任务序列。



## **2、****堵塞与非堵塞**



阻塞和非阻塞这两个概念与程序（线程）**等待消息通知**(无所谓同步或者异步)时的状态有关。也就是说阻塞与非阻塞主要是程序（线程）等待消息通知时的状态角度来说的。

 

**阻塞**调用是指调用结果返回之前，当前线程会被挂起，一直处于等待消息通知，不能够执行其他业务。函数只有在得到结果之后才会返回。

**
**

**非阻塞**和阻塞的概念相对应，指在不能立刻得到结果之前，该函数不会阻塞当前线程，而会立刻返回。虽然表面上看非阻塞的方式可以明显的提高CPU的利用率，但是也带了另外一种后果就是系统的线程切换增加。增加的CPU执行时间能不能补偿系统的切换成本需要好好评估。

 

(a) 如果这个线程在等待当前函数返回时，仍在执行其他消息处理，那这种情况就叫做**同步非阻塞；**



(b) 如果这个线程在等待当前函数返回时，没有执行其他消息处理，而是处于挂起等待状态，那这种情况就叫做**同步阻塞**；

**
**

**同步/异步关注的是消息通知的机制，而阻塞/非阻塞关注的是程序（线程）等待消息通知时的状态**。



## **3、用户空间与内核空间**



现在操作系统都是采用虚拟存储器，那么对32位操作系统而言，它的寻址空间（虚拟存储空间）为4G（2的32次方）。操作系统的核心是内核，独立于普通的应用程序，可以访问受保护的内存空间，也有访问底层硬件设备的所有权限。为了保证用户进程不能直接操作内核（kernel），保证内核的安全，操作系统将虚拟空间划分为两部分，一部分为内核空间，一部分为用户空间。针对linux操作系统而言，**将最高的1G字节（从虚拟地址0xC0000000到0xFFFFFFFF），供内核使用，称为内核空间，而将较低的3G字节（从虚拟地址0x00000000到0xBFFFFFFF），供各个进程使用，称为用户空间。**



## **4、进程切换**



为了控制进程的执行，内核必须有能力挂起正在CPU上运行的进程，并恢复以前挂起的某个进程的执行。这种行为被称为进程切换。因此可以说，任何进程都是在操作系统内核的支持下运行的，是与内核紧密相关的。

从一个进程的运行转到另一个进程上运行，这个过程中经过下面这些变化：



1、保存处理机上下文，包括程序计数器和其他寄存器。

2、更新PCB信息。

3、把进程的PCB移入相应的队列，如就绪、在某事件阻塞等队列。

4、选择另一个进程执行，并更新其PCB。

5、更新内存管理的数据结构。

6、恢复处理机上下文。

注：总而言之就是很耗资源

## **5、****进程的堵塞**



正在执行的进程，由于期待的某些事件未发生，如请求系统资源失败、等待某种操作的完成、新数据尚未到达或无新工作做等，则由系统自动执行阻塞原语(Block)，使自己由运行状态变为阻塞状态。可见，进程的阻塞是进程自身的一种主动行为，也因此只有处于运行态的进程（获得CPU），才可能将其转为阻塞状态。当**进程进入阻塞状态，是不占用CPU资源的**。

## **6、文件描述符**



文件描述符（File descriptor）是计算机科学中的一个术语，是一个用于表述指向文件的引用的抽象化概念。



文件描述符在形式上是一个非负整数。实际上，它是一个**索引值**，**指向内核为每一个进程所维护的该进程打开文件的记录表**。当程序打开一个现有文件或者创建一个新文件时，内核向进程返回一个文件描述符。在程序设计中，一些涉及底层的程序编写往往会围绕着文件描述符展开。但是文件描述符这一概念往往只适用于UNIX、Linux这样的操作系统。

## **7、缓存**



缓存 IO 又被称作标准 IO，大多数文件系统的默认 IO 操作都是缓存 IO。在 Linux 的缓存 IO 机制中，操作系统会将 IO 的数据缓存在文件系统的页缓存（page cache ）中，也就是说，数据会先被拷贝到操作系统内核的缓冲区中，然后才会从操作系统内核的缓冲区拷贝到应用程序的地址空间。



缓存 IO 的缺点：



数据在传输过程中需要在应用程序地址空间和内核进行多次数据拷贝操作，这些数据拷贝操作所带来的 CPU 以及内存开销是非常大的。

## **二、IO模型**



网络IO的本质是socket的读取，socket在linux系统被抽象为流，IO可以理解为对流的操作。刚才说了，对于一次IO访问（以read举例），数据会先被拷贝到操作系统内核的缓冲区中，然后才会从操作系统内核的缓冲区拷贝到应用程序的地址空间。



所以说，当一个read操作发生时，它会经历两个阶段：



第一阶段：等待数据准备 (Waiting for the data to be ready)。

第二阶段：将数据从内核拷贝到进程中 (Copying the data from the kernel to the process)。

 

对于socket流而言，



第一步：通常涉及等待网络上的数据分组到达，然后被复制到内核的某个缓冲区。

第二步：把数据从内核缓冲区复制到应用进程缓冲区。

 

网络应用需要处理的无非就是两大类问题，网络IO，数据计算。相对于后者，网络IO的延迟，给应用带来的性能瓶颈大于后者。



网络IO的模型大致有如下几种：

· 同步模型（synchronous IO）

· 阻塞IO（bloking IO）

· 非阻塞IO（non-blocking IO）

· 多路复用IO（multiplexing IO）

· 信号驱动式IO（signal-driven IO）

· 异步IO（asynchronous IO）**注：由于signal driven IO在实际中并不常用，所以我这只提及剩下的四种IO Model。**

## **1、****堵塞IO模型**



应用程序调用一个 IO 函数，导致应用程序阻塞，等待数据准备好。 如果数据没有准备好，一直等待…数据准备好了，从内核拷贝到用户空间,IO 函数返回成功指示。



当调用 recv()函数时，系统首先查是否有准备好的数据。如果数据没有准备好，那么系统就处于等待状态。当数据准备好后，将数据从系统缓冲区复制到用户空间，然后该函数返回。在套接应用程序中，当调用 recv()函数时，未必用户空间就已经存在数据，那么此时 recv()函数就会处于等待状态。

 

![图片](https://mmbiz.qpic.cn/mmbiz_png/VqTTXZZ8QCp61ZA6pzV9k4ibZetxGicuicRmgz4YQoeFEPBjok90y9y4zH4TuIzQo6SeYvPwMymumqLvJSzkuLRiaw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



## **2、非堵塞IO模型**

 ![图片](https://mmbiz.qpic.cn/mmbiz_png/VqTTXZZ8QCp61ZA6pzV9k4ibZetxGicuicRQ8iaReo0KlkKwQME4BiaskmXRdEvOClCDjkRocibfbMYicR3he94TUvfLA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

我们把一个 SOCKET 接口设置为非阻塞就是告诉内核，当所请求的 I/O 操作无法完成时，不要将进程睡眠，而是返回一个错误。这样我们的 I/O 操作函数将不断的测试数据是否已经准备好，如果没有准备好，继续测试，直到数据准备好为止。在这个不断测试的过程中，会大量的占用 CPU 的时间。上述模型绝不被推荐。



## **3、IO 复用模型**

 ![图片](https://mmbiz.qpic.cn/mmbiz_png/VqTTXZZ8QCp61ZA6pzV9k4ibZetxGicuicRfOWHTX86rjaia8MwCmcxptswsIme9PUYVMrjaAHDhA9f91Hibwjpqw9Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

由于同步非阻塞方式需要不断主动轮询，轮询占据了很大一部分过程，轮询会消耗大量的CPU时间，而 “后台” 可能有多个任务在同时进行，人们就想到了循环查询多个任务的完成状态，只要有任何一个任务完成，就去处理它。如果轮询不是进程的用户态，而是有人帮忙就好了。那么这就是所谓的 **“IO 多路复用”**

 

IO多路复用有两个特别的系统调用select、poll、epoll函数。select调用是内核级别的，select轮询相对非阻塞的轮询的区别在于---前者可以等待多个socket，能实现同时对多个IO端口进行监听，当其中任何一个socket的数据准好了，就能返回进行可读，然后进程再进行recvform系统调用，将数据由内核拷贝到用户进程，当然这个过程是阻塞的。select或poll调用之后，会阻塞进程，与blocking IO阻塞不同在于，此时的select不是等到socket数据全部到达再处理, 而是有了一部分数据就会调用用户进程来处理。如何知道有一部分数据到达了呢？监视的事情交给了内核，内核负责数据到达的处理。也可以理解为"非阻塞"吧。



I/O复用模型会用到select、poll、epoll函数，这几个函数也会使进程阻塞，但是和阻塞I/O所不同的的，这两个函数可以同时阻塞多个I/O操作。而且可以同时对多个读操作，多个写操作的I/O函数进行检测，直到有数据可读或可写时（注意不是全部数据可读或可写），才真正调用I/O操作函数。



对于多路复用，也就是轮询多个socket。多路复用既然可以处理多个IO，也就带来了新的问题，多个IO之间的顺序变得不确定了，当然也可以针对不同的编号。

 

在I/O编程过程中，当需要同时处理多个客户端接入请求时，可以利用多线程或者I/O多路复用技术进行处理。I/O多路复用技术通过把多个I/O的阻塞复用到同一个select的阻塞上，从而使得系统在单线程的情况下可以同时处理多个客户端请求。与传统的多线程/多进程模型比，I/O多路复用的最大优势是系统开销小，系统不需要创建新的额外进程或者线程，也不需要维护这些进程和线程的运行，降底了系统的维护工作量，节省了系统资源，I/O多路复用的主要应用场景如下：



1、服务器需要同时处理多个处于监听状态或者多个连接状态的套接字。

2、服务器需要同时处理多种网络协议的套接字。



此时你是不是想到的了redis如何做的啊，redis用的就是多路复用。

 

## **3、****信号驱动IO**

 ![图片](https://mmbiz.qpic.cn/mmbiz_png/VqTTXZZ8QCp61ZA6pzV9k4ibZetxGicuicR5MwQLHExkCvGjicZe2mN79bibIyYp4J56zjibedjI6ibucNdicTCjw9VkqA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



简介：两次调用，两次返回；



首先我们允许套接口进行信号驱动 I/O,并安装一个信号处理函数，进程继续运行并不阻塞。当数据准备好时，进程会收到一个 SIGIO 信号，可以在信号处理函数中调用 I/O 操作函数处理数据。



## **4、****异步IO模型**

 

相对于同步IO，**异步IO不是顺序执行**。用户进程进行aio_read系统调用之后，无论内核数据是否准备好，都会直接返回给用户进程，然后用户态进程可以去做别的事情。等到socket数据准备好了，内核直接复制数据给进程，然后从内核向进程发送通知。IO两个阶段，进程都是非阻塞的。



Linux提供了AIO库函数实现异步，但是用的很少。目前有很多开源的异步IO库，例如libevent、libev、libuv。

 ![图片](https://mmbiz.qpic.cn/mmbiz_png/VqTTXZZ8QCp61ZA6pzV9k4ibZetxGicuicRtpJibDaCSmUGzgerKlr1vkjyvTBg2WP9iaZzfj6AcvYSSn5WZJ4EFRlA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

 

## **5、****5种I/O模型的比较**

 

不同 I/O 模型的区别，其实主要在**等待数据和数据复制**这两个时间段不同，图形中已经表示得很清楚了。



通过上面的图片，可以发现non-blocking IO和asynchronous IO的区别还是很明显的。在non-blocking IO中，虽然进程大部分时间都不会被block，但是它仍然要求进程去主动的check，并且当数据准备完成以后，也需要进程主动的再次调用recvfrom来将数据拷贝到用户内存。而asynchronous IO则完全不同。它就像是用户进程将整个IO操作交给了他人（kernel）完成，然后他人做完后发信号通知。在此期间，用户进程不需要去检查IO操作的状态，也不需要主动的去拷贝数据。

 ![图片](https://mmbiz.qpic.cn/mmbiz_png/VqTTXZZ8QCp61ZA6pzV9k4ibZetxGicuicRjNPv11rkoiby0H4fwpbhgiaxx37prq0jBpzhticeFEdibqHeKLNpzKq5KQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

 

同步非阻塞方式相比同步阻塞方式：

**
**

**优点：**能够在等待任务完成的时间里干其他活了（包括提交其他任务，也就是 “后台” 可以有多个任务在同时执行）。

**
**

**缺点：**任务完成的响应延迟增大了，因为每过一段时间才去轮询一次read操作，而任务可能在两次轮询之间的任意时间完成。这会导致整体数据吞吐量的降低。



## **三、select 、poll 、epoll的区别？**



1、支持一个进程所能打开的最大连接数

 

![图片](https://mmbiz.qpic.cn/mmbiz_png/VqTTXZZ8QCp61ZA6pzV9k4ibZetxGicuicRtiaN2fclCHBjdm8YCj2seX5kp0GbAJ9FWTVyVJDc8zib6s1MtfkhnuKQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



2、FD （文件描述符）剧增后带来的 IO 效率问题

 ![图片](https://mmbiz.qpic.cn/mmbiz_png/VqTTXZZ8QCp61ZA6pzV9k4ibZetxGicuicRybaruwf8YXRTrKTTbgo0tx5xn7cgiahzibXjFl3ia5eLibiaicWAicPhNxjUQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

3、消息传递方式

 ![图片](https://mmbiz.qpic.cn/mmbiz_png/VqTTXZZ8QCp61ZA6pzV9k4ibZetxGicuicRUibwCmXYRbLTrcKFHC7Uiccf0errjT7sSksF0khbYicl6FKibnvgcSVLjA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



综上，在选择select，poll，epoll 时要根据具体的使用场合以及这三种方式的自身特点。



1、表面上看 epoll 的性能最好，但是在连接数少并且连接都十分活跃的情况下，select 和 poll 的性能可能比 epoll 好，毕竟 epoll 的通知机制需要很多函数回调。

2、select 低效是因为每次它都需要轮询。但低效也是相对的，视情况而定，也可通过良好的设计改善

 

**补充知识点：**



Level_triggered(水平触发)：当被监控的文件描述符上有可读写事件发生时，epoll_wait()会通知处理程序去读写。如果这次没有把数据一次性全部读写完(如读写缓冲区太小)，那么下次调用 epoll_wait()时，它还会通知你在上没读写完的文件描述符上继续读写，当然如果你一直不去读写，它会一直通知你！如果系统中有大量你不需要读写的就绪文件描述符，而它们每次都会返回，这样会大大降低处理程序检索自己关心的就绪文件描述符的效率！



Edge_triggered(边缘触发)：当被监控的文件描述符上有可读写事件发生时，epoll_wait()会通知处理程序去读写。如果这次没有把数据全部读写完(如读写缓冲区太小)，那么下次调用 epoll_wait()时，它不会通知你，也就是它只会通知你一次，直到该文件描述符上出现第二次可读写事件才会通知你！这种模式比水平触发效率高，系统不会充斥大量你不关心的就绪文件描述符！



select()，poll()模型都是水平触发模式，信号驱动IO是边缘触发模式，epoll()模型即支持水平触发，也支持边缘触发，默认是水平触发。



**相关阅读：**

- [未来网络的关键技术](http://mp.weixin.qq.com/s?__biz=MzAxNzU3NjcxOA==&mid=2650730749&idx=2&sn=ddf6236da99373a5749ff77d08dbaf07&chksm=83e9369cb49ebf8afdd3d10a13d81c1d3902bc1e4195aef584dfdd46a09d0391c0746da18841&scene=21#wechat_redirect)
- [架构设计之架构演化、模式与核心要素](http://mp.weixin.qq.com/s?__biz=MzAxNzU3NjcxOA==&mid=2650730712&idx=1&sn=e8782eff74f57271c320d674cab68084&chksm=83e936b9b49ebfaf35847b0ad4883499fb39dff19e094431ee941c859789e8902e30b971d49f&scene=21#wechat_redirect)
- [架构设计，如何业务逻辑和技术分离？](http://mp.weixin.qq.com/s?__biz=MzAxNzU3NjcxOA==&mid=2650730712&idx=2&sn=4c5e421b13d66631f6f683491bf244bf&chksm=83e936b9b49ebfafd599242a7b04d6a4a95f5af16f9f48614b2950c62ef93a76185c1bd73be1&scene=21#wechat_redirect)