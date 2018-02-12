---
title: Java系列（三）——类文件结构
date: 2018-02-11 15:23:44
categories: Java
tags:
- Java
- Class
- 学习
- 小结
---

JVM 的一大成就带来了平台无关性和日益凸显的语言无关性，而字节码（ByteCode）作为语言与平台的“中间代码”，正是构成这种无关性的基石，不同的语言经过各自的编译器编译后形成相同格式的字节码，再被 Java 虚拟机解释为平台相关的机器码并执行，从而实现了一次编写，到处运行（Write Once, Run Anywhere）。

![](/images/Jvm_class_loading.png)

<center>Java 虚拟机提供的语言无关性，图片来源于网络</center>

# Class 类文件的结构

Class 文件是一组以 8 位字节为基础单位的二进制流，其本质就是一张表，如下图所示，u1、u2、u4 表示 1 个字节、2 个字节、4 个字节的**无符号数**，无符号数可以用来描述数字、索引引用、数量值或者按照 utf-8 编码构成的字符串值。_info 结尾的表示**表**，用以描述有层次关系的复合结构数据。

| **类型**         | **名称**              | **描述**                           |
| -------------- | ------------------- | -------------------------------- |
| u4             | magic               | 魔数，用以标记文件类型，固定值0xCAFEBABE        |
| u2             | minor_version       | 次版本号，向上兼容                        |
| u2             | major_version       | 主版本号                             |
| u2             | constant_pool_count | **常量**的个数                        |
| cp_info        | constant_pool       | 常量池，数量为（constant_pool_count - 1） |
| u2             | access_flags        | 访问标识                             |
| u2             | this_class          | 当前类索引                            |
| u2             | super_class         | 父类索引                             |
| u2             | interfaces_count    | 接口的个数                            |
| u2             | interfaces          | 具体的接口内容，数量为 interfaces_count     |
| u2             | fields_count        | **字段**的个数                        |
| field_info     | fields              | 具体的字段内容，数量为 fields_count         |
| u2             | methods_count       | **方法**的个数                        |
| method_info    | methods             | 具体的方法内容，数量为 methods_count        |
| u2             | attribute_count     | **属性**的个数                        |
| attribute_info | attributes          | 具体的属性内容，数量为 attributes_count     |

 ## 常量池（Constant pool）

常量池是Class文件空间最大的数据项之一，长度不固定，主要包含两类：字面量（Literal）和符号引用（Symbolic References），字面量比较接近 Java 语言层面的常量概念，如文本字符串，声明为 final 的常量值等；符号引用这属于编译原理方面的概念，主要包括下面三类常量：

```
- 类和接口全限定名(Full Qualified Name)

- 字段的名称和描述符(Descriptor)

- 方法的名称和描述符
```

Java 是在虚拟机加载 Class 文件的时候进行的动态链接，因此 Class 文件不会保存各个方法字段的最终内存布局信息，关于动态链接部分，将在下一偏文章介绍。

常量池中的每一个数据项都有自己的类型，目前 Java 8一共有以下 14 种表结构数据支持不同的常量类型，具体结构可参见[官方文档](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html)。

| **常量池中数据项类型**                    | **类型标志** | **类型描述**           |
| -------------------------------- | -------- | ------------------ |
| CONSTANT_Utf8_info               | 1        | UTF-8编码的Unicode字符串 |
| CONSTANT_Integer_info            | 3        | int类型字面值           |
| CONSTANT_Float_info              | 4        | float类型字面值         |
| CONSTANT_Long_info               | 5        | long类型字面值          |
| CONSTANT_Double_info             | 6        | double类型字面值        |
| CONSTANT_Class_info              | 7        | 对一个类或接口的符号引用       |
| CONSTANT_String_info             | 8        | String类型字面值        |
| CONSTANT_Fieldref_info           | 9        | 对一个字段的符号引用         |
| CONSTANT_Methodref_info          | 10       | 对一个类中声明的方法的符号引用    |
| CONSTANT_InterfaceMethodref_info | 11       | 对一个接口中声明的方法的符号引用   |
| CONSTANT_NameAndType_info        | 12       | 对一个字段或方法的部分符号引用    |
| CONSTANT_MethodHandle_info       | 15       | 表示方法句柄             |
| CONSTANT_MethodType_info         | 16       | 标示方法类型             |
| CONSTANT_InvokeDynamic_info      | 18       | 表示一个动态方法调用点        |

可以使用 JDK 提供的 javap 工具，```javap -verbose ClassName```的方式查看 Class 中的常量。

## 访问标志（Access flags）

2个字节代表，标示用于识别一些类或者接口层次的访问信息。

| 标识名            | 标识值    | 解释                              |
| -------------- | ------ | ------------------------------- |
| ACC_PUBLIC     | 0x0001 | 声明为public;可以从包外部访问              |
| ACC_FINAL      | 0x0010 | 被声明为final;不允许子类修改               |
| ACC_SUPER      | 0x0020 | 当被invokespecial指令调用时，将特殊对待父类的方法 |
| ACC_INTERFACE  | 0x0200 | 接口标识符                           |
| ACC_ABSTRACT   | 0x0400 | 声明为abstract;不能被实例化              |
| ACC_SYNTHETIC  | 0x1000 | 声明为synthetic;不存在于源代码，由编译器生成     |
| ACC_ANNOTATION | 0x2000 | 声明为注释类型                         |
| ACC_ENUM       | 0x4000 | 声明为枚举类型                         |

## 类索引、父类索引和接口索引

类索引（this_class）和父类索引（super_class）都是一个 u2 的数据类型，而接口索引（Interfaces）是一组长度为interfaces_count 的 u2 类型的数据集合，它们的值大多存放的是对常量池中 CONSTANT_Class_info 的引用， Class 文件中由这三项数据来确定这个类的继承关系。

## 字段表（Field info）

字段表用于描述类或接口中声明的变量，格式如下：

```
field_info {
    u2             access_flags; //访问标识
    u2             name_index;  //名称索引
    u2             descriptor_index; //描述符索引
    u2             attributes_count; //属性个数
    attribute_info attributes[attributes_count];  //属性表的具体内容
}
```

字段访问标识如下：(表中加粗项是字段独有的)：

| 标识名               | 标识值    | 解释                                |
| ----------------- | ------ | --------------------------------- |
| ACC_PUBLIC        | 0x0001 | 声明为 public; 可以从包外部访问              |
| ACC_PRIVATE       | 0x0002 | 声明为 private; 只有定义的类可以访问           |
| ACC_PROTECTED     | 0x0004 | 声明为 protected;只有子类和相同package的类可访问 |
| ACC_STATIC        | 0x0008 | 声明为 static；属于类变量                  |
| ACC_FINAL         | 0x0010 | 声明为 final; 对象构造后无法直接修改值           |
| **ACC_VOLATILE**  | 0x0040 | 声明为 volatile; 不会被缓存,直接刷新到主屏幕      |
| **ACC_TRANSIENT** | 0x0080 | 声明为 transient; 不能被序列化             |
| ACC_SYNTHETIC     | 0x1000 | 声明为 synthetic; 不存在于源代码，由编译器生成     |
| ACC_ENUM          | 0x4000 | 声明为enum                           |

Java语法中，接口中的字段默认包含ACC_PUBLIC, ACC_STATIC, ACC_FINAL标识。ACC_FINAL，ACC_VOLATILE不能同时选择等规则。紧跟其后的name_index和descriptor_index是对常量池的引用，分别代表着字段的简单名和方法的描述符。

## 方法表（Method info）

方法表用于描述类或接口中声明的方法，格式如下：

```
method_info {
    u2             access_flags; //访问标识
    u2             name_index;  //名称索引
    u2             descriptor_index;  //描述符索引
    u2             attributes_count;  //属性个数
    attribute_info attributes[attributes_count]; //属性表的具体内容
}
```

方法访问标识如下：(表中加粗项是方法独有的)

| 标识名                  | 标识值    | 解释                                |
| -------------------- | ------ | --------------------------------- |
| ACC_PUBLIC           | 0x0001 | 声明为 public; 可以从包外部访问              |
| ACC_PRIVATE          | 0x0002 | 声明为 private; 只有定义的类可以访问           |
| ACC_PROTECTED        | 0x0004 | 声明为 protected;只有子类和相同package的类可访问 |
| ACC_STATIC           | 0x0008 | 声明为 static；属于类变量                  |
| ACC_FINAL            | 0x0010 | 声明为 final; 不能被覆写                  |
| **ACC_SYNCHRONIZED** | 0x0020 | 声明为 synchronized; 同步锁包裹           |
| ACC_BRIDGE           | 0x0040 | 桥接方法, 由编译器生成                      |
| **ACC_VARARGS**      | 0x0080 | 声明为 接收不定长参数                       |
| **ACC_NATIVE**       | 0x0100 | 声明为 native; 由非Java语言来实现           |
| **ACC_ABSTRACT**     | 0x0400 | 声明为 abstract; 没有提供实现              |
| **ACC_STRICT**       | 0x0800 | 声明为 strictfp; 浮点模式是FP-strict      |
| ACC_SYNTHETIC        | 0x1000 | 声明为 synthetic; 不存在于源代码，由编译器生成     |

- 对于方法里的Java代码，进过编译器编译成字节码指令后，存放在方法属性表集合中“code”的属性内。
- 当子类没有覆写父类方法，则方法集合中不会出现父类的方法信息。
- Java语言中重载方法，必须与原方法同名，且特征签名不同。特征签名是指方法中各个参数在常量池的字段符号引用的集合，不包括返回值。当时Class文件格式中，特征签名范围更广，允许方法名和特征签名都相同，但返回值不同的方法，合法地共存子啊同一个Class文件中。

## 属性表（Attribute info）

属性表的基本结构，不同类型的属性表以此为基础各不相同：

```
attribute_info {
    u2 attribute_name_index;
    u4 attribute_length;
    u1 info[attribute_length];
}
```

属性表的限制相对宽松，不需要各个属性表有严格的顺序，只有不与已有的属性名重复，任何自定义的编译器都可以向属性表中写入自定义的属性信息，Java虚拟机运行时会忽略掉无法识别的属性。 关于虚拟机规范中预定义的属性，这里不展开讲了，列举几个常用的。

| 属性名                | 使用位置      | 解释                                       |
| ------------------ | --------- | ---------------------------------------- |
| Code               | 方法表       | 方法体的内容                                   |
| ConstantValue      | 字段表       | final关键字定义的常量值                           |
| Deprecated         | 类、方法表、字段表 | 声明为deprecated                            |
| InnerClasses       | 类文件       | 内部类的列表                                   |
| LineNumberTable    | Code属性    | Java源码的行号与字节码指令的对应关系                     |
| LocalVariableTable | Code属性    | 方法的局部变量描述                                |
| Signature          | 类、方法表、字段表 | 用于支持泛型的方法签名，由于Java的泛型采用擦除法，避免类型信息被擦除后导致签名混乱，Signature记录相关信息 |

# 小结

Class 文件格式所具备的平台中立（不依赖于特定硬件及操作系统）、紧凑、稳定和可扩展的特点，是 Java 技术实现平台无关、语言无关两大特性的重要支柱。

Class 文件是 Java 虚拟机执行引擎的数据入口，也是 Java 体系技术构成之一。了解 Class 文件的结构对后面进一步了解虚拟机执行引擎有重要的意义。

> * References:
> * <http://gityuan.com/2015/10/17/jvm-class-instruction/>
> * <https://my.oschina.net/HeliosFly/blog/356517>
> * <https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html>

