---
title: 使用SnakeYAML解析YAML
tags:
  - Java
  - SnakeYAML
  - YAML
categories: 工具学习
abbrlink: fd11bfea
date: 2020-04-07 15:47:12
updated: 2020-04-07 15:47:12
---

在 [YAML 简介](https://cycleke.github.io/posts/a4eef3d0/)中简单介绍了 YAML，这里介绍一下使用 [SnakeYAML](https://bitbucket.org/asomov/snakeyaml/src/master/) 来解析 YAML。

~~如果想要使用 Java 解析 json，推荐使用 [fastjson](https://github.com/alibaba/fastjson) 。~~

<!-- more -->

# 安装

你可以下载 SnakeYAML 的 jar 包[](https://repo1.maven.org/maven2/org/yaml/snakeyaml/1.25/snakeyaml-1.25.jar)，也可以在`pom.xml`中添加

```xml
<dependencies>
  <dependency>
    <groupId>org.yaml</groupId>
    <artifactId>snakeyaml</artifactId>
    <version>1.26-SNAPSHOT</version>
  </dependency>
</dependencies>
```

# 简单使用

## 使用 load

下面是一个使用 load 来解析 YAML 的例子：

```java
Yaml yaml = new Yaml();
String document = "\n- Hesperiidae\n- Papilionidae\n- Apatelodidae\n- Epiplemidae";
List<String> list = (List<String>) yaml.load(document);
System.out.println(list.toString());
```

程序输出为：

```
[Hesperiidae, Papilionidae, Apatelodidae, Epiplemidae]
```

当然，SnakeYAML 也可以读入文件：

```java
Yaml yaml = new Yaml();
InputStream inputStream = new FileInputStream(new File("src/main/resource/example_1.yaml"));
Object data = yaml.load(inputStream);
System.out.println(data.toString());
```

其中`example_1.yaml`内容如下：

```yml
int:
  - canonical: 12345
  - decimal: +12345
  - octal: 0o14
  - hexadecimal: 0xC
float:
  - canonical: 1.23015e+3
  - exponential: 12.3015e+02
  - fixed: 1230.15
  - negative infinity: -.inf
  - not a number: .NaN
timestamp:
  - canonical: 2001-12-15T02:59:43.1Z
  - iso8601: 2001-12-14t21:59:43.10-05:00
  - spaced: 2001-12-14 21:59:43.10 -5
  - date: 2002-12-14
```

对映的程序输出为：

```
{int=[{canonical=12345}, {decimal=12345}, {octal=0o14}, {hexadecimal=12}]},
{float=[{canonical=1230.15}, {exponential=1230.15}, {fixed=1230.15}, {negative infinity=-Infinity},{not a number=NaN}]},
{timestamp=[{canonical=Sat Dec 15 10:59:43 CST 2001}, {iso8601=Sat Dec 15 10:59:43 CST 2001},{spaced=Sat Dec 15 10:59:43 CST 2001}, {date=Sat Dec 14 08:00:00 CST 2002}]}
```

当然我们使用时可以将`Object`转化为对应的数据类型，如`Map`或`List`。

## loadAll

如果需要加载的 yaml 文件中包含多个 yaml 片段，那么就无法使用 load 方法，而是使用 loadAll 方法加载所有的 yaml 内容。比如有如下一个 yaml 文件：

```yml
---
Time: 2001-11-23 15:01:42 -5
User: ed
Warning: This is an error message
  for the log file
---
Time: 2001-11-23 15:02:31 -5
User: ed
Warning: A slightly different error
  message.
---
Date: 2001-11-23 15:03:17 -5
User: ed
Fatal: Unknown variable "bar"
Stack:
  - file: TopClass.py
    line: 23
    code: |
      x = MoreObject("345\n")
  - file: MoreClass.py
    line: 58
    code: |-
      foo = bar
```

可以使用如下代码解析：

```java
Yaml yaml = new Yaml();
InputStream inputStream = new FileInputStream(new File("src/main/resource/example_2.yaml"));
for (Object data : yaml.loadAll(inputStream))
	System.out.println(data.toString());
```

输出为：

```
{Time=Sat Nov 24 04:01:42 CST 2001, User=ed, Warning=This is an error message for the log file}
{Time=Sat Nov 24 04:02:31 CST 2001, User=ed, Warning=A slightly different error message.}
{Date=Sat Nov 24 04:03:17 CST 2001, User=ed, Fatal=Unknown variable "bar", Stack=[{file=TopClass.py, line=23, code=x = MoreObject("345\n")
}, {file=MoreClass.py, line=58, code=foo = bar}]}
```

## 解析出类

我们可以不断使用类型转换获得一个 YAML 文件的数据。不过如果一个 YAML 文件存储的是一个类的数据，那么这种方法就太麻烦了。SnakeYAML 也考虑了这个问题。

对于一个`Customer`类，我们在一个 YAML 文件中存下所有的数据。

```yml
firstName: "John"
lastName: "Doe"
age: 31
contactDetails:
  - type: "mobile"
    number: 123456789
  - type: "landline"
    number: 456786868
homeAddress:
  line: "Xyz, DEF Street"
  city: "City Y"
  state: "State Y"
  zip: 345657
```

而`Customer`类为

```java
public class Customer {
    private String firstName;
    private String lastName;
    private int age;
    private List<Contact> contactDetails;
    private Address homeAddress;
    // getters and setters
}

public class Contact {
    private String type;
    private int number;
    // getters and setters
}

public class Address {
    private String line;
    private String city;
    private String state;
    private Integer zip;
    // getters and setters
}
```

我们可以使用如下代码直接构造一个`Customer`类：

```java
Yaml yaml = new Yaml(new Constructor(Customer.class));
InputStream inputStream = new FileInputStream(new File("src/main/resource/example_3.yaml"));
Customer customer = yaml.load(inputStream);
```

SnakeYAML 还可以使用`loadAs`方法来或在 YAML 文件使用标记（如使用`!!com.cycleke.Demo.Customer`）直接解析出对应类。

## 其它

SnakeYAML 还包含许多用法，如使用`dump`输出为 YAML 文件等，建议阅读[SnakeYAML 文档](https://bitbucket.org/asomov/snakeyaml/wiki/Documentation) 。

# 参考

1. [SnakeYAML 文档](https://bitbucket.org/asomov/snakeyaml/wiki/Documentation)
2. [Parsing YAML with SnakeYAML | Baeldung](https://www.baeldung.com/java-snake-yaml)
3. [SnakeYaml 快速入门](https://www.jianshu.com/p/d8136c913e52)
