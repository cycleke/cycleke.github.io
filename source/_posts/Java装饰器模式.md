---
title: Java装饰器模式
abbrlink: fa2f150e
date: 2020-04-23 21:40:17
updated: 2020-04-23 21:40:17
tags:
  - Java
categories: Java
---

装饰器模式（Decorator Pattern）是一种结构型模式，允许向一个现有的对象添加新的功能，同时又不改变其结构。

<!--more-->

# 介绍

通过装饰器模式，我们可以在运行时动态给某个对象实例添加功能。装饰器模式是类继承的另外一种选择。类继承在编译时候增加行为，而装饰模式是在运行时增加行为。由于是在运行时动态地添加功能，所以装饰器模式相比与子类继承更加灵活。

装饰器模式的原理是增加一个装饰类来包裹原来的类，而这个装饰类拥有新的功能，间接实现对与一个实例添加新的功能。在不需要使用新的功能的部分，它则直接调用被包裹的实例的接口。所以装饰类需要与原本的类具有相同的接口，一般可以然装饰类与被装饰类继承自同一个基类。

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"
    src="tree.svg">
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">一种典型的装饰器模式的继承图</div>
</center>

# 例子

Java 中修饰器模式的典型例子为 IO Stream。

```java
InputStream fileStream = new FileInputStream("test.gz");
InputStream bufferStream = new BufferedInputStream(fileStream);
InputStream gzipStream = new GZIPInputStream(bufferStream);
```

通过上面的例子，我们为一个`InputStream`添加了使用缓冲和解压缩的功能。我们也可以使用下面的方法构造。

```java
InputStream inputStream = new GZIPInputStream(new BufferedInputStream(new FileInputStream("test.gz")));
```

# 实现

我们首先声明一个基类：

```java
public interface SuperClass {
	void setValue(int value);

	int getValue();

	String getDescription();
}
```

再实现一个子类：

```java
public class SubClass implements SuperClass {
	private int value;

	public void setValue(int value) {
		this.value = value;
	}

	public int getValue() {
		return value;
	}

	public String getDescription() {
		return "This is SubClass.";
	}

}
```

紧接着，为了实现 Decorator 模式，需要有一个抽象的`Decorator`类：

```java
public abstract class Decorator implements SuperClass {
	protected SuperClass target;

	protected Decorator(SuperClass target) {
		this.target = target;
	}

	@Override
	public void setValue(int value) {
		target.setValue(value);
	}

	@Override
	public int getValue() {
		return target.getValue();
	}
}
```

通过使用`target`的 API，我们可以实现`SuperClass`要求的 API。最后我们来实现一个装饰器类。

```java
public class DecoratorA extends Decorator {

	public DecoratorA(SuperClass target) {
		super(target);
	}

	@Override
	public String getDescription() {
		return "Add a decorator DecoratorA\n" + target.getDescription();
	}
}
```

我们可以在运行时给一个实例添加新的功能。

```java
SuperClass class1 = new SubClass();
SuperClass class2 = new DecoratorA(class1);

System.out.println(class1.getDescription());
// 输出为: "Add a decorator DecoratorA\n"
System.out.println(class2.getDescription());
// 输出为: "Add a decorator DecoratorA\nThis is SubClass.\n"
```

# 总结

使用 Decorator 模式，我们可以在运行期动态地给核心功能增加任意个附加功能。Decorator 模式类似于穿衣服，可以在运行时根据需要添加特定的“工作服”。
