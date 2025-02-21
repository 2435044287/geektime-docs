你好，我是宫文学。

上一讲，我给你概要地介绍了一下Julia这门语言，带你一起解析了它的编译器的编译过程。另外我也讲到，Julia创造性地使用了LLVM，再加上它高效的分派机制，这就让一门脚本语言的运行速度，可以跟C、Java这种语言媲美。更重要的是，你用Julia本身，就可以编写需要高性能的数学函数包，而不用像Python那样，需要用另外的语言来编写（如C语言）高性能的代码。

那么今天这一讲，我就带你来了解一下Julia运用LLVM的一些细节。包括以下几个核心要点：

- **如何生成LLVM IR？**
- **如何基于LLVM IR做优化？**
- **如何利用内建（Intrinsics）函数实现性能优化和语义个性化？**

这样，在深入解读了这些问题和知识点以后，你对如何正确地利用LLVM，就能建立一个直观的认识了，从而为自己使用LLVM打下很好的基础。

好，首先，我们来了解一下Julia做即时编译的过程。

## 即时编译的过程

我们用LLDB来跟踪一下生成IR的过程。

```
$ lldb                      #启动lldb
(lldb)attach --name julia   #附加到julia进程
c                           #让julia进程继续运行
```

首先，在Julia的REPL中，输入一个简单的add函数的定义：

```
julia> function add(a, b)
            x = a+b
            x
       end
```

接着，在LLDB或GDB中设置一个断点“br emit\_funciton”，这个断点是在codegen.cpp中。
<div><strong>精选留言（2）</strong></div><ul>
<li><img src="https://static001.geekbang.org/account/avatar/00/16/fd/83/b432b125.jpg" width="30px"><span>鱼_XueTr</span> 👍（2） 💬（0）<div>jinpeng.d@Mac$ lldb
(lldb) attach --name julia
error: attach failed: could not find a process named julia
(lldb) ^D</div>2020-07-26</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/26/eb/d7/90391376.jpg" width="30px"><span>ifelse</span> 👍（0） 💬（0）<div>😂</div>2022-01-17</li><br/>
</ul>