---
title: 为什么将ThreadLocal变量设置为private static
date: 2022-01-05 00:00:00
updated: 2022-01-05 00:00:00
tags: [Java,多线程,并发编程]
type: [Java,多线程,并发编程]
comments: 性能优化框架介绍
description: 页面描述
keywords: 关键字
top_img:
mathjax:
katex:
aside:
aplayer:
highlight_shrink:
---

[TOC]

# 概述

文章参考：https://blog.csdn.net/silyvin/article/details/79551635

https://www.zhihu.com/question/35250439




在JDK源码中这样写到，为什么呢?

```
<tt>ThreadLocal</tt> instances are typically private static fields in classes that wish to associate state with a thread。
```

首先，使用private只是Java的通用编程习惯。与ThreadLocal并没有本质性的关系，所以这里我们不做分析。

其实，ThreadLocal一般会使用static进行修饰。这样做既有好处也有坏处。好处是这样在一定程度上可以避免错误。至少可以避免重复创建TSO（Thread Specific Object 即ThreadLoca所关联的对象）所导致的浪费

缺点是：这样正好形成内存泄漏所需要的条件

下面，我们就来详细的分析一下：

我们知道ThreadLocal类的目的是为每个线程单独维护一个变量的值，避免线程间对同一变量的竞争访问，适用于一个变量在每个线程中需要有自己独立的值的场合。例如代码：

```
public class ThreadLocalManager {
    private static ThreadLocal threadLocal = new ThreadLocal();
    
    int setID(int id) {
        threadLocal.set(id);
    }
    
    int getID() {
        return threadLocal.get();
    }
}
```

当多线程同时访问类A的setID和getID方法时，每个线程的getID方法会返回自己setID()时设置的值。

那么ThreadLocal类型的成员变量threadLocalID为什么设置为static的呢？

Java 中每个线程都有与之关联的Thread对象，Thread对象中有一个ThreadLocal.ThreadLocalMap类型的成员变量，该变量是一个Hash表， 所以每个线程都单独维护这样一个Hash表，当ThreadLocal类型对象调用set方法时，即上面的threadLocalID.set(id)，这个set方法会使用当前线程维护的Hash表，把自己作为key, id作为value插入到Hash表中。由于每个线程维护的Hash表是独立的，因此在不同的Hash表中，key值即使相同也是没问题的。

如果把threadLocalID声明为非静态，则在类A的每个实例中都会产生一个新对象，这是毫无意义的，只是增加了内存消耗。



