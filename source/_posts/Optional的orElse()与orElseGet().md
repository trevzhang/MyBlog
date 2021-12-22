---
title: Optional的orElse()与orElseGet()
date: 2020-12-30 02:25:44
categories: Java
tags: 
	- Java
---

项目中有这样一段代码：

```
return Optional.ofNullable(service.A()).orElse(service.B())
```

功能显而易见，service.A()如果返回值是null，则返回service.B()，否则直接返回service.A()。
实际使用中发现：
如果service.A()返回非null,最终结果是service.A(),然而service.B()这个方法也被执行了。这样肯定就不对了,如果service.B()中还有插入数据库或者RPC这种操作，问题就大了。刚开始还以为是什么执行顺序问题，后来在Stack Overflow上看到老外讨论orElse()和orElseGet()的区别，其中一点区别就是：

**orElse(T)无论前面Optional容器是null还是non-null，都会执行orElse里的方法，orElseGet(Supplier)并不会，如果service无异常抛出的情况下，Optional使用orElse或者orElseGet的返回结果都是一样的。**

stack overflow上有人还给出这样一个例子：

![1.webp](http://ww1.sinaimg.cn/large/005PIxshgy1gm5dphf4r1j30u00ehmyj.jpg)

↑上面提问者大概的意思就是，orElse()和orElseGet()都能在Optional容器的值为空时提供一个可选的值，用orElse(new Object())可以直接传入对象，而用orElseGet(Supplier)时需要传入一个Supplier（可以为Lamda表达式），问还有没有其他区别。

![2.webp](http://ww1.sinaimg.cn/large/005PIxshgy1gm5dpqn9vhj30u00ehwfs.jpg)



↑作者采用的最佳答案是这样的



看了上面代码，我就把我的代码改成如下即可：

```
return Optional.ofNullable(service.A()).orElseGet(() -> service.B())
```

结论：

**若方法不是纯计算型的，使用Optional的orElse(T)；**

**若有与数据库交互或者远程调用的，都应该使用orElseGet(Supplier)。**

附stack overflow地址：

> https://stackoverflow.com/questions/33170109/difference-between-optional-orelse-and-optional-orelseget#