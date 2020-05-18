---
title: Java异常类
abbrlink: fe568e9
date: 2020-05-18 00:40:30
updated: 2020-05-18 00:40:30
tags:
  - Java
categories: Java
---

在程序的运行过程中，各种错误是不可避免的，比如想要读入一个文件，但是文件不存在。

我们可以通过返回特殊值来表示错误（常见于一些较底层的语言如 C），但是它存在一定的局限性，如特殊值的数目有限、有时无法有效区分特殊值和错误......当一个函数返回的就是`boolean`类型时，那么就难以规定特殊值；一个函数返回一个`Object`类型，那么当返回值为`null`时到底是发生了错误还是值就是`null`就难以区分。

<!--more-->

# 简介

另一种方法为在语言层面上提供一个异常处理机制。`Java`就内置了一套异常处理机制，总是使用异常来表示错误。一个用来处理读取文件时文件不存在的代码。

```java
try {
  InputStream inpStream = FileInputStream(file);
} catch (FileNotFoundException e) {
  e.printStackTrace();
  choseAnotherFile();
}
```

这样当文件不存在时程序不会直接终止，而是提示选择其他文件，其中的`FileNotFoundException`是一种异常类。

异常类是一种特殊的类，它可以在任何地方抛出，但需要被捕获。如果没有在当前方法捕获，那么这个异常会沿着栈一层层向上传递。如果整个程序中没有捕获这个异常，那么它会传到运行时，最后在控制台显示，并结束整个程序。

# 继承关系

异常类的继承关系图如下：

```
                     ┌───────────┐
                     │  Object   │
                     └───────────┘
                           ▲
                           │
                     ┌───────────┐
                     │ Throwable │
                     └───────────┘
                           ▲
                 ┌─────────┴─────────┐
                 │                   │
           ┌───────────┐       ┌───────────┐
           │   Error   │       │ Exception │
           └───────────┘       └───────────┘
                 ▲                   ▲
         ┌───────┘              ┌────┴──────────┐
         │                      │               │
┌─────────────────┐    ┌─────────────────┐┌───────────┐
│OutOfMemoryError │... │RuntimeException ││IOException│...
└─────────────────┘    └─────────────────┘└───────────┘
                                ▲
                    ┌───────────┴─────────────┐
                    │                         │
         ┌─────────────────────┐ ┌─────────────────────────┐
         │NullPointerException │ │IllegalArgumentException │...
         └─────────────────────┘ └─────────────────────────┘
```

`Throwable`表示这个类可以被`throw`，即抛出。

其中`Error`类表示极其严重的错误，一般来说程序员无法处理，根据快速显示出 BUG 的原则，应该立即终止程序，如：

- `OutOfMemoryError`: 内存耗尽，无法获取足够的内存
- `StackOverflowError`： 栈溢出

而我们讨论的`Exception`则是运行时产生的错误，程序员对于这些异常应当捕获并处理。

# 分类

## 运行时错误`RuntimeException`和其他的异常

    这是从异常产生的原因所做的分类

异常分为两大类：运行时错误`RuntimeException`和其他的异常。

其中运行时错误的产生是由于程序员对于代码的处理不当，代码存在错误，如访问无效内存。而其他的异常一般是由程序员无法控制的外部原因导致，应当包含在代码的逻辑中，如 IO 错误。

运行时错误是可以避免的。若一个运行时错误被抛出，那么此段代码是存在错误的。程序员可以通过提前检验来避免`RuntimeException`。如`ArrayIndexOutOfBoundsException`完全可以通过事先检验下标是否合法来避免。

其他的异常则较难避免，因为外部因素时难以控制的。如对于`FileNotFoundException`，即使我们提前检测了文件是否存在，在程序运行时文件有可能被删除。

## `Checked Exception`和`Unchecked Exception`

    这是从异常处理机制的角度所做的分类

如果一个异常必须捕获，即该异常要由程序员来处理，那么它就是`Checked Exception`，包含`Exception`及其除了`RuntimeException`的其他子类。而如果一个异常不是必须捕获，那么它就是`Unchecked Exception`，包含`Error`类和`RuntimeException`类以及它们的子类。

![](d35befa84278d060a9d6d32e62d77101.png)

对于`Unchecked Exception`，程序员不需要使用`try...catch`等机制来处理这些异常。编译器会帮助我们处理这些异常。但是它可能会导致程序运行失败，出现潜在的 BUG。它类似于编程语言中的`dynamic type checking`，错误发生在运行时。

对于`Checked Exception`，我们必须使用`try...catch`来处理，或者在方法声明时要添加到`throws`中，让调动它的方法来处理。如果我们不使用这些机制显试地处理这些异常，那么程序会报错，类似于编程语言中的`static type checking`，在编译期就会检测错误。

当然，对于`Unchecked Exception`我们也可以采用`try...catch`机制来处理，但大多数时候是不需要的，也不应该这么做。这样做不利于 BUG 暴露出来。我们要让 BUG 较早显现，而不是掩耳盗铃。

当要决定是采用`Unchecked Exception`还是`Checked Exception`的时候，问一个问题:“如果这种异常一旦抛出，客户端会采用怎样的补救?”

- 如果客户端可以通过其他的方法恢复异常，那么采用`Checked Exception`
- 如果客户端对出现的异常无能为力，那么采用`Unchecked Exception`

一般我们应当努力的恢复，而不是简单地打印错误。

# 参考

1.  [https://www.liaoxuefeng.com/wiki/1252599548343744/1264734349295520](https://www.liaoxuefeng.com/wiki/1252599548343744/1264734349295520)
