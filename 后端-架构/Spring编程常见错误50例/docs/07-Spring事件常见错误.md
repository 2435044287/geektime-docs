你好，我是傅健，这节课我们聊聊Spring事件上的常见错误。

前面的几讲中，我们介绍了Spring依赖注入、AOP等核心功能点上的常见错误。而作为Spring 的关键功能支撑，Spring事件是一个相对独立的点。或许你从没有在自己的项目中使用过Spring事件，但是你一定见过它的相关日志。而且在未来的编程实践中，你会发现，一旦你用上了Spring事件，往往完成的都是一些有趣的、强大的功能，例如动态配置。那么接下来我就来讲讲Spring事件上都有哪些常见的错误。

## 案例1：试图处理并不会抛出的事件

Spring事件的设计比较简单。说白了，就是监听器设计模式在Spring中的一种实现，参考下图：

![](https://static001.geekbang.org/resource/image/34/c6/349f79e396276ab3744c04b0a29eccc6.jpg?wh=3634%2A2283)

从图中我们可以看出，Spring事件包含以下三大组件。

1. 事件（Event）：用来区分和定义不同的事件，在Spring中，常见的如ApplicationEvent和AutoConfigurationImportEvent，它们都继承于java.util.EventObject。
2. 事件广播器（Multicaster）：负责发布上述定义的事件。例如，负责发布ApplicationEvent 的ApplicationEventMulticaster就是Spring中一种常见的广播器。
3. 事件监听器（Listener）：负责监听和处理广播器发出的事件，例如ApplicationListener就是用来处理ApplicationEventMulticaster发布的ApplicationEvent，它继承于JDK的 EventListener，我们可以看下它的定义来验证这个结论：
<div><strong>精选留言（15）</strong></div><ul>
<li><img src="https://static001.geekbang.org/account/avatar/00/15/f4/8c/0866b228.jpg" width="30px"><span>子房</span> 👍（6） 💬（4）<div>异步可以定义一个线程池，或者试用eventlisenner加上异步注解</div>2021-05-28</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/18/6c/67/07bcc58f.jpg" width="30px"><span>虹炎</span> 👍（2） 💬（6）<div>老师，案例1的MyContextStartedEventListener可以自动加入到ApplicationEventMulticaster实现类SimpleApplicationEventMulticaster 中。案例2的MyApplicationEnvironmentPreparedEventListener也应该自动加入到SimpleApplicationEventMulticaster 中。为啥没有？单看文章，感觉没有讲透彻。也就是说为啥案例1的MyContextStartedEventListener 不用单独注册到initialMulticaster 广播体系中。没懂？？？</div>2021-05-15</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/1e/f5/97/9a7ee7b3.jpg" width="30px"><span>Geek4329</span> 👍（2） 💬（0）<div>事件异步处理有两种，一种发送方异步发送，另一种监听方异步监听。都可以</div>2021-05-10</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/ce/d7/074920d5.jpg" width="30px"><span>小飞同学</span> 👍（1） 💬（0）<div>思考题：在SimpleApplicationEventMulticaster实例化的时候，设置属性。或者使用@PostConstructor注解。  在想有没有啥其他的更优雅的方式?
@Bean
    public SimpleApplicationEventMulticaster testMulticaster(SimpleApplicationEventMulticaster caster) {
        caster.setErrorHandler(TaskUtils.LOG_AND_SUPPRESS_ERROR_HANDLER);
        caster.setTaskExecutor(Executors.newFixedThreadPool(10));
        return caster;
    }</div>2021-05-06</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/2b/48/fa/43dda02c.jpg" width="30px"><span>繁空</span> 👍（0） 💬（0）<div>MyContextRefreshedEventListener这个类里面少了日志的注解</div>2023-10-28</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/20/26/e7/1c2c341d.jpg" width="30px"><span>棒棒糖</span> 👍（0） 💬（0）<div>Spring 事件，往往完成的都是一些有趣的、强大的功能，例如动态配置。</div>2023-03-10</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/1f/af/b8/458866d3.jpg" width="30px"><span>Bo</span> 👍（0） 💬（0）<div>为什么案例一的listener不需要像案例二的listener一样，写到spring.factories文件里面？

“applicationEventMulticaster广播器生效的监..器：由上述提及的 ...&#47;spring.factories 中加载的监..器以及扫描到的 Appli...Listener 类型的 Bean 共同组成”，说明还会包括 ApplicationListener 类型的 Bean。</div>2023-03-03</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/1f/af/b8/458866d3.jpg" width="30px"><span>Bo</span> 👍（0） 💬（0）<div>为什么案例一的listener不需要像案例二的listener一样，写到spring_factories文件里面？</div>2023-03-03</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/2e/62/a0/e46bdbeb.jpg" width="30px"><span>粽</span> 👍（0） 💬（0）<div>老师有几个地方讲的其实是有点混乱的，我自己整理了一下，大家可以参考下https:&#47;&#47;blog.csdn.net&#47;Zong_0915&#47;article&#47;details&#47;126525246。</div>2022-08-25</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/1b/b8/21/f692bdb0.jpg" width="30px"><span>路在哪</span> 👍（0） 💬（0）<div>为什么案例一的listener不需要像案例二的listener一样，写到spring。factories文件里面</div>2022-07-05</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/1b/b8/21/f692bdb0.jpg" width="30px"><span>路在哪</span> 👍（0） 💬（0）<div>感觉脑子不够用</div>2022-07-05</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/2e/27/b4/df65c0f7.jpg" width="30px"><span>| ~浑蛋~</span> 👍（0） 💬（0）<div>从代码得知，有线程池的话就会把listener提交到线程池执行，所以设置multicaster的线程池就能异步执行事件监听逻辑了</div>2022-06-28</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/19/6b/e9/7620ae7e.jpg" width="30px"><span>雨落～紫竹</span> 👍（0） 💬（1）<div>为什么我很少在现在的java应用看到EventListener 这种我只在mq和 桌面级开发上看到过</div>2022-06-20</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/0f/46/2f/abb7bfe3.jpg" width="30px"><span>梁亚利</span> 👍（0） 💬（0）<div>使用SimpleAsyncTaskExecutor

 @Bean
    public ApplicationEventMulticaster applicationEventMulticaster(){
        SimpleApplicationEventMulticaster simpleApplicationEventMulticaster=new SimpleApplicationEventMulticaster();
        simpleApplicationEventMulticaster.setTaskExecutor(new SimpleAsyncTaskExecutor(&quot;event-task&quot;));
        return  simpleApplicationEventMulticaster;
    }</div>2022-03-23</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/21/11/c8/889846a7.jpg" width="30px"><span>黑白颠倒</span> 👍（0） 💬（1）<div>第二个不是很懂啊</div>2021-08-02</li><br/>
</ul>