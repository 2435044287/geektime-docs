你好，我是何辉，很高兴能在这门Dubbo实战进阶课中和你相遇。

先自我介绍一下，我是一名擅长用Java / Python / Go封装插件或工具来解决通用性问题的一线架构师，也曾因为解决通用性问题先后申请了五十余项国家专利。

解决问题这件事，一直以来，我比较信奉爱因斯坦的一句话：“你无法在制造问题的同一思维层次上解决这个问题”。所以，既然能解决，我们要么依靠经验技巧，要么依靠方法流程、科学原理，或者是依靠哲学理念。

而问题，始终都会有的。对于Dubbo来说，每当我带领团队成员设计功能、代码编写、问题排查时，经常被问到：

- 我该怎么快速掌握Dubbo框架体系？
- Dubbo的知识点我都看了，为什么在实际应用的时候就想不到呢？
- 某些Dubbo特性我也知道，但是为什么需要有这样特性的存在？
- 看到Dubbo各种底层报错，如何根据问题反推用哪些特性来解决呢？

这些问题，你一定或多或少疑惑过。为了找到自己心中想要的答案，相信你也曾试图把Dubbo框架的代码翻个遍，**但随着时间的流逝，你还能记住多少，又或者说你理解了多少？这也是我当时学Dubbo的困境。**

因为Dubbo的学习说复杂也复杂，说简单也简单。

随着微服务的流行，现在好多项目要么起步就是微服务，要么就是在转微服务的过程中，但市场上微服务的框架为数不多，而属于Java开发的框架更加凤毛麟角了，Dubbo框架，恰巧就是其中一款。

而这么一款几乎是国内微服务的首选框架，Dubbo可谓是身经百战，经历了阿里巴巴各种复杂业务的高并发挑战，也受到大规模互联网企业的高度认可，涵盖从战略到战术、从顶层到底层、从抽象到具体，有着非常全面的技术知识体系，这是Dubbo学习的复杂所在。

那怎样才能更简单地上手并掌握Dubbo框架呢？

很多人就像我前面说的那样，花大量的时间和精力钻研Dubbo框架的代码，当然可行，但也会走很多弯路，而且可能因为一头扎进源码中，沉迷细节无法自拔，终究比较痛苦地拿下了最难的知识体系，却又因为缺乏实战经验，在开发时，对如何应用仍然毫无头绪，感觉学了个寂寞。

其实，对于我们大多数人来说，最好最简单的学习方式一定是带着问题学习，要会抓体系、抓主干、抓思路，重思考、重推导、重理解，而不是一上来就去啃源码，这样很容易把自己的信心压垮。

毕竟，**凡是你理解的都会记住，凡是你不理解的都会遗忘**。我们学Dubbo，不是学怎么用Java写出一手好代码，而是学它的编码技巧、设计思想、架构精髓，然后按照自己所理解的方式去使用。

当你认认真真在几个框架中实践以后，你会发现很多之前觉得难的设计思路，其实套路都差不多，都是以最简单实用的逻辑，在特定的场景中，以不同的形式，体现在框架中。

所以，在这个专栏里，我会带着你以“发现问题——分析问题——解决问题”的案例驱动的思路，从一个问题现象出发，分析如何思考问题，一步步推导出需要怎样的技术支撑，再从我们已有的知识储备搜刮出可以有哪些解决方案，最后针对这些解决方案，快速有效地细化出落地方案，逐渐找到透过现象看本质的方法论。

具体会分为 4 个模块：

- **基础篇**

用一张Dubbo的总体架构图，把日常的开发流程串联起来，勾勒起你对Dubbo数十个基础知识点的整体印象；在此基础上，用视频形式带你统一梳理Dubbo日常开发必须掌握的基础特性，查漏补缺。

如果你是初学者，掌握好基础篇就能应付日常开发实践了。

- **特色篇**

以真实案例为背景，逐步分析并推导出需要的技术手段，带你灵活应用框架中的高级特性来解决实际问题。深入理解高级特性外，也有助于你利用高级特性开发出比较通用的产品功能。

如果你是有Dubbo基础的开发者，掌握特色篇基本上可以在实战中横着走了。

- **源码篇**

通过源码的学习，达到知其然知其所以然。我们会站在框架设计者的角度，体会Dubbo框架每个机制设计的亮点所在，锻炼你对Dubbo掌握的纵向深度。

如果你对自己有更高要求，掌握了源码篇，你可以称得上 Dubbo 框架高手了。

- **拓展篇**

在这里我们将针对一些工作中的实际诉求，分析出需要的功能解决方案，并且从前面已学的知识点中，提取关键要素尝试解决，在应用中进一步提升你对Dubbo的理解，晋级宗师。

![图片](https://static001.geekbang.org/resource/image/59/10/5961cae218a07c07510715a43c862310.jpg?wh=1920x1485)

整个专栏，我们会循序渐进地学习，但每一讲是相对独立的，你也可以针对性学习，思考题，我也会在下一讲解答。

而且每一讲，也都是面试环节经常被问到的知识点，你完全可以参考我们学习的思路跟面试官掰扯，在一个什么样的实战场景中，你遇到了一个什么样的难题，是怎么分析得到解决方案的，又是如何找到突破口的，最后你还能利用课程中的实战代码向面试官说明你是如何编码解决的（课程的代码仓库链接： [https://gitee.com/ylimhhmily/GeekDubbo3Tutorial](https://gitee.com/ylimhhmily/GeekDubbo3Tutorial)）。

有问题，有思路，有解法，还有代码，保证表现亮眼。

好，马上就要正式开课了，在开始之前，我想把美团王兴的那句话送给你：“**多数人为了逃避真正的思考，愿意做任何事情。**”

如今这个又“卷”又“急躁”的IT行业，竞争压力不小，毕业生大量涌入，大批非科班人士转行，面对浩瀚的人员，面试时不得不大力出奇招，先问算法过滤一波，再问技术底层过滤一波，然后问字节码汇编过滤一波，剩下来的就寥寥无几了。

但陷入“卷面试”的表象反而是最远的弯路，毕竟日常面对实际开发问题，才是见真章的时候，真正愿意慢下来沉心思考软件设计精髓思想的那批人，反而能走得更稳，走得更远！

加油吧，让我们一起携手，向着3个月后游刃有余掌握 Dubbo 的目标冲锋，助你轻松驾驭各种微服务框架。

戳此加入[专栏交流群](http://jinshuju.net/f/qZtw6l)。
<div><strong>精选留言（15）</strong></div><ul>
<li><span>Geek_895efd</span> 👍（7） 💬（2）<p>对目前微服务框架的迷惑，期待老师答疑：
1、目前的微服务框架：springCloud体系、阿里的springCloudAlibaba体系、以dubbo为主的微服务框架
2、对微服务框架的疑惑点一：国内主流微服务栈基本是springCloudAlibaba，我看阿里官方的描述，dubbo也是springCloudAlibaba的组成部分，那dubbo现在也有服务治理能力，是可以替代springCloudAlibaba吗
3、对微服务框架的疑惑点二：springCloudAlibaba技术栈包括nacos、springCloud gateway、sentinel、Sleuth、Seata等，那末duboo微服务治理包括哪些，还是说跟pringCloudAlibaba是重叠的
4、对微服务框架的疑惑点三：springCloudAlibaba、dubbo各自的定位是什么
5、对微服务框架的疑惑点四：springCloudAlibaba、dubbo各自的使用场景是什么


期待老师的答疑：不然，课程学下来，学的目的都不知道是什么</p>2023-01-12</li><br/><li><span>LVM_23</span> 👍（5） 💬（2）<p>老师，Dubbo我在其他地方看说是RPC框架，当前文中说是微服务框架。没理解好，求解惑，谢谢</p>2022-12-21</li><br/><li><span>Andy</span> 👍（3） 💬（1）<p>看了《开篇词》，可以看出这是老师对于学习之“道”、解决之“道”的干货分享，我是先大致浏览了一下，发现有很多细节点，也就是一些思维，非常重要，也很契合我之前所想的，比如“问题驱动学习”、“卷面试”、“凡是理解的都会记住，凡是不理解的都会忘记”，而后，又重新精读了一遍。

整篇写得非常诚恳，也很务实，可以说，是一切后续学习实践的真正前提，所谓“工欲善其事，必先利其器”。方向和方法对了，接下来只是行动就完了。很多人总想一下子扎进知识干货的海洋里，熟不知海洋里不会游泳，是会淹死的。正如那句古话所言“吾生也有涯，而知也无涯，以有涯随无涯，殆己”。

希望，想要学习任何知识，想要收获真正解决问题的能力的同学，应该好好先看看这篇开篇词。</p>2023-01-04</li><br/><li><span>陌兮</span> 👍（2） 💬（1）<p>赶上热乎的了。16年底开始用dubbo，不过后面通知不会维护了，加上换了公司，就开始用spring-cloud。现在所在的家公司又在用dubbo，再捡回来。</p>2023-01-03</li><br/><li><span>Evan</span> 👍（2） 💬（1）<p>Dubbo  源码篇是按3.0 以版解讲 ？Dubbo 确实非常优秀的，和Spring Cloud 相比缺少一定生态技术环境</p>2022-12-19</li><br/><li><span>码小呆</span> 👍（2） 💬（1）<p>我是第一?</p>2022-12-19</li><br/><li><span>张申傲</span> 👍（1） 💬（1）<p>老师的课程不仅内容质量高，还很励志~</p>2022-12-26</li><br/><li><span>Casin</span> 👍（1） 💬（1）<p>加油跟着老师一起学</p>2022-12-22</li><br/><li><span>小天</span> 👍（1） 💬（1）<p>工作里一直有用到Dubbo，但也就只知道怎么用。希望能通过学习了解更多</p>2022-12-21</li><br/><li><span>aoe</span> 👍（1） 💬（1）<p>目标是用 Dubbo 横着走</p>2022-12-21</li><br/><li><span>慎独明强</span> 👍（1） 💬（1）<p>最近在看dubbo的源码，希望这个课程可以从不同角度的有收获</p>2022-12-19</li><br/><li><span>胖虎</span> 👍（1） 💬（1）<p>加油！跟着老师学习</p>2022-12-19</li><br/><li><span>Berton 👻</span> 👍（1） 💬（1）<p>希望能有所收获</p>2022-12-19</li><br/><li><span>豪</span> 👍（1） 💬（1）<p>跟着老师一起学习</p>2022-12-19</li><br/><li><span>慕华</span> 👍（0） 💬（1）<p>解决通用问题的代码，怎么生气专利</p>2023-02-16</li><br/>
</ul>