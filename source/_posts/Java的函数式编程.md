---
title: Java的函数式编程
abbrlink: 80f8936f
date: 2020-06-05 00:09:37
updated: 2020-06-05 00:09:37
tags:
  - Java
  - 函数式编程
categories: Java
---

最近写软件构造的实验代码时经常使用到一些函数式编程的特性，所以打算写一篇博客用来给自己当做笔记。

<!--more-->

# 什么是函数式编程

函数式编程式是一种编程范式，也就是如何编写程序的方法论。它属于声明式的结构化编程，主要思想是把运算过程尽量写成一系列嵌套的函数调用。

一般而言，我们编程是通过产生各种「副作用」（如修改变量、读写文件）来实现的，而函数式编程不同，它不产生「副作用」，一个函数对于同一个输入总是同一个输出，与系统的其他部分无关（所以要实现随机数等功能需要一些「奇迹淫巧」），也不会修改外部变量。

当然函数式编程不是真的不产生「副作用」，不然就没法编程了。函数式编程是一种隔离应用逻辑（表达）与实际运行时解释的方法。我们的代码用来描述逻辑，而「副作用」由运行时实现。这样代码的抽象程度更高，我们可以直接描述逻辑。纯函数式代码，更易于测试，引用透明性会让代码可读性提升，提高开发阶段的效率，减少 BUG，提高产品质量。同时，函数式编程，相比命令式编程，可以让很多复杂的代码便得更简单。

函数式编程以前基本上只在学界使用，但是最近一些十分流行的语言也添加了一些函数式编程的特性，如`Erlang`、`JavaScript`、`Java`、`C#`等。当然也有一些纯函数式编程语言，最为知名的就是`Haskell`。

# Java 的函数式编程

## Lambda 表达式

在`Java`中需要实现一个单方法的接口，而且只用一次，如排序时的比较接口。一般我们会使用一个匿名类来实现。

```java
Integer[] arr = { 1, 3, 5, 7, 9, 2, 4, 6, 8, 10 };
Arrays.sort(arr, new Comparator<Integer>() {
  @Override
  public int compare(Integer a, Integer b) {
    return a.compareTo(b);
  }
});
```

而自`Java 8`开始我们就可以使用 Lambda 来实现一个匿名函数。

```java
Integer[] arr = { 1, 3, 5, 7, 9, 2, 4, 6, 8, 10 };
Arrays.sort(arr, (a, b) -> a.compareTo(b));
```

显然这样的代码可读性更高。一个 Lambda 表达式形如`(args) -> {expression}`，其中`(args)`是函数的参数，`{expression}`为函数具体表达式。参数可以省略类型，编译器可以自动推断。而表达式的内容和一般的函数没有太大区别，不过无法使用可变的外部变量。如果表达式的只有一句`return ...`，那么可以使用更简单的写法，即上面的写法。

## 方法引用

上面的内容我们还可以使用方法引用。所谓方法引用，是指如果某个方法签名和接口恰好一致，就可以直接传入方法引用。如上面的方法也可以改为：

```java
Integer[] arr = { 1, 3, 5, 7, 9, 2, 4, 6, 8, 10 };
Arrays.sort(arr, Integer::compare);
```

## 柯里化和高阶函数

柯里化是指把多个参数的函数转换为只有一个参数的函数，进而实现将函数标准化。而高阶函数就是返回函数的函数。

举一个`Haskell`中的例子，如一个两个参数的函数：

```haskell
multiple' :: (Num a) => a -> a -> a
multiple' a b = x * b

multiple2' :: (Num a) => a -> a
multiple2' = multiple' 2
--- multiple2' a = 2 * a
```

其中`multiple'`是一个将两个数字相乘的函数，但是其实它的实现方法是一个柯里化的过程。`multiple' 2 3`的实现其实是首先实现一个带有一个参数的函数`multiple2'`，之后再调用`multiple2' 3`。这样的过程可以使函数标准化，每个函数都是接受一个参数，返回一个值（可以是函数）。

而在`Java`中， 我们也可以利用 Lambda 表达式来实现柯里化。

```java
Function<Integer, Function<Integer, Integer>> multiple = x -> y -> x + y;
Function<Integer, Integer> multiple2 = = multiple.apply(2);
```

这里`Function`就是 Lambda 表达式的类型。

## Stream

在一般的函数式编程中，除了 Lambda 表达式，还是两个重要的函数是`map`和`reduce`。这两个函数会将一个序列变为另一个序列/一个值。`Java 8`引入了 `Stream` API 来实现这些功能。

`Stream`是任意对象的序列化。与`List`不同，`Stream`使用的是惰性计算，即在最终需要计算出结果之前每个`Stream`存储的是计算表达式，而具体的值没有实时存在内存中。当一个`Stream`被计算一次后，就不可以再使用了（包括它使用的流），如果需要使用就需要创建新的流。`Stream`可以使用`Arrays.stream`、`Stream.of`、`Collection::stream`等方法来创建。注意有几个特别的`Stream`：`IntStream`，`LongStream`和`DoubleStream`，用来指出基本类型。

`map`函数将一个系列映射为另一个序列，即`map : sequence x function -> sequence`。`Stream::map`可以将一个`Stream`转化为另一个`Stream`，将序列中的每一个元素映射为一个新元素并构成序列。

```java
IntStream s1 = IntStream.of(1, 2, 3);
IntStream s2 = s1.map(x -> x * x);
```

那么`s2`的值就是`IntStream.of(1, 4, 9)`。

`reduce`函数则是将一个序列转化为一个值，即`reduce : sequence x function -> element`。`Stream::reduce`就可以将一个`Stream<T>`映射到一个`T`值。

```java
int s1 = IntStream.of(1, 2, 3).reduce(0, (acc, x) -> acc + x);
System.out.println(s1);
```

注意这里的`reduce`需要一个初值。如果不提供初值，那么我们得到的是一个`Optional<Integer>`，因为`Stream`可能为空。

`Stream`还支持许多其他操作，下面是一些常用的操作：

- `filter`： 对一个流过滤
- `collect`： 输出一个`Collection`对象
- `groupingBy`： 将元素分组
- `sort`： 排序
- `distinct`： 去重
- `skip`： 跳过前面的部分元素
- `allMatch`： 全称量词
- `anyMatch`： 存在量词
- `forEach`： 相当与使用 for 迭代

# 参考

1. [函数式编程初探](https://www.ruanyifeng.com/blog/2012/04/functional_programming.html)
