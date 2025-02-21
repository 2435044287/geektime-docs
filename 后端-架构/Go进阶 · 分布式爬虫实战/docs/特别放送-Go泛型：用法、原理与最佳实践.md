你好，我是郑建勋。

泛型一直是自Go语言诞生以来讨论最热烈的话题之一，之前，Go语言因为没有泛型而被很多人吐槽过。[经过了多年的设计](https://go.googlesource.com/proposal/+/HEAD/design/43651-type-parameters.md)，我们正式迎来了 Go1.18 泛型。这节课就让我们来看一看泛型的几个重要问题，你也可以先问一问自己下面几个问题。

- 什么是泛型？
- Go为什么需要泛型？
- Go之前为什么没有泛型？
- Go泛型的特性与使用方法是什么？

## 什么是泛型？

我们先来看看[维基百科](https://en.wikipedia.org/wiki/Generic_programming)对泛型的定义。

> Generic programming centers around the idea of abstracting from concrete, efficient algorithms to obtain generic algorithms that can be combined with different data representations to produce a wide variety of useful software.

泛型编程的核心思想是从具体的、高效的运算中抽象出通用的运算，这些运算可以适用于不同形式的数据，从而能够适用于各种各样的场景。

显然，泛型是高级语言为了让一段代码拥有更强的抽象能力和表现力而设计出来的。

许多语言都有对泛型的支持，我们可以看看其他语言是怎么实现泛型的。在Java中对函数进行泛型抽象的代码如下所示。
<div><strong>精选留言（1）</strong></div><ul>
<li><img src="https://static001.geekbang.org/account/avatar/00/11/e2/52/56dbb738.jpg" width="30px"><span>牙小木</span> 👍（1） 💬（1）<div>Key 可以是 int 或者 string，而 Value 必须是 string 或者 float。没get到这句话啥意思</div>2023-08-19</li><br/>
</ul>