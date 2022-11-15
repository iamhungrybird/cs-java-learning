# 解析XML

## XML文档

XML被称为**「可扩展标记语言」**，由SGML语言发展而来。允许用户自定义标签，将标签和内容有效分离。逐渐演变为一种跨平台的数据交换格式（不同平台、、不同系统之间），一种轻量级的持久化方案（无需数据库，保存简单数据）。

XML只是**「纯文本」**而已，是一种独立于软件和硬件的存储和传输工具。可以对外提供信息，但无法提供“动态行为”。

XML中开发者可以*自定义标签，并且更加关注数据的存储和传输*。XML不同于HTML可以在浏览器中解析和显示，它必须要通过自己编写的软件和程序才能够传送，接收和显示这个文档。

### XML的文档结构

示例XML的文档结构

```
<?xml version="1.0" encoding="UTF-8"?>
<!-- XML description-->
<students>
    <student id="001">
        <name>JACK</name>
        <age>18</age>
        <score>98</score>
    </student>
    <student id="002">
        <name>Mike</name>
        <age>19</age>
        <score>99</score>
    </student>
</students>
```

一个标准的XML文档包含了以下内容：![图片](https://mmbiz.qpic.cn/mmbiz_svg/hzVGicX27IG0IPvtFohMP4PMBvoOYwpy32aNqMNKDoqYpoe47f77HlYZXTZgoVZQ9aTFd0gKFYYiaL45y4yZiazcRUpWSYvvJy0/640?wx_fmt=svg&wxfrom=5&wx_lazy=1&wx_co=1)

1. 文档声明

`<?xml version="1.0" encoding="UTF-8"?>`文档总是以XML声明开始，定义了XML的**「版本信息」**和所使用的**「编码信息」**等。

1. 元素

*开始标签、结束标签和内容组合成元素*。元素是XML文档的主要部分，元素内容既可以是**「普通文本」**，也可以是**「子元素」**。一个XML文档有且仅有一个**「根元素」**，如上例的students元素

1. 属性

`<student id="001">`中的id就是属性名，001是属性值。属性值要使用*双引号*括起来。**「属性加载在一个元素的开始标签上」**。一个元素可以有多个属性，可以用*空格*隔开，属性没有先后顺序。同一个XML元素不允许有同名的属性。

1. 注释

`<!-- XML description-->`与HTML的注释一样。对XML文档的内容进行解释说明的文字。

1. CDATA标记、字符实体

有时元素文本中会含有一些特殊的字符“&”，大于小于号等，这些字符在XML文档中已经用到了。此时主要通过两种办法来正确解析这些 字符。

- 个别的特殊字符和用字符实体来进行替换![图片](images/19-%E8%A7%A3%E6%9E%90XML%EF%BC%8CJSON/640)

严格地说在XML文档中仅有字符“<”和“&”是非法的，单引号，双引号和大于号都是合法的。但是把他们替换成实体引用也是个好习惯。

- 大量的特殊符号可以用CDATA标记来处理。

CDATA标记的所有字符都会被当作普通字符来处理，而不是XML标签，

定义CDATA的语法

<！[CDATA[要显示的字符]]>

```
 <score><![CDATA[<90]]></score>
```

#### 格式良好的XML文档：遵循XML文档的基本规则

- 元素的*正确嵌套*
- XML文件的第一行必须是xml文件的声明。
- XML文件只能有*一个根节点*。
- 英文字符的大小写是有差异的。
- 开始的控制标记与结束的控制标记是缺一不可的。
- 属性值的设置必须用“”标记起来。

#### 有效的XML文档

- 首先必须是格式良好的（语法约束）
- 使用DTD和XML Schema定义语义约束

### XML的优势

- 简单性：遵循XML文档的基本规则之的前提下，可以任意*自定义标签*
- 良好的可读性：遵循XML文档的基本规则的前提下，标签名见名知义，具有良好的嵌套关系，带有了*良好的可读性*。
- 可扩展性：根据XML的基本语法来进一步限定使用范围和文档格式，从而定义一种新的语言。
- 可以轻松地*跨平台应用*：XML文档是基于文本的，很容易被人和机器阅读，非常容易使用。便于不同设备和系统之间的信息交换
- *数据内容与显示的分离*：在XML文档中，数据的显示样式已经从文档中分离出来，而放入相关的样式表文件。

### XML文件的作用

- 数据交换

可以使用XML文件来交换数据，如可以通过XML文件来实现Windows和Linux的之间的数据传输。![图片](https://mmbiz.qpic.cn/mmbiz_png/Jkv2NC9FsJEfgKEggiakfZgVMtYiaKz2q5g2ibtnZ3z7HkezQA8VpcjLhvibmFCTZ1Jy3icRLoyKy11K5k8M07Ld2ag/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- 数据配置

使用XML配置文件可读性强，灵活性高，后面的JavaEE等开发会经常用到*XML存储配置信息*。

- 数据存储

XML文件可以做*小型数据库*，用来存储数据。如MSN中保存的聊天记录就是通过XML文件来存储。

## XML语义约束

XML的语义约束主要包括*DTD和XML schema*两种约束。DTD是早期的约束，XML Schema是DTD的替代者，本身也是XML文件。功能更加强大。

### DTD约束

DTD 文档类型定义,*保证XML文档格式正确性*，使用DTD定义了合法的语义约束后，必须让XML文档引入该语义的约束，才会生效。在XML文档中引入DTD 主要有三种方式：

#### 内部DTD

指的是DTD与XML数据存于同一个XML文件中。**「DTD定义在XML声明和XML主体之间」**以结束。定义格式如图所示：![图片](https://mmbiz.qpic.cn/mmbiz_png/Jkv2NC9FsJEfgKEggiakfZgVMtYiaKz2q5VGFviaA4bMS7Wt2yLlDWmbFZ8x10csGHGZMeMIJSlJCow3ym8iciaQqGQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

常见的DTD约束（基于student.xml文件）

![图片](https://mmbiz.qpic.cn/mmbiz_png/Jkv2NC9FsJEfgKEggiakfZgVMtYiaKz2q5JawZHu76CLRibezYYr2OKpsZtib77kADBq4udFGuF6CZe2fRjxWultcQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

注意：

- 子元素*有序使用逗号*：`<!ELEMENT student (name,age,score+)>`

- 子元素*互斥使用竖线*：`<!ELEMENT student (name|age|score)>`

- 子元素无序没有特殊语法，变通解决：`<!ELEMENT student （(name|age|score)+）>`

- 元素类型：*#PCDATA表示字符串类型*，EMPTY表示空内容，ANY表示任意内容。

- 子元素出现的频率：

- 1. ？表示子元素出现0到1次
  2. +表示子元素至少出现一次
  3. *表示子元素可以出现0到多次

- PCDATA：被解析的**「字符串类型」**，在DTD定义中来指定元素类型 CDATA：**「不被解析的字符串类型」**。在DTD定义中指定属性类型。

- ATTLIST：表示只能定义**「一个属性」**，如果一个元素中包含多个属性，则需要多个ATTLIST来定义。

#### 外部DTD

如果存在不同的XML文件使用相同的DTD验证规则，此时可以采用外部DTD，让XML文件**「引入外部DTD」**。

```
<!ELEMENT students (student+)>
<!ELEMENT student (name,age,score+)>
<!ATTLIST student id CDATA #REQUIRED>
<!ELEMENT name (#PCDATA)>
<!ELEMENT age (#PCDATA)>
<!ELEMENT score (#PCDATA)>
```

引入外部DTD的语法是：**「!DOCTYPE 根元素 SYSTEM “外部DTD文件路径"」**

完整的引用路径

```
<?xml version="1.0" encoding="UTF-8"?>
<!-- XML description-->
<!DOCTYPE students SYSTEM "student.dtd">
<students>
    <student id="001">
        <name>JACK</name>
        <age>18</age>
        <score>98</score>
    </student>
    <student id="002">
        <name>Mike</name>
        <age>19</age>
        <score><![CDATA[<90]]></score>
    </student>
</students>
```

#### 公用的DTD

其实也是一种**「外部的DTD」**，主要是某个权威机构制定的并供给特定的行业或公众使用。公用的DTD使用*PUBLIC关键字*引入，并且还要增加一个 标识名。引入语法：**「!DOCTYPE 根元素 PUBLIC “DTD标识名" “公用的DTD的URL"」**

#### 缺点

可以定义XML文档的结构，但是**「无法约束XML内元素的内容。」**

### XML Schema约束

XML Schema使用XML文档来定义语义约束，相比DTD更加复杂，但是功能也更加强大。

它指定丰富的类型，允许开发者自定义数据类型，既可以定义XML文档的结构，还可以对XML文档的内容进行约束。其优势主要体现在：

1. 可读性强：本身就是一个XML文档
2. 支持数据类型：比如日期类型，并且可以限定日期范围。
3. 可扩展：可以导入其他的Schema，自定义数据类型，一个XML文档可以使用多个XML Schema

开源框架中大量使用了XML文档。陆续从以前的DTD约束升级到Schema约束。

#### XMLSchema约束的三种编写方式

1. Russia Roll（俄罗斯玩偶）

```
<?xmlversion="1.0" encoding="UTF-8"?>

<schemaxmlns="http://www.w3.org/2001/XMLSchema"targetNamespace="http://www.example.org/02"

xmlns:tns="http://www.example.org/02"elementFormDefault="qualified">

 

<element name="books">

           <complexType>

           <!-- maxOccurs表示最大出现次数-->

                    <sequencemaxOccurs="unbounded">

                             <elementname="book">

                                       <complexType>

                                                <sequenceminOccurs="1" maxOccurs="unbounded">

                                                         <elementname="title" type="string" />

                                                         <elementname="content" type="string" />

                                                         <choice>

                                                                   <elementname="author" type="string" />

                                                                   <elementname="authors">

                                                                            <complexType>

                                                                                     <all><!--每个元素只能出现一次 -->

                                                                                               <elementname="author" type="string"/>

                                                                                     </all>

                                                                            </complexType>

                                                                   </element>

                                                         </choice>

                                                </sequence>

                                                <attributename="id" type="int" use="required"/>

                                       </complexType>

                             </element>

                    </sequence>

           </complexType>

</element>

 

</schema>
```

**「优点是结构清晰，缺点是类型不能重用」**

1. Salami Slice（腊肉切片）

```
<?xml version="1.0"encoding="UTF-8"?>

<schemaxmlns="http://www.w3.org/2001/XMLSchema"

                   targetNamespace="http://www.example.org/03"

                   xmlns:tns="http://www.example.org/03"

                   elementFormDefault="qualified">

 

         <elementname="book" type="tns:bookType"></element>

         <elementname="id" type="int"/>

         <elementname="title" type="string"/>

         <elementname="content" type="string"/>

        

        

         <complexTypename="bookType">

                   <sequence>

                            <elementref="tns:id"/>

                            <elementref="tns:title"/>

                            <elementref="tns:content"/>

                   </sequence>

         </complexType>

</schema>
```

**「优点是类型最大程度重用，缺点是结构不够清晰，根节点不太好找哦」**

1. Venetian Blind（百叶窗）

```
<?xml version="1.0"encoding="UTF-8"?>

<schemaxmlns="http://www.w3.org/2001/XMLSchema"

                   targetNamespace="http://www.example.org/04"

                   xmlns:tns="http://www.example.org/04"

                   elementFormDefault="qualified">

                  

         <elementname="person" type="tns:personType"/>

        

         <complexTypename="personType">

                   <sequence>

                            <elementname="name" type="string"/>

                            <elementname="age" type="tns:ageType"/>

                            <elementname="email" type="tns:emailType"/>

                   </sequence>

                   <attributename="sex" type="tns:sexType"/>

         </complexType>

        

         <simpleTypename="emailType">

                   <restrictionbase="string">

                            <patternvalue="(\w+\.*)*\w+@\w+\.[A-Za-z]{2,6}"/>

                            <minLengthvalue="6"/>

                            <maxLengthvalue="255"/>

                   </restriction>

         </simpleType>

        

         <simpleTypename="ageType">

                   <restrictionbase="int">

                            <minInclusivevalue="1"/>

                            <maxExclusivevalue="150"/>

                   </restriction>

         </simpleType>

        

         <simpleTypename="sexType">

                   <restrictionbase="string">

                            <enumerationvalue="男"/>

                            <enumerationvalue="女"/>

                   </restriction>

         </simpleType>

</schema>
```

![图片](https://mmbiz.qpic.cn/mmbiz_svg/hzVGicX27IG0IPvtFohMP4PMBvoOYwpy3dsOJffggiaIjFtQ1clicAa81TWx8R4iaGevWkW9WES7DqoOkyvcBBH7BGhXa7V1pDSw/640?wx_fmt=svg&wxfrom=5&wx_lazy=1&wx_co=1)

## DOM解析XML

### XML解析四种方式

*DOM和SAX是两种解析规范*，目前主流的XML解析器都会为DOM和SAX提供实现。但这两种原生的解析方式代码冗长，可读性并不高。因此Java领域又出现两个新的解析器：DOM4J和JDOM，两者很有渊源，也非常相似。DOM4J是*面向接口*编程的，JDOM是*面向实现*编程的。目前DOM4J的性能比JDOM要好，也更加灵活。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Jkv2NC9FsJEfgKEggiakfZgVMtYiaKz2q5qN59X7qywG2gxmbDWlaF6oesmyXfFLTbb64POJDlianfmasnmMtbiaXg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### DOM（Document Object Model）文档对象模型

使用该技术解析文档时，会根据要操作的文档，构建**「一棵要驻留在内存中的树」**。然后使用DOM接口来操作这棵树。由于树驻留在内存中的，所以 操作非常方便。但因为这棵树中包含了所有的XML的内容，所以比较耗资源。该方式适合处理小文档。，适合多次访问的文档解析。

![图片](https://mmbiz.qpic.cn/mmbiz_png/Jkv2NC9FsJEfgKEggiakfZgVMtYiaKz2q5x6w15hv0xqsoT9gcRsFIYElj7LkIWrpL1ibGrBIV6eHPQLRa3JJqAFQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### SAX （Simple API for XML）

其是基于**「事件」**解析的，为了解决DOM耗费资源而产生的。SAX在解析文档时会依次出发文档开始，元素开始，元素结束，文档结束等事件。应用程序通过监听解析过程中触发的事件来获得XML的内容。不需要事先将整个文档调入。占用资源少，消耗小，一般解析大的XML文档时会使用该方法。![图片](https://mmbiz.qpic.cn/mmbiz_png/Jkv2NC9FsJEfgKEggiakfZgVMtYiaKz2q5Kibib9KQMNjcExGxU9JAZ5CJ3pDibMAtjEksDR8KLY4Tia9oibWk1tZ9ibsA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### DOM4J（DOM for Java）

开源的XML解析工具，完全支持DOM和SAX机制，具有性能优异，功能强大，操作简单等特点。越来越多的Java软件开始支持使用DOM4J来处理XML文档。

#### JDOM （Java DOM）

JDOM的目的是为了成为Java的特定文档模型。其与DOM4J具有相同的设计目的，用法也十分类似，JDOM主要以类的API为主，DOM4J主要以接口的 API为主。

总结：Java对DOM和SAX两种规范都提供了支持。Java解析XML文档的叫做JAXP，作为JDK的一部分发布。其中javax.xml.parsers包中提供了四个与DOM和SAX解析相关的类，![图片](https://mmbiz.qpic.cn/mmbiz_png/Jkv2NC9FsJEfgKEggiakfZgVMtYiaKz2q5PwZaZoWsVSWIDsCkENUKHzdRPdPGoBQV5Bnv5VBNtNKRgDaBpAkRow/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- **「如果使用DOM解析，就是用org.w3c.dom包的类和接口。」**
- **「如果使用SAX解析，就是用org.xml.sax包的类和接口。」**

![图片](https://mmbiz.qpic.cn/mmbiz_png/Jkv2NC9FsJEfgKEggiakfZgVMtYiaKz2q52wD6Azib4Ro7RNyafoHF7dd8DzQQZCAwBichnsRV83KwVibX4E32IVbdw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 认识 DOM

DOM将文档解析为一棵树，XML文档的节点对应DOM树的节点，节点之间保持父子、兄弟关系。并且DOM树中 每一个节点都是一个对象。若要解析文档，则需要利用面向对象的思想调用节点的**「属性」**和**「方法」**。可以对该树进行添加修改删除查询操作。最终会转换为对应 XML文档的操作。

Node类是DOM树中节点的**「父类」**。根据具体类型可以划分成多个子类：

- 元素节点类 Element
- 属性节点类 Attr
- 文本节点类 Text .........

另外还有**「注释类Comment，文档类Document」**。其中**「Document」**表示整个XML文档本身，是对整个文档进行操作的*入口*。Document对象中包含一个**「根节点」**。

### 使用DOM来解析XML

使用DOM可以解析XML，同时也可以对XML的内容进行增删改查。

```
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;
import org.xml.sax.SAXException;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.ParserConfigurationException;
import java.io.File;
import java.io.IOException;

/**
 * @author lambda
 */
public class TestDom {
    public static void main(String[] args) throws ParserConfigurationException, IOException, SAXException {
        //创建DOM解析器工厂
       DocumentBuilderFactory dbf=DocumentBuilderFactory.newInstance();
        //由DOM解析器工厂创建DOM解析器
        DocumentBuilder db= dbf.newDocumentBuilder();
        //由DOM解析器解析文档，生成DOM树  /data/home/lambda/idea软件/src/xn/XMLExercises
        Document document=db.parse(new File("/data/home/lambda/idea软件/src/xn/XMLExercises/Student2.xml"));
       // System.out.println(document);

        //解析DOM树,此时的nodeList里面包含注释，约束规范和之后的元素（文档的具体内容）

        /* 第一种方式，利用孩子节点来查找元素
        NodeList nodeList=document.getChildNodes();
        System.out.println(nodeList.getLength());
        //获取具体的内容（依据索引值）
        Element root=(Element)nodeList.item(2);
        System.out.println(root);

*/
        //第二种方式，直接利用标签来获取元素。
        //直接按照标签名字来找,会返回一个节点集合，因为要查找的元素可能不止一个。

        //解析步骤
        /*
        * 1.获取根元素
        * 2.获取根元素students的子元素student
        * 3对每个student进行操作
        * */

        //获取根元素
        NodeList nodeList= document.getElementsByTagName("students");
        //此时在students这个集合内查找下标为0的元素。即根元素
        Element root= (Element) nodeList.item(0);
        System.out.println(root);

        //获取根元素下的子元素student,其中空白部分也算做Text节点，所以节点个数为5（获取孩子节点空白也会计入。需要排除。）
        NodeList stuNodeList = root.getChildNodes();
        //System.out.println(stuNodeList.getLength());
        for (int i=0;i<stuNodeList.getLength();i++){
            Node node=stuNodeList.item(i);
           // System.out.println(node.getNodeType());
            //Node.ELEMENT_NODE=1表示为元素
            if (node.getNodeType()==Node.ELEMENT_NODE){
                //获取每个student的id属性（先将Node类型的节点转换成Element类型）
                Element stuElement=(Element) node;
             String idValue=stuElement.getAttribute("id");
                System.out.println("id----->"+idValue);

                //获取student的孩子元素节点
               NodeList NodeList1 = stuElement.getChildNodes();
               // System.out.println(NodeList1.getLength());
            for (int j=0;j<NodeList1.getLength();j++){
                    Node node1=NodeList1.item(j);
                    if (node1.getNodeType()==Node.ELEMENT_NODE){
                        Element nasElement=(Element) node1;
                        //获取标签内的文本内容 nasElement.getTextContent();
                       // System.out.println(nasElement.getTagName()+"   "+nasElement.getNodeName()+"   "+nasElement.getNodeValue());

                        System.out.println(nasElement.getTagName()+"-------->"+nasElement.getTextContent());
                    }
                 }
            }
        }
    }
}
```

## 使用DOM4J解析XML

### 认识DOM4J

DOM4J是一套开源的XML解析工具，与利用DOM SAX和JAXP相比，表现更优秀，更容易并且性能优越。虽然DOM4J也将文档解析为一棵结构化的树，但是它的处理方式比DOM简单很多。![图片](https://mmbiz.qpic.cn/mmbiz_png/Jkv2NC9FsJEfgKEggiakfZgVMtYiaKz2q5TFDsV5Ad4SaPUaH7kJa11UBM4hdMFYggLfnMlvgMYoOCZgK8VHNrpw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/Jkv2NC9FsJEfgKEggiakfZgVMtYiaKz2q5t439r6fQ6EmiaiaY3A3BZZrpgpqbND7n6bFX5X1gOA1IQYibsfAeCg9ow/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 使用DOM4J解析XML

```
import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;
import org.w3c.dom.Attr;

import java.io.File;
import java.util.Iterator;
import java.util.List;

/**
 * @author lambda
 */
public class TestDom4J1 {

    public static void main(String[] args) throws DocumentException {
        //根据XML文档创建一个DOM4J的树。
        /*reader是一个解析器*/
        SAXReader reader=new SAXReader();
        /*file是一个文件*/
        File file=new File("/data/home/lambda/idea软件/src/xn/XMLExercises/Student2.xml");
        /*我们需要利用reader解析器解析file文件，形成一棵树。*/
        Document doc= reader.read(file);
        //获取DOM4J树的根节点，students
        Element rootElement = doc.getRootElement();
        //获取students的子节点student
        /*此时获取根节点下指定名字的子节点student（此时只有student）*/
           List<Element> stuList= rootElement.elements("student");
           /*利用迭代器遍历stuList*/
        Iterator<Element> iterator=stuList.iterator();
      /*  rootElement.elementIterator("student");上一操作的简便写法*/

        //对student的属性和子节点进行解析。
        while (iterator.hasNext()){
            //获取每个学生student
            Element stuElement=iterator.next();
            //获取学生的属性

            /**
           Attribute attr=stuElement.attribute("id");
            System.out.println(attr.getName()+"----->"+attr.getValue());
             得到单个属性
             */

          Iterator<Attribute> attributeIterator=  stuElement.attributeIterator();
          while (attributeIterator.hasNext()){
              Attribute attribute=attributeIterator.next();
              System.out.println(attribute.getName()+":"+attribute.getValue());
          }
            //获取student的子元素(name age  score等)
            List<Element> nasElement = stuElement.elements();
            for (Element element : nasElement) {
                //方法一
               System.out.println(element.getName()+"----->"+element.getStringValue());
               //方法二
                System.out.println(element.getName()+"----->"+element.getText());
            }
            System.out.println(" ");
        }



    }
}
```

- 技能点1：DOM4J对底层原始的XML文档解析进行**「高度的封装」**。这种封装简化了XML的处理，在DOM4J的包下提供了如下几个类：

1. **「DOMReader」**：根据w3c的DOM树创建DOM4J树
2. **「SAXReader」**：基于SAX解析机制解析一份XML文档，并将其转换为一棵DOM4J树。

- 技能点2：获取属性

1.获取所有属性：List attributes=elem.attributes();

2.获取指定的属性：Attribute attr=elem.attribute("id");

3.attr.getName()+":"+attr.getValue()获取属性名和属性值

- 技能点3：获取元素

1. Element rootElem=doc.getRootElement()获取根元素
2. List stuList=rootElement.elements()获取所有名称的子元素列表

3.List stuList=rootElement.elements("student")获取指定名称的子元素列表

4.String ename=subElem.getName()获取元素名称

5.String etxt=subElem.getText();获取元素文本

### 使用DOM4J完成添加创建操作

#### 创建一个新的XML文档开始

```
/**
 *
 * 创建一个新的XML文档*/

import org.dom4j.Document;
import org.dom4j.DocumentFactory;
import org.dom4j.DocumentHelper;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.XMLWriter;

import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

/**
 * @author lambda
 */
public class TestDom4J2 {
    public static void main(String[] args) throws IOException {
            //1.创建一个DocumentFactory工厂对象
        DocumentFactory factory=new DocumentFactory();

            //2.使用一个DocumentFactory对象创建一个Document对象
        Document doc=factory.createDocument();
            //使用帮助者工具类来创建Document
        Document doc1= DocumentHelper.createDocument();
            //添加一部分注释
        doc.addComment("用DOM4J创建一个XML文档并添加注释");
        //3、给Document增加一个根节点students
           Element rootElement= doc.addElement("Students");
        //4.给students添加一个子节点student
          Element stuElement=  rootElement.addElement("student");
        //5.给student节点添加一个属性节点id
            stuElement.addAttribute("id","001");
        //6给student节点添加子节点，name age score
           Element nameElement= stuElement.addElement("name");
           nameElement.setText("王五");
           Element ageElement=stuElement.addElement("age");
           ageElement.setText("25");
           Element scoreElement=stuElement.addElement("score");
           scoreElement.setText("95");
        //将DOM4J树写入一个文件。
        /*创建一个好看的输出样式,创建一个好看的样式*/
        OutputFormat format=OutputFormat.createPrettyPrint();
        Writer writer=new FileWriter("/data/home/lambda/idea软件/src/xn/XMLExercises/Student3.xml");
        //writer提供一个路径给xmlWriter
        XMLWriter xmlWriter=new XMLWriter(writer,format);
        //xmlwriter调用write将doc的内容写入writer提供的路径的文件当中，
        xmlWriter.write(doc);
        xmlWriter.close();
    }
}
```

- 技能点1：如何创建**「新的XML文档」**

- 1. DocumentFactory：使用了**「工厂模式」**
  2. DocumentHelper:底层还是调用了DocumentFactory

- 技能点2：如何**「添加子元素」**

- 1. Element stuElement=rootElement.addElement("student");
  2. stuElement.addAttribute("id","003");//di属性
  3. Element stuAgeElement=stuElement.addElement("age")age子元素
  4. stuAgeElement.setText("30")

- 技能点3：如何**「写数据到XML文件中」**

- 1. XMLWriter xmlWriter=new XMLWriter(new FileWriter(”filePath“),OutputFormat.createPrettyPrint（）)
  2. createPrettyPrint：精致美观格式，带缩进，有换行，格式美观
  3. createCompactFormat：紧密压缩格式，没有缩进，没有换行。

#### 添加元素到已存在的文件（默认最后一个元素）

```
/*
* *
* 在已经存在的文件中加入元素子节点，即加到最后面*/

import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.SAXReader;
import org.dom4j.io.XMLWriter;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

/**
 * @author lambda
 */
public class TestDOM4J3 {
    public static void main(String[] args) throws DocumentException, IOException {
        //1.根据XML文档创建DOM树
        SAXReader reader=new SAXReader();
        File file=new File("/data/home/lambda/idea软件/src/xn/XMLExercises/Student3.xml");
        Document doc=reader.read(file);

        //2.获取根节点
        Element rootElement=doc.getRootElement();

        //3.给students添加一个子节点student(加到最后)
        Element stuElement =rootElement.addElement("student");
                //给添加的子元素student添加属性
        stuElement.addAttribute("id","002");
        //4.给student添加子元素
        Element nameElement=stuElement.addElement("name");
        nameElement.addText("张三");
        Element ageElement=stuElement.addElement("age");
        ageElement.addText("26");
        Element scoreElement=stuElement.addElement("score");
        scoreElement.addText("94");

        //5.将新建的内存树模型写入硬盘中。
        OutputFormat format=OutputFormat.createPrettyPrint();
        //设置新加的编码：
        format.setEncoding("UTF-8");
        Writer writer=new FileWriter(file);
        XMLWriter xmlWriter=new XMLWriter(writer,format);
        xmlWriter.write(doc);
        xmlWriter.close();
    }
}
```

#### 添加元素至已存在的文件（指定位置,利用集合的方式）

```
/** * 添加一个子节点到指定的位置。 
  * */
import org.dom4j.Document;import org.dom4j.DocumentException;
import org.dom4j.DocumentHelper;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.SAXReader;
import org.dom4j.io.XMLWriter;
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;
import java.util.List;
/** 
* @author lambda
*/
public class TestDOM4J4 {  
  public static void main(String[] args) throws DocumentException, IOException {   
  //1.解析文件为DOM树  
  SAXReader reader=new SAXReader(); 
  File file=new File("/data/home/lambda/idea软件/src/xn/XMLExercises/Student3.xml");  
  Document doc=reader.read(file); 
  //2.获取根节点  
  Element rootElement=doc.getRootElement();  
  //3遍历根节点下所有的student子节点 
  List<Element> stuList=rootElement.elements(); 
  //4.新建一个student子节点。(不用根节点，因为这里只是创建一个新的节点。)           Element stuElement= DocumentHelper.createElement("student");  
  //为student新建结点添加属性值   
  stuElement.addAttribute("id","003");  
  Element nameElement =stuElement.addElement("name");  
  nameElement.addText("李白");  
  Element ageElement=stuElement.addElement("age");  
  ageElement.addText("999");  
  Element scoreElement=stuElement.addElement("score"); 
  scoreElement.addText("9999"); 
  //5.将新建立的节点添加到指定的位置（采用集合的方式来添加） 
  stuList.add(1,stuElement);   
  //6将添加好的内存树写入xml文件中  
  OutputFormat format=OutputFormat.createPrettyPrint(); 
  format.setEncoding("utf_8"); 
  Writer writer=new FileWriter("/data/home/lambda/idea软件/src/xn/XMLExercises/Student3.xml");  
  XMLWriter xmlWriter=new XMLWriter(writer,format);  
  xmlWriter.write(doc);  
  xmlWriter.close(); 
  }
}
```

技能点：**「添加元素到指定的位置」**

1. List lists=rootElement.elements("student");
2. Element stuElement=DocumentHelper.createElement("student");
3. lists.add(1,stuElement);

### 使用DOM4J完成对元素删除修改操作

#### 使用DOM4J删除指定元素

```
/**
删除指定XML文档的指定节点 
*
*/
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.SAXReader;
import org.dom4j.io.XMLWriter;
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;
import java.util.List;
/** 
* @author lambda 
*/
public class TestDOM4J5 {   
  public static void main(String[] args) throws DocumentException, IOException{    
  //1.首先根据XML文档创建DOM树 
  SAXReader reader=new SAXReader();   
  File file=new File("/data/home/lambda/idea软件/src/xn/XMLExercises/Student3.xml"); 
  Document doc=reader.read(file);
  //2.获取根节点  
  Element rootElement=doc.getRootElement();  
  //3.获取根节点下所有子节点 
  List<Element> stuList = rootElement.elements(); 
  //4.删除指定的节点(若知道索引，remove方法即可)  
  //stuList.remove(1);  
  //若不知道索引，只知道id ,需要对整个集合进行遍历   
  for (int i = 0; i < stuList.size(); i++) {    
      Element stuElement=stuList.get(i);    
      //获得指定删除节点的id值     
      String value=stuElement.attribute("id").getValue(); 
             if ("003".equals(value)){    
                  stuList.remove(stuElement);  
              //通过父亲节点来删指定元素   
              //rootElement.remove(stuElement);  
            }   
        }    
 //5.将DOM树写入xml中 
  OutputFormat format=OutputFormat.createPrettyPrint();  
 // format.setEncoding("utf-8");
//写入到指定的文件中  
Writer writer=new FileWriter("/data/home/lambda/idea软件/src/xn/XMLExercises/Student3.xml"); 
XMLWriter xmlWriter=new XMLWriter(writer,format);  
xmlWriter.write(doc);  
xmlWriter.close();
    }
}
```

#### 使用DOM4J完成**「修改指定元素」**

```java
/*
*修改id为003的节点 
*
*/
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.SAXReader;
import org.dom4j.io.XMLWriter;
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;
import java.util.List;
/**
* @author lambda 
*/
public class TestDOM4J6 {   
      public static void main(String[] args) throws DocumentException, IOException { 
      //1.首先根据XML文档创建DOM树 
      SAXReader reader=new SAXReader();
      File file=new File("/data/home/lambda/idea软件/src/xn/XMLExercises/Student3.xml"); 
      Document doc=reader.read(file); 
      //2.获取根节点      
      Element rootElement=doc.getRootElement(); 
      //3.获取根节点下所有子节点      
      List<Element> stuList = rootElement.elements();  
      for (int i = 0; i < stuList.size(); i++) { 
            Element stuElement=stuList.get(i);  
            //获得指定节点的id值  
            String value=stuElement.attribute("id").getValue();                     //获得stuElement的下一个元素       
            Element nameElement= stuElement.element("name");                        if ("003".equals(value)){    
                    //修改stuElement的name    
                  nameElement.setText("小明");  
                stuElement.attribute("id").setText("101");   
                        break;          
                  }      
          }   
     //5.将DOM树写入xml中   
       OutputFormat format=OutputFormat.createPrettyPrint();    
      // format.setEncoding("utf-8");  
      //写入到指定的文件中  
      Writer writer=new FileWriter("/data/home/lambda/idea软件/src/xn/XMLExercises/Student3.xml");  
      XMLWriter xmlWriter=new XMLWriter(writer,format); 
    xmlWriter.write(doc);  
    xmlWriter.close(); 
          }
}
```

# 解析JSON

## 概述

JSON(JavaScript Object Notation JavaScript 对象表示法)是一种轻量级的数据交换格式，它基于JavaScript的一个子集，易于人的编写和阅读，也易于机器解析。JSON采用完全独立于语言的文本格式，但是也使用了类似于C语言家族的习惯（包括C, C++, C#, Java, JavaScript, Perl, Python等）。这些特性使JSON成为理想的数据交换语言。

## 结构组成

JSON由两种结构组成：

1. 键值对的无序集合——对象(或者叫记录、结构、字典、哈希表、有键列表或关联数组等)
2. 值的有序列表——数组

这些都是常见的数据结构。事实上大部分现代计算机语言都以某种形式支持它们。这使得一种数据格式在同样基于这些结构的编程语言之间交换成为可能。

## 语法规则

- 数据在名称/值对中
- 数据由逗号分隔
- 大括号保存对象
- 中括号保存数组

## JSON的形式

### 对象

一个无序键值对的集合，以"{“开始，同时以”}“结束，键值对之间以”:“相隔，不同的键值对之间以”,"相隔，举例

```
{    "key1" : 1,    "key2" : "string"}
```



![图片](https://mmbiz.qpic.cn/mmbiz_png/uISTiacTMXkQ0rs0TYXDcTnjFNLicwC4SNQTNzbWuwj7ZY6uE2OYbfXS5X6Eia9ibXVTiaJtKmupqbqfFAWbJmsMfWw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 数组

值（value）的有序集合。一个数组以“[”（左中括号）开始，“]”（右中括号）结束。值之间使用“,”（逗号）分隔。

举例

```
[ "Google", "Runoob", "Taobao" ]
```



![图片](https://mmbiz.qpic.cn/mmbiz_png/uISTiacTMXkQ0rs0TYXDcTnjFNLicwC4SNMPqYfutcKEibKx7Scj3bwnHg1v6p8hZ7DVo58ANccnFbKcwYPQfX7JA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

值（value）可以是双引号括起来的字符串（string）、数值(number)、true、false、 null、对象（object）或者数组（array）。这些结构可以嵌套。

![图片](https://mmbiz.qpic.cn/mmbiz_png/uISTiacTMXkQ0rs0TYXDcTnjFNLicwC4SNP9mQgsBlsibqEzuXFT4VWrl8zeAOCC9LmCDuW61lqKK7R6l3xQZh9uQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

字符串（string）是由双引号包围的任意数量Unicode字符的集合，使用反斜线转义。一个字符（character）即一个单独的字符串（character string）。

字符串（string）与C或者Java的字符串非常相似。

![图片](https://mmbiz.qpic.cn/mmbiz_png/uISTiacTMXkQ0rs0TYXDcTnjFNLicwC4SNk4NPcLvz0bXlicekC2ic8gD6RkUibjVJrRVhkfWsJuG2iadeXEKAWCCOjQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

数值（number）也与C或者Java的数值非常相似。除去未曾使用的八进制与十六进制格式。除去一些编码细节。

![图片](https://mmbiz.qpic.cn/mmbiz_png/uISTiacTMXkQ0rs0TYXDcTnjFNLicwC4SNgU5LiaDLrmyicmPFJ7R2rQ4c4VNiafjONKOIGmvhe0KTzcMNHeKlVMUEg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

空白可以加入到任何符号之间。

这里举例一个比较复杂的json

```
{    "name":"网站",    "money":3.22,    "status":0,    "valid":true,    "address":null,    "sites": [        { "name":"Google", "info":[ "Android", "Google 搜索", "Google 翻译" ] },        { "name":"Runoob", "info":[ "菜鸟教程", "菜鸟工具", "菜鸟微信" ] },        { "name":"Taobao", "info":[ "淘宝", "网购" ] }    ]}
```



具体参见JSON官网http://www.json.org/json-zh.html

很多语言都提供了JSON的解析库，可以参考：

**Java几种常用JSON库性能比较**

JSON不管是在Web开发还是服务器开发中是相当常见的数据传输格式，一般情况我们对于JSON解析构造的性能并不需要过于关心，除非是在性能要求比较高的系统。

目前对于Java开源的JSON类库有很多种，下面我们取4个常用的JSON库进行性能测试对比， 同时根据测试结果分析如果根据实际应用场景选择最合适的JSON库。

这4个JSON类库分别为：Gson，FastJson，Jackson，Json-lib。

## 简单介绍

选择一个合适的JSON库要从多个方面进行考虑：

- 字符串解析成JSON性能
- 字符串解析成JavaBean性能
- JavaBean构造JSON性能
- 集合构造JSON性能
- 易用性

先简单介绍下四个类库的身份背景。

### Gson

> 项目地址：https://github.com/google/gson

Gson是目前功能最全的Json解析神器，Gson当初是为因应Google公司内部需求而由Google自行研发而来，但自从在2008年五月公开发布第一版后已被许多公司或用户应用。Gson的应用主要为toJson与fromJson两个转换函数，无依赖，不需要例外额外的jar，能够直接跑在JDK上。在使用这种对象转换之前，需先创建好对象的类型以及其成员才能成功的将JSON字符串成功转换成相对应的对象。类里面只要有get和set方法，Gson完全可以实现复杂类型的json到bean或bean到json的转换，是JSON解析的神器。

### FastJson

> 项目地址：https://github.com/alibaba/fastjson

Fastjson是一个Java语言编写的高性能的JSON处理器,由阿里巴巴公司开发。无依赖，不需要例外额外的jar，能够直接跑在JDK上。FastJson在复杂类型的Bean转换Json上会出现一些问题，可能会出现引用的类型，导致Json转换出错，需要制定引用。FastJson采用独创的算法，将parse的速度提升到极致，超过所有json库。

### Jackson

> 项目地址：https://github.com/FasterXML/jackson

Jackson是当前用的比较广泛的，用来序列化和反序列化json的Java开源框架。Jackson社区相对比较活跃，更新速度也比较快， 从Github中的统计来看，Jackson是最流行的json解析器之一，Spring MVC的默认json解析器便是Jackson。

Jackson优点很多：

- Jackson 所依赖的jar包较少，简单易用。
- 与其他 Java 的 json 的框架 Gson 等相比，Jackson 解析大的 json 文件速度比较快。
- Jackson 运行时占用内存比较低，性能比较好
- Jackson 有灵活的 API，可以很容易进行扩展和定制。

目前最新版本是2.9.4，Jackson 的核心模块由三部分组成：

- jackson-core 核心包，提供基于”流模式”解析的相关 API，它包括 JsonPaser 和 JsonGenerator。Jackson 内部实现正是通过高性能的流模式 API 的 JsonGenerator 和 JsonParser 来生成和解析 json。
- jackson-annotations 注解包，提供标准注解功能；
- jackson-databind 数据绑定包，提供基于”对象绑定” 解析的相关 API（ ObjectMapper ）和”树模型” 解析的相关 API（JsonNode）；基于”对象绑定” 解析的 API 和”树模型”解析的 API 依赖基于”流模式”解析的 API。

### Json-lib

> 项目地址：http://json-lib.sourceforge.net/index.html

json-lib最开始的也是应用最广泛的json解析工具，json-lib 不好的地方确实是依赖于很多第三方包，对于复杂类型的转换，json-lib对于json转换成bean还有缺陷， 比如一个类里面会出现另一个类的list或者map集合，json-lib从json到bean的转换就会出现问题。json-lib在功能和性能上面都不能满足现在互联网化的需求。

### 编写性能测试

接下来开始编写这四个库的性能测试代码。

### 添加maven依赖

当然首先是添加四个库的maven依赖，公平起见，我全部使用它们最新的版本：

```
<!-- json-lib --><dependency>    <groupId>net.sf.json-lib</groupId>    <artifactId>json-lib</artifactId>    <version>2.4</version>    <classifier>jdk15</classifier></dependency>
<!-- gson --><dependency>    <groupId>com.google.code.gson</groupId>    <artifactId>gson</artifactId>    <version>2.8.5</version></dependency>
<!-- fastjson --><dependency>    <groupId>com.alibaba</groupId>    <artifactId>fastjson</artifactId>    <version>1.2.58</version></dependency>
<!-- jackson --><dependency>    <groupId>com.fasterxml.jackson.core</groupId>    <artifactId>jackson-databind</artifactId>    <version>2.9.9</version></dependency><dependency>    <groupId>com.fasterxml.jackson.core</groupId>    <artifactId>jackson-annotations</artifactId>    <version>2.9.9</version></dependency>
```



### 四个库的工具类

**FastJsonUtil.java**

```
public class FastJsonUtils {    private static final SerializerFeature[] features = {        // 序列化所有参数，包括null        SerializerFeature.WriteMapNullValue,        // 日期类型格式        SerializerFeature.WriteDateUseDateFormat        // list字段如果为null，输出为[]，而不是null        // SerializerFeature.WriteNullListAsEmpty,        // 数值字段如果为null，输出为0，而不是null        // SerializerFeature.WriteNullNumberAsZero,        // Boolean字段如果为null，输出为false，而不是null        // SerializerFeature.WriteNullBooleanAsFalse,        // 字符类型字段如果为null，输出为""，而不是null        // SerializerFeature.WriteNullStringAsEmpty    };
    public static String bean2Json(Object obj) {        return JSON.toJSONString(obj);    }
    public static String bean2JsonFeatures(Object obj) {        return JSON.toJSONString(obj, features);    }
    public static <T> T json2Bean(String jsonStr, Class<T> objClass) {        return JSON.parseObject(jsonStr, objClass);    }}
```



**GsonUtil.java**

```
public class GsonUtils {    private static Gson gson = new GsonBuilder().create();
    public static String bean2Json(Object obj) {        return gson.toJson(obj);    }
    public static <T> T json2Bean(String jsonStr, Class<T> objClass) {        return gson.fromJson(jsonStr, objClass);    }
    public static String jsonFormatter(String uglyJsonStr) {        Gson gson = new GsonBuilder().setPrettyPrinting().create();        JsonParser jp = new JsonParser();        JsonElement je = jp.parse(uglyJsonStr);        return gson.toJson(je);    }}
```



JacksonUtil.java

SpringBoot中Jackson可以使用properties配置

```
#日期类型格式spring.jackson.date-format=yyyy-MM-dd HH:mm:ss#日期类型使用中国时区spring.jackson.time-zone=GMT+8#序列化所有参数spring.jackson.default-property-inclusion=always
```

```java
public class JacksonUtils {    private static ObjectMapper mapper = new ObjectMapper();
    static {        // 设置时区
        objectMapper.setTimeZone(TimeZone.getTimeZone("GMT+8"));        // 日期类型格式
        objectMapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss")); 
        // 序列化所有参数，包括null 
        objectMapper.setSerializationInclusion(JsonInclude.Include.ALWAYS);    }
                           
    public static String bean2Json(Object obj) {        try {            return mapper.writeValueAsString(obj);        } catch (JsonProcessingException e) {            e.printStackTrace();            return null;        }    }
    public static <T> T json2Bean(String jsonStr, Class<T> objClass) {        try {            return mapper.readValue(jsonStr, objClass);        } catch (IOException e) {            e.printStackTrace();            return null;        }    }}
```



**JsonLibUtil.java**

```
public class JsonLibUtils {
    public static String bean2Json(Object obj) {        JSONObject jsonObject = JSONObject.fromObject(obj);        return jsonObject.toString();    }
    @SuppressWarnings("unchecked")    public static <T> T json2Bean(String jsonStr, Class<T> objClass) {        return (T) JSONObject.toBean(JSONObject.fromObject(jsonStr), objClass);    }}
```



**准备Model类**

这里我写一个简单的Person类，同时属性有Date、List、Map和自定义的类FullName，最大程度模拟真实场景。

```
public class Person {    private String name;    private FullName fullName;    private int age;    private Date birthday;    private List<String> hobbies;    private Map<String, String> clothes;    private List<Person> friends;
    // getter/setter省略
    @Override    public String toString() {        StringBuilder str = new StringBuilder("Person [name=" + name + ", fullName=" + fullName + ", age="                + age + ", birthday=" + birthday + ", hobbies=" + hobbies                + ", clothes=" + clothes + "]\n");        if (friends != null) {            str.append("Friends:\n");            for (Person f : friends) {                str.append("\t").append(f);            }        }        return str.toString();    }}
```

```
public class FullName {    private String firstName;    private String middleName;    private String lastName;
    public FullName() {    }
    public FullName(String firstName, String middleName, String lastName) {        this.firstName = firstName;        this.middleName = middleName;        this.lastName = lastName;    }
    // 省略getter和setter
    @Override    public String toString() {        return "[firstName=" + firstName + ", middleName="                + middleName + ", lastName=" + lastName + "]";    }}
```

### JSON序列化性能基准测试

```
@BenchmarkMode(Mode.SingleShotTime)@OutputTimeUnit(TimeUnit.SECONDS)@State(Scope.Benchmark)public class JsonSerializeBenchmark {    /**     * 序列化次数参数     */    @Param({"1000", "10000", "100000"})    private int count;
    private Person p;
    public static void main(String[] args) throws Exception {        Options opt = new OptionsBuilder()                .include(JsonSerializeBenchmark.class.getSimpleName())                .forks(1)                .warmupIterations(0)                .build();        Collection<RunResult> results =  new Runner(opt).run();        ResultExporter.exportResult("JSON序列化性能", results, "count", "秒");    }
    @Benchmark    public void JsonLib() {        for (int i = 0; i < count; i++) {            JsonLibUtils.bean2Json(p);        }    }
    @Benchmark    public void Gson() {        for (int i = 0; i < count; i++) {            GsonUtils.bean2Json(p);        }    }
    @Benchmark    public void FastJson() {        for (int i = 0; i < count; i++) {            FastJsonUtils.bean2Json(p);        }    }
    @Benchmark    public void Jackson() {        for (int i = 0; i < count; i++) {            JacksonUtils.bean2Json(p);        }    }
    @Setup    public void prepare() {        List<Person> friends=new ArrayList<Person>();        friends.add(createAPerson("小明",null));        friends.add(createAPerson("Tony",null));        friends.add(createAPerson("陈小二",null));        p=createAPerson("邵同学",friends);    }
    @TearDown    public void shutdown() {    }
    private Person createAPerson(String name,List<Person> friends) {        Person newPerson=new Person();        newPerson.setName(name);        newPerson.setFullName(new FullName("zjj_first", "zjj_middle", "zjj_last"));        newPerson.setAge(24);        List<String> hobbies=new ArrayList<String>();        hobbies.add("篮球");        hobbies.add("游泳");        hobbies.add("coding");        newPerson.setHobbies(hobbies);        Map<String,String> clothes=new HashMap<String, String>();        clothes.put("coat", "Nike");        clothes.put("trousers", "adidas");        clothes.put("shoes", "安踏");        newPerson.setClothes(clothes);        newPerson.setFriends(friends);        return newPerson;    }}
```



说明一下，上面的代码中

```
ResultExporter.exportResult("JSON序列化性能", results, "count", "秒");
```

这个是我自己编写的将性能测试报告数据填充至Echarts图，然后导出png图片的方法。

执行后的结果图：

![图片](https://mmbiz.qpic.cn/mmbiz_png/uISTiacTMXkQ0rs0TYXDcTnjFNLicwC4SNiapiaicsphSEYCyZerL3Uc6nGawjhStdzSWbHWTZ2qz0nrfNz0hnFlricg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

从上面的测试结果可以看出，序列化次数比较小的时候，Gson性能最好，当不断增加的时候到了100000，Gson明细弱于Jackson和FastJson， 这时候FastJson性能是真的牛，另外还可以看到不管数量少还是多，Jackson一直表现优异。而那个Json-lib简直就是来搞笑的。^_^

### JSON反序列化性能基准测试

```
@BenchmarkMode(Mode.SingleShotTime)@OutputTimeUnit(TimeUnit.SECONDS)@State(Scope.Benchmark)public class JsonDeserializeBenchmark {    /**     * 反序列化次数参数     */    @Param({"1000", "10000", "100000"})    private int count;
    private String jsonStr;
    public static void main(String[] args) throws Exception {        Options opt = new OptionsBuilder()                .include(JsonDeserializeBenchmark.class.getSimpleName())                .forks(1)                .warmupIterations(0)                .build();        Collection<RunResult> results =  new Runner(opt).run();        ResultExporter.exportResult("JSON反序列化性能", results, "count", "秒");    }
    @Benchmark    public void JsonLib() {        for (int i = 0; i < count; i++) {            JsonLibUtils.json2Bean(jsonStr, Person.class);        }    }
    @Benchmark    public void Gson() {        for (int i = 0; i < count; i++) {            GsonUtils.json2Bean(jsonStr, Person.class);        }    }
    @Benchmark    public void FastJson() {        for (int i = 0; i < count; i++) {            FastJsonUtils.json2Bean(jsonStr, Person.class);        }    }
    @Benchmark    public void Jackson() {        for (int i = 0; i < count; i++) {            JacksonUtils.json2Bean(jsonStr, Person.class);        }    }
    @Setup    public void prepare() {        jsonStr="{\"name\":\"邵同学\",\"fullName\":{\"firstName\":\"zjj_first\",\"middleName\":\"zjj_middle\",\"lastName\":\"zjj_last\"},\"age\":24,\"birthday\":null,\"hobbies\":[\"篮球\",\"游泳\",\"coding\"],\"clothes\":{\"shoes\":\"安踏\",\"trousers\":\"adidas\",\"coat\":\"Nike\"},\"friends\":[{\"name\":\"小明\",\"fullName\":{\"firstName\":\"xxx_first\",\"middleName\":\"xxx_middle\",\"lastName\":\"xxx_last\"},\"age\":24,\"birthday\":null,\"hobbies\":[\"篮球\",\"游泳\",\"coding\"],\"clothes\":{\"shoes\":\"安踏\",\"trousers\":\"adidas\",\"coat\":\"Nike\"},\"friends\":null},{\"name\":\"Tony\",\"fullName\":{\"firstName\":\"xxx_first\",\"middleName\":\"xxx_middle\",\"lastName\":\"xxx_last\"},\"age\":24,\"birthday\":null,\"hobbies\":[\"篮球\",\"游泳\",\"coding\"],\"clothes\":{\"shoes\":\"安踏\",\"trousers\":\"adidas\",\"coat\":\"Nike\"},\"friends\":null},{\"name\":\"陈小二\",\"fullName\":{\"firstName\":\"xxx_first\",\"middleName\":\"xxx_middle\",\"lastName\":\"xxx_last\"},\"age\":24,\"birthday\":null,\"hobbies\":[\"篮球\",\"游泳\",\"coding\"],\"clothes\":{\"shoes\":\"安踏\",\"trousers\":\"adidas\",\"coat\":\"Nike\"},\"friends\":null}]}";    }
    @TearDown    public void shutdown() {    }}
```



执行后的结果图：

![图片](https://mmbiz.qpic.cn/mmbiz_png/uISTiacTMXkQ0rs0TYXDcTnjFNLicwC4SNbh2awehDcZ1icFkCIibpYsa14qkLIRlMUqP5nL1JUzy55WsSF6e7qvww/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

从上面的测试结果可以看出，反序列化的时候，Gson、Jackson和FastJson区别不大，性能都很优异，而那个Json-lib还是来继续搞笑的。

以上就是几种几种主流JSON库的基本介绍，希望能对你有所帮助！

## 序列化方法处理流程

**序列化**：把对象转换为字节序列存储于磁盘或者进行网络传输的过程称为对象的序列化。
**反序列化**：把磁盘或网络节点上的字节序列恢复到对象的过程称为对象的反序列化。

基本流程为：

1. 首先，构建通用序列化基础方法所需要的参数类型对象；
2. 其次，对序列化类型进行分析，根据注解或者”get方法名(比如getXxx,isXxx)”等来构建需要序列化的属性
3. 然后，通过反射机制分别对所有的序列化属性进行处理：通过发现拿到对应的值,getxxx方法等
4. 拼接字符串：其内部是根据类型写入一些开始结束符号，例如{,[等，在其中嵌入步骤3的解析设值
5. 返回最后得到的字符串内容