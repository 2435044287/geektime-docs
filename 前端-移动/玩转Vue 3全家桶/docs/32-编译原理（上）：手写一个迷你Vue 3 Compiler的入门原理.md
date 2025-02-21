你好，我是大圣。

前面我们用了四讲，学习了Vue在浏览器中是如何执行的，你可以参考上一讲结尾的Vue执行全景图来回顾一下。在Vue中，组件都是以虚拟DOM的形式存在，加载完毕之后注册effect函数。这样组件内部的数据变化之后，用Vue的响应式机制做到了通知组件更新，内部则使用patch函数实现了虚拟DOM的更新，中间我们也学习了位运算、最长递增子序列等算法。

这时候你肯定还有一个疑问，那就是虚拟DOM是从哪来的？我们明明写的是template和JSX，这也是吃透Vue源码最后一个难点：Vue中的Compiler。

下图就是Vue核心模块依赖关系图，reactivity和runtime我们已经剖析完毕，迷你版本的代码你可以在[GitHub](https://github.com/shengxinjing/weiyouyi)中看到。今天开始我将用三讲的内容，给你详细讲解一下Vue在编译的过程中做了什么。

![图片](https://static001.geekbang.org/resource/image/59/15/59f10ba0b6a6ed5fb956ca05016fde15.jpg?wh=1888x982)

编译原理也属于计算机中的一个重要学科，Vue的compiler是在Vue场景下的实现，目的就是实现template到render函数的转变。

我们第一步需要先掌握编译原理的基本概念。Vue官方提供了模板编译的[在线演示](https://vue-next-template-explorer.netlify.app/#%7B%22src%22%3A%22%3Cdiv%20id%3D%5C%22app%5C%22%3E%5Cn%20%20%20%20%3Cdiv%20%40click%3D%5C%22%28%29%3D%3Econsole.log%28xx%29%5C%22%20%3Aid%3D%5C%22name%5C%22%3E%7B%7Bname%7D%7D%3C%2Fdiv%3E%5Cn%20%20%20%20%3Ch1%20%3Aname%3D%5C%22title%5C%22%3E%E7%8E%A9%E8%BD%ACvue3%3C%2Fh1%3E%5Cn%20%20%20%20%3Cp%20%3E%E7%BC%96%E8%AF%91%E5%8E%9F%E7%90%86%3C%2Fp%3E%5Cn%3C%2Fdiv%3E%5Cn%22%2C%22ssr%22%3Afalse%2C%22options%22%3A%7B%22mode%22%3A%22module%22%2C%22filename%22%3A%22Foo.vue%22%2C%22prefixIdentifiers%22%3Afalse%2C%22hoistStatic%22%3Atrue%2C%22cacheHandlers%22%3Atrue%2C%22scopeId%22%3Anull%2C%22inline%22%3Afalse%2C%22ssrCssVars%22%3A%22%7B%20color%20%7D%22%2C%22compatConfig%22%3A%7B%22MODE%22%3A3%7D%2C%22whitespace%22%3A%22condense%22%2C%22bindingMetadata%22%3A%7B%22TestComponent%22%3A%22setup-const%22%2C%22setupRef%22%3A%22setup-ref%22%2C%22setupConst%22%3A%22setup-const%22%2C%22setupLet%22%3A%22setup-let%22%2C%22setupMaybeRef%22%3A%22setup-maybe-ref%22%2C%22setupProp%22%3A%22props%22%2C%22vMySetupDir%22%3A%22setup-const%22%7D%2C%22optimizeBindings%22%3Afalse%7D%7D)。下图左侧代码是我们写的template，右侧代码就是compiler模块解成的render函数，我们今天的任务就是能够实现一个迷你的compiler。
<div><strong>精选留言（6）</strong></div><ul>
<li><img src="https://static001.geekbang.org/account/avatar/00/18/88/42/176ddd62.jpg" width="30px"><span>神瘦</span> 👍（0） 💬（1）<div>“tokenizer 的迷你实现”这个地方“把模板中的”这几个字后面一大段空白呢，你们那边能看到吗？检查下呢</div>2022-01-30</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/14/aa/a8/6ca767ca.jpg" width="30px"><span>openbilibili</span> 👍（0） 💬（1）<div>template中的p标签 不能有空格 不然解析不了</div>2022-01-06</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/0f/65/25/c6de04bc.jpg" width="30px"><span>斜月浮云</span> 👍（0） 💬（2）<div>hoisted开头的变量用于静态节点提升，说白了就是在整个生命周期中只需要进行一次创建，有效节省不必要的性能开销。

话说最后的generate代码明显不对啊，createVnode的后括号阔歪了哦~🙂</div>2022-01-03</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/25/91/09/6f0b987a.jpg" width="30px"><span>陈坚泓</span> 👍（0） 💬（0）<div>const [key, val] = token.val.replace(&#39;=&#39;,&#39;~&#39;).split(&#39;~&#39;)  
是不是可以写成
const [key, val] = token.val.split(&#39;=&#39;)  

备注里 &#47;&#47; trnsformText    估计是拼错了 </div>2022-05-20</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/2c/e6/eb/58da8565.jpg" width="30px"><span>Blueberry</span> 👍（0） 💬（0）<div>template语法需要经过这么一系列的编译，那h函数呢，是经过什么变成了最后的render语法?</div>2022-04-12</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/1e/7c/b8/b4e0dfb0.jpg" width="30px"><span>名字好长的大林</span> 👍（0） 💬（1）<div>PatchFlags 这个变量没有给？？</div>2022-02-04</li><br/>
</ul>