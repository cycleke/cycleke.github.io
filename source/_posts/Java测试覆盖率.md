---
title: Java代码覆盖率工具
tags:
  - Java
  - Maven
  - JaCoCo
  - Eclipse
  - EclEmma
categories: 工具学习
abbrlink: 319a97ef
date: 2020-03-27 15:55:02
updated: 2020-03-27 22:10:05
---

在单元测试的过程中，我们需要一个指标来计量我们单元测试的质量。使用代码覆盖率来量化是一个常用的
选择。现在常用的代码覆盖率工具有 Emma，Cobertura，Jacoco 和 Clover(商用)。下面介绍在 Eclipse 和
Maven 中的一些使用方法。

<!-- more -->

# 各个工具特点

| 工具         | Jacoco                                                                                                                                      | Emma                                                                                               | Cobertura                                                                                                                                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 原理         | 使用 ASM 修改字节码                                                                                                                         | 修改 jar 文件，class 文件字节码文件                                                                | 基于 jcoverage,基于 asm 框架对 class 文件插桩                                                                                                                                                                  |
| 覆盖粒度     | 行，类，方法，指令，分支                                                                                                                    | 行，类，方法，基本块，指令，无分支覆盖                                                             | 项目，包，类，方法的语句覆盖/分支覆盖                                                                                                                                                                          |
| 插桩         | on the fly、offline                                                                                                                         | on the fly、offline                                                                                | offline，把统计代码插入编译好的 class 文件中                                                                                                                                                                   |
| 生成结果     | 在 Tomcat 的 catalina.sh 配置 javaangent 参数，指出需要收集覆盖率的文件，shutdown 时才收集，只能使用 kill 命令关闭 Tomcat，不要使用 kill -9 | html、xml、txt，二进制格式报表                                                                     | html，xml                                                                                                                                                                                                      |
| 缺点         | 需要源代码                                                                                                                                  | 1、需要 debug 版本，并打来 build.xml 中的 debug 编译项； 2、需要源代码，且必须与插桩的代码完全一致 | 1、不能捕获测试用例中未考虑的异常； 2、关闭服务器才能输出覆盖率信息（已有修改源代码的解决方案，定时输出结果；输出结果之前设置了 hook，会与某些服务器的 hook 冲突，web 测试中需要将 cobertura.ser 文件来回 copy |
| 性能         | 快                                                                                                                                          | 小巧                                                                                               | 插入的字节码信息更多                                                                                                                                                                                           |
| 执行方式     | maven，ant，命令行                                                                                                                          | 命令行                                                                                             | maven，ant                                                                                                                                                                                                     |
| Jenkins 集成 | 生成 html 报告，直接与 hudson 集成，展示报告，无趋势图                                                                                      | 无法与 hudson 集成                                                                                 | 有集成的插件，美观的报告，有趋势图                                                                                                                                                                             |
| 报告实时性   | 默认关闭，可以动态从 jvm dump 出数据                                                                                                        | 可以不关闭服务器                                                                                   | 默认是在关闭服务器时才写结果                                                                                                                                                                                   |
| 维护状态     | 持续更新中                                                                                                                                  | 停止维护                                                                                           | 停止维护                                                                                                                                                                                                       |

# Eclipse 和 EclEmma

在 Eclipse 中可以使用 EclEmma 插件来统计。EclEmma 是一个开源的软件测试工具，可以在编码过程中查
看代码调用情况、也可以检测覆盖率。

EclEmma 原本源于 EMMA 项目。自 2.0 起 EclEmma 开始基于 JaCoCo（其实现在 JaCoCo 的开发人员就
是 EMMA 的开发人员）。

可以在 Marketplace 中找到找到 EclEmma 并下载。
![Eclipse Marketplace](Eclipse_Marketplace.png)

使用 EclEmma 测试代码覆盖率。
![EclEmma测试结果](EclEmma.png)

> 其中不同的颜色有不同的意义：
>
> - 绿色表示代码被执行到
> - 黄色表示代码部分执行到
> - 红色表示代码没有被执行到

# 在 Maven 中使用 JaCoCo

在 Maven 中也可以使用 JaCoCo 来测试代码覆盖率。虽然在网上的资料很多，但是不知道为什么有些方法无法有效。下面的方法是我在自己的电脑上成功的方法。

在 pom.xml 中添加如下代码：

```xml
<project>
  <build>
    <plugins>
      <plugin>
	<groupId>org.jacoco</groupId>
	<artifactId>jacoco-maven-plugin</artifactId>
	<version>0.8.5</version>
	<executions>
	  <execution>
	    <id>prepare-agent</id>
	    <goals>
              <goal>prepare-agent</goal>
	    </goals>
	  </execution>
	  <execution>
	    <id>report</id>
	    <phase>test</phase>
	    <goals>
              <goal>report</goal>
	    </goals>
	  </execution>
	</executions>
      </plugin>
    </plugins>
  </build>
</project>
```

之后可以在终端中输入

```bash
mvn clean test
```

可以测试同时生成报告，在`target/site/jacoco/index.html`。

# 参考

1. [Jacoco Code Coverage - 简书](https://www.jianshu.com/p/16a8ce689d60).
2. [EclEmma 官网](https://www.eclemma.org/).
