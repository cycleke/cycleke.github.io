---
title: YAML简介
tags:
  - YAML
categories: 工具学习
abbrlink: a4eef3d0
date: 2020-04-07 14:25:14
updated: 2020-04-07 14:25:14
---

最近写实验时需要描述初始界面，为了减少代码中硬编码的部分，所以打算使用配置文件来描述，同时方便拓展。
目前常用的配置文件格式有 JSON、XML、ini、TOML 和 YAML。出于熟悉程度的考虑最早我打算使用 JSON，但是 JSON 比较花眼，写起来没有 YAML 方便，所以最后选择了 YAML。

<!-- more -->

# YAML 简介

YAML（YAML Ain't Markup Language）是一种适用于所有编程语言的通用的数据串行化格式。其一些基本语法规则如下：

- 大小写敏感
- 使用缩进表示层级关系（请自带游标卡尺）
- 缩进时不允许使用 Tab 键，只允许使用空格
- 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可
- 使用`#`来注释一行
- 在同一文件中可以使用`---`来分割不同的 YAML 块

YAML 中的数据有三种：

- 对象/键值对（key: value pair）
- 数组（sequence）
- 纯量（scalar）
- 标签（tag）

## 对象

YAMl 的对象与 JSON 有点类似，是一个键值对，使用冒号表示，下面就是一个对象。

```yml
student: bob
```

相当于 JSON 的

```json
{"student": "bob"}
```

YAML 可以使用对象套对象：

```yml
student:
  name: ken
  age: 17
  class: 5
```

YAML 也允许另一种写法，将所有键值对写成一个行内对象：

```yml
student: {name: ken, age: 17, class: 5}
```

## 数组

数组是一组`-`打头的行，他们的层级关系相同。

```yml
- C
- Java
- Python
- Haskell
```

相当于 JSON 的

```json
["C", "Java", "Python", "Haskell"]
```

同样的可以将数组和对象相互嵌套：

```yml
---
# 二维数组
- [name, hr, avg]
- [Mark McGwire, 65, 0.278]
- [Sammy Sosa, 63, 0.288]
---
# 对象包含数组
Language:
  - C
  - Java
  - Python
  - Haskell
---
# 对象数组
- name: Mark McGwire
  hr: 65
  avg: 0.278
- name: Sammy Sosa
  hr: 63
  avg: 0.288
```

通过对象和数组的不断嵌套，可以表示出各种复杂的数据。

## 纯量

纯量就是将原文本完整的存下来，不做其它的分析，即纯文本。纯量可以使用`|`和`>`表示。其中`|`是保留换行符，而`>`会折叠换行，具体可以参考[官方文档说明](<https://yaml.org/spec/1.2/spec.html#b-l-trimmed(n,c)>)。

```yml
---
# ASCII Art
|
  \//||\/||
  // ||  ||__
---
>
  Mark McGwire's
  year was crippled
  by a knee injury.
```

纯量也可以使用引号表示：

```yml
unicode: "Sosa did fine.\u263A"
control: "\b1998\t1999\t2000\n"
hex esc: "\x0d\x0a is \r\n"

single: '"Howdy!" he cried.'
quoted: " # Not a 'comment'."
tie-fighter: '|\-*-/|'
```

## 标签

标签其实就是一系列数据类型，用于对于纯量标记，包括：

- 数组(seq)
- 键值对(map)
- 整数(int)
- 浮点数(float)
- 布尔值(boolean)
- ...

YAML 可以解析出一些数据类型，也可以使用`!`标记。

```yml
---
# int
canonical: 12345
decimal: +12345
octal: 0o14
hexadecimal: 0xC

---
# float
canonical: 1.23015e+3
exponential: 12.3015e+02
fixed: 1230.15
negative infinity: -.inf
not a number: .NaN

---
# timestamp
canonical: 2001-12-15T02:59:43.1Z
iso8601: 2001-12-14t21:59:43.10-05:00
spaced: 2001-12-14 21:59:43.10 -5
date: 2002-12-14

---
# binary
canonical: !!binary "\
  R0lGODlhDAAMAIQAAP//9/X17unp5WZmZgAAAOfn515eXvPz7Y6OjuDg4J+fn5\
  OTk6enp56enmlpaWNjY6Ojo4SEhP/++f/++f/++f/++f/++f/++f/++f/++f/+\
  +f/++f/++f/++f/++f/++SH+Dk1hZGUgd2l0aCBHSU1QACwAAAAADAAMAAAFLC\
  AgjoEwnuNAFOhpEMTRiggcz4BNJHrv/zCFcLiwMWYNG84BwwEeECcgggoBADs="
generic: !binary |
  R0lGODlhDAAMAIQAAP//9/X17unp5WZmZgAAAOfn515eXvPz7Y6OjuDg4J+fn5
  OTk6enp56enmlpaWNjY6Ojo4SEhP/++f/++f/++f/++f/++f/++f/++f/++f/+
  +f/++f/++f/++f/++f/++SH+Dk1hZGUgd2l0aCBHSU1QACwAAAAADAAMAAAFLC
  AgjoEwnuNAFOhpEMTRiggcz4BNJHrv/zCFcLiwMWYNG84BwwEeECcgggoBADs=
description: The binary value above is a tiny arrow encoded as a gif image.

---
# unordered set
!!set
? Mark McGwire
? Sammy Sosa
? Ken Griff
```

# 参考

1. [YAML 官方文档](https://yaml.org/spec/1.2/spec.html)
2. [YAML 语言教程](https://www.ruanyifeng.com/blog/2016/07/yaml.html)
3. [SnakeYAML 文档](https://bitbucket.org/asomov/snakeyaml/wiki/Documentation)
