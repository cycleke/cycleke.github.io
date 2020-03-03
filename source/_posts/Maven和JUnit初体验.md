---
title: Maven和JUnit初体验
tags:
  - Java
  - Maven
  - JUnit
categories: 工具学习
abbrlink: b7210194
date: 2020-03-03 18:46:00
---

最近由于软件构造课的实验需要，我需要进行Java的单元测试。但是Eclipse的体验个人不太
喜欢，IDEA在自己的渣笔记本又太慢，所以学习使用Maven来管理和测试。

~~此时大喊emacs大法好！！！~~
<!--more-->

# Maven
Maven是Apache开源组织的一个开源项目，Maven的本质是一个项目管理工具，将项目开发和
管理过程抽象成一个项目对象模型（Project Object Model,POM）。

Maven的主要功能有：
- 提供了一套标准化的项目结构；
- 提供了一套标准化的构建流程（编译，测试，打包，发布……）；
- 提供了一套依赖管理机制。

## 安装Maven
### Windows
首先需要安装JDK，然后从 [Maven官网](https://maven.apache.org/download.cgi) 下载Maven，之后解压、配置环境变量。
```
M2_HOME=/path/to/maven-3.6.x
PATH=$PATH:$M2_HOME/bin
```

### Linux
同样可以从官网下载，也可以通过包管理器下载。如Arch Linux可以：
``` bash
sudo pacman -S maven
```

### macOS
其实穷人并没有mac。ε=ε=ε=ε=ε=ε=┌(;￣◇￣)┘

### 判断

可以在终端中输入
``` bash
mvn --version
```
判断是否安装成功。

## 配置镜像
由于种种原因，Maven的官方源访问十分慢，需要配置国内镜像源。我使用的是阿里的源。
### 方式一
修改/path/to/maven/conf/settings.xml，在mirrors节点下面添加子节点：
``` xml
<id>nexus-aliyun</id>
<mirrorOf>central</mirrorOf>
<name>Nexus aliyun</name>
<url>http://maven.aliyun.com/nexus/content/groups/public</url>
```

### 方式二
在~/.m2中修改文件settings.xml，这样不用使用root权限，而且可以为每个用户使用单独的配置。
``` xml
<settings>
  <mirrors>
    <mirror>
      <id>aliyun</id>
      <name>aliyun</name>
      <mirrorOf>central</mirrorOf>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </mirror>
  </mirrors>
</settings>
```

### 方式三
单项目配置时，需要修改pom文件。pom文件中，没有mirror元素。在pom文件中，通过覆盖
默认的中央仓库的配置，实现中央仓库地址的变更。
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.test</groupId>
<artifactId>conifg</artifactId>
<packaging>war</packaging>
<version>0.0.1-SNAPSHOT</version>

<repositories>
    <repository>
        <id>central</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <layout>default</layout>
        <!-- 是否开启发布版构件下载 -->
        <releases>
            <enabled>true</enabled>
        </releases>
        <!-- 是否开启快照版构件下载 -->
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```

## 使用Maven
Maven的关键是项目描述文件pom.xml。下面是我此次实验中使用的pom.xml。
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>HIT</groupId>
    <artifactId>cycleke</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <source>8</source>
        <target>8</target>
    </properties>

    <build>
        <sourceDirectory>src</sourceDirectory>
        <resources>
            <resource>
                <filtering>true</filtering>
                <directory>src</directory>
                <includes>
                    <include>*.*</include>
                </includes>
            </resource>
            <resource>
                <filtering>true</filtering>
                <directory>txt</directory>
                <includes>
                    <include>*.*</include>
                </includes>
            </resource>
        </resources>

        <testSourceDirectory>test</testSourceDirectory>
        <testResources>
            <testResource>
                <filtering>true</filtering>
                <directory>src</directory>
                <includes>
                    <include>*.*</include>
                </includes>
            </testResource>
            <testResource>
                <filtering>true</filtering>
                <directory>txt</directory>
                <includes>
                    <include>*.*</include>
                </includes>
            </testResource>
        </testResources>

        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
            <scope>compile</scope>
        </dependency>
    </dependencies>

</project>
```

其中，groupId类似于Java的包名，通常是公司或组织名称，artifactId类似于Java的类名
，通常是项目名称，再加上version，一个Maven工程就是由groupId，artifactId和version
为唯一标识。我们在引用其他第三方库的时候，也是通过这3个变量确定。

注意上述版本的``SNAPSHOT``，SNAPSHOT版本和正式版本的区别可见[理解Maven中的SNAPSHOT版本和正式版本](https://www.cnblogs.com/huang0925/p/5169624.html)。

标准的Maven项目结构是

> maven-project
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   └── resources
│   └── test
│       ├── java
│       └── resources
└── target

其中src中存放代码，main是主要代码，test是测试代码，java放代码文件，resources放资
源；target存放生成的文件。

但是我们可以通过pom.xml修改和添加src等文件，如上面的pom.xml将代码目录修改了src和
test目录，项目结构如下：

> Lab1
├── src
│   ├── P1
│   ├── P2
│   └── P3
├── test
│   ├── P1
│   ├── P2
│   └── P3
└── target

通过pom.xml,我们还可以配置插件（如上面的maven-compiler-plugin）和第三方库（junit）。

## 常用指令

下面是Maven的常用指令：

| 命令                | 作用                       |
|---------------------|----------------------------|
| mvn clean           | 清理所有生成的class和jar   |
| mvn clean compile   | 先清理，再编译             |
| mvn clean test      | 先清理，再编译、运行测试   |
| mvn clean package   | 先清理，再编译、测试、打包 |
| mvn exec:exec       | 运行程序                   |
| mvn dependency:tree | 查看依赖关系               |



# JUnit
JUnit是一个开源的Java语言的单元测试框架，专门针对Java设计，使用最广泛。一般的IDE
都支持JUnit或对应的插件。JUnit已经发展到JUnit5，然而一般用的似乎是JUnit4。

~~又想起还有多少项目在Java8。。。GTMD Oracle~~

JUnit的测试方法有``@Test``注释，如下例：
``` java
package P1;

import static org.junit.Assert.assertEquals;
import org.junit.Test;

public class MagicSquareTest {
  @Test
  public void isLegalMagicSquareTest() {
    boolean[] corrects = new boolean[] { true, true, false, false, false, true };
    for (int i = 0; i < 6; ++i) {
    String fileName = String.format("txt/%d.txt", i + 1);
      assertEquals(corrects[i], MagicSquare.isLegalMagicSquare(fileName));
    }
  }

  @Test
  public void generateMagicSquareTest()  {
    assertEquals(false, MagicSquare.generateMagicSquare(0));
    assertEquals(false, MagicSquare.generateMagicSquare(2));
    assertEquals(true, MagicSquare.generateMagicSquare(101));
  }
}
```
``assertEquals``会判断函数运行结果是否与期望相同。
``Assertion``还定义了其他断言方法，可以翻阅[wiki](https://github.com/junit-team/junit4/wiki/Assertions) 查看。

JUnit还可以通过如下方式测试异常：
``` java
@Test(expected = RuntimeException.class)
public void expectedTest() {
  throws new RuntimeException("／人◕ ‿‿ ◕人＼");
}
```

写完单元测试后在Maven加入JUnit第三方库依赖，然后运行``mvn clean test``测试。

# 参考
[Maven官网](https://maven.apache.org/)
[Maven介绍](https://www.liaoxuefeng.com/wiki/1252599548343744/1309301146648610)
[Maven之阿里云镜像仓库配置](https://yq.aliyun.com/articles/695269)
[理解Maven中的SNAPSHOT版本和正式版本](https://www.cnblogs.com/huang0925/p/5169624.html)
[如何查看Maven项目中的jar包依赖树情况？](https://blog.csdn.net/majinggogogo/article/details/52098096)
[JUnit官网](https://junit.org/junit4/)
