你好，我是胡夕。今天我来跟你聊聊CommitFailedException异常的处理。

说起这个异常，我相信用过Kafka Java Consumer客户端API的你一定不会感到陌生。**所谓CommitFailedException，顾名思义就是Consumer客户端在提交位移时出现了错误或异常，而且还是那种不可恢复的严重异常**。如果异常是可恢复的瞬时错误，提交位移的API自己就能规避它们了，因为很多提交位移的API方法是支持自动错误重试的，比如我们在上一期中提到的**commitSync方法**。

每次和CommitFailedException一起出现的，还有一段非常著名的注释。为什么说它很“著名”呢？第一，我想不出在近50万行的Kafka源代码中，还有哪个异常类能有这种待遇，可以享有这么大段的注释，来阐述其异常的含义；第二，纵然有这么长的文字解释，却依然有很多人对该异常想表达的含义感到困惑。

现在，我们一起领略下这段文字的风采，看看社区对这个异常的最新解释：

> Commit cannot be completed since the group has already rebalanced and assigned the partitions to another member. This means that the time between subsequent calls to poll() was longer than the configured max.poll.interval.ms, which typically implies that the poll loop is spending too much time message processing. You can address this either by increasing max.poll.interval.ms or by reducing the maximum size of batches returned in poll() with max.poll.records.
<div><strong>精选留言（30）</strong></div><ul>
<li><img src="https://static001.geekbang.org/account/avatar/00/0f/c7/dc/9408c8c2.jpg" width="30px"><span>ban</span> 👍（43） 💬（1）<div>老师，1、请问Standalone Consumer 的独立消费者一般什么情况会用到
2、Standalone Consumer 的独立消费者 使用跟普通消费者组有什么区别的。</div>2019-07-16</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/11/47/1b/64262861.jpg" width="30px"><span>胡小禾</span> 👍（34） 💬（2）<div>“当消息处理的总时间超过预设的 max.poll.interval.ms 参数值时，Kafka Consumer 端会抛出 CommitFailedException 异常”。


其实逻辑是这样：消息处理的总时间超过预设的 max.poll.interval.ms 参数值  导致了 Rebalance‘；
rebalance导致了 partition assgined 的consumer member变了；
导致原来的consumer 想要commit都没法commit 。（因为元信息,比如连的broker都变了）.

请老师指正下</div>2020-05-11</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/11/47/1b/64262861.jpg" width="30px"><span>胡小禾</span> 👍（24） 💬（1）<div>为啥自动commit 不会抛 CommitFailedException？</div>2020-05-11</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/11/fd/59/4416b40f.jpg" width="30px"><span>德惠先生</span> 👍（22） 💬（3）<div>希望老师可以更加具体的说说，rebalance的细节，比如某个consumer发生full gc的场景，它的partition是怎么被分配走的，重连之后提交会发生什么</div>2019-07-16</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/0f/c7/dc/9408c8c2.jpg" width="30px"><span>ban</span> 👍（11） 💬（2）<div>老师，我想问下max.poll.interval.ms两者session.timeout.ms有什么联系，可以说0.10.1.0 之前的客户端 API，相当于session.timeout.ms代替了max.poll.interval.ms吗？
比如说session.timeout.ms是5秒，如果消息处理超过5秒，也算是超时吗？</div>2019-07-16</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/17/1a/ad/faf1bf19.jpg" width="30px"><span>windcaller</span> 👍（9） 💬（1）<div>To use this mode, instead of subscribing to the topic using subscribe, you just call assign(Collection) with the full list of partitions that you want to consume.

     String topic = &quot;foo&quot;;
     TopicPartition partition0 = new TopicPartition(topic, 0);
     TopicPartition partition1 = new TopicPartition(topic, 1);
     consumer.assign(Arrays.asList(partition0, partition1));
 
Once assigned, you can call poll in a loop, just as in the preceding examples to consume records. The group that the consumer specifies is still used for committing offsets, but now the set of partitions will only change with another call to assign. Manual partition assignment does not use group coordination, so consumer failures will not cause assigned partitions to be rebalanced. Each consumer acts independently even if it shares a groupId with another consumer. To avoid offset commit conflicts, you should usually ensure that the groupId is unique for each consumer instance.

老师 standalone mode 是上面这段内容吗？</div>2019-07-31</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/a8/e2/f8e51df2.jpg" width="30px"><span>Li Shunduo</span> 👍（9） 💬（1）<div>假如broker集群整个挂掉了，过段时间集群恢复后，consumer group会自动恢复消费吗？还是需要手动重启consumer机器？</div>2019-07-16</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/05/db/1bd78419.jpg" width="30px"><span>有时也，命也，运也，如之奈何？</span> 👍（6） 💬（1）<div>老师kafka死信该怎么去实现的？
2.0之后增加了如下配置：
errors.tolerance = all
errors.deadletterqueue.topic.name = &quot;&quot;？</div>2019-07-30</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/17/1a/ad/faf1bf19.jpg" width="30px"><span>windcaller</span> 👍（6） 💬（1）<div>我没在kafka官网、stackoverflow 、google 找到任何关于 standalone kafka consumer的 例子，还望老师给个链接学习学习</div>2019-07-30</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/13/15/d5/6d66288b.jpg" width="30px"><span>极极</span> 👍（5） 💬（3）<div>老师您好，这边遇到个很奇怪的问题

开启第一个消费者的时候，正常消费

但是开启另一个后，触发了 rebalanced

这时候第一个消费者会报出如下错误：
The provided member is not known in the current generation

是因为第一个消费者被踢出了 generation，但是它不知道，还在继续消费提交位移，或者做着其他事情？这个其他事情可能是什么？

还有就是 rebalance发生的时候，消费者是立即暂停，还是会消费完整个poll？这时候coordinator会等他吗？还是直接踢出去？
</div>2020-02-22</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/11/04/0d/3dc5683a.jpg" width="30px"><span>柯察金</span> 👍（5） 💬（1）<div>老师，没有设置 group.id 话，会怎么样，系统会自动生成唯一的一个值吗</div>2019-11-08</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/0f/48/f3/65c7e3ef.jpg" width="30px"><span>cricket1981</span> 👍（5） 💬（1）<div>&quot;不幸的是，session.timeout.ms 参数还有其他的含义，因此增加该参数的值可能会有其他方面的“不良影响”，这也是社区在 0.10.1.0 版本引入 max.poll.interval.ms 参数，将这部分含义从 session.timeout.ms 中剥离出来的原因之一。&quot;---&gt;能细述一下不良影响吗？</div>2019-07-16</li><br/><li><img src="" width="30px"><span>crud~boy</span> 👍（4） 💬（2）<div>老师poll一批消息，多线程处理并且是手动处理，会不会每个线程速度不一致，会导致提交位移时，offset小得后提交，会有什么影响吗</div>2020-08-20</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/16/d5/7b/c512da6a.jpg" width="30px"><span>石栖</span> 👍（3） 💬（3）<div>关于这个错误，我这边很奇怪，consumer用的是自动提交的配置，但是也出现了这个错误。看错误应该是broker-2挂掉了，然后rediscovery。但是后面日志又说The coordinator is not aware of this member.再后面就是Commit cannot be completed 的错误了。我这里是有多个broker，然后采用的域名的方式，ip可能会变。老师通过这些日志，能给点建议吗？
错误日志：
2020-05-04 18:49:56.592  INFO 6 --- [ntainer#0-0-C-1] o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=consumer-2, groupId=test-group] Discovered group coordinator broker-2-lhfm0slmx1v4nyfz.kafka.svc01.local:9093 (id: 2147483645 rack: null)
2020-05-04 18:49:56.592  INFO 6 --- [ntainer#0-0-C-1] o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=consumer-2, groupId=test-group] Group coordinator broker-2-lhfm0slmx1v4nyfz.kafka.svc01.local:9093 (id: 2147483645 rack: null) is unavailable or invalid, will attempt rediscovery
2020-05-04 18:49:56.694  INFO 6 --- [ntainer#0-0-C-1] o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=consumer-2, groupId=test-group] Discovered group coordinator broker-2-lhfm0slmx1v4nyfz.kafka.svc01.local:9093 (id: 2147483645 rack: null)
2020-05-04 18:49:56.735 ERROR 6 --- [ntainer#0-0-C-1] o.a.k.c.c.internals.ConsumerCoordinator  : [Consumer clientId=consumer-2, groupId=test-group] Offset commit failed on partition test.topic-0 at offset 0: The coordinator is not aware of this member.
2020-05-04 18:49:56.735  WARN 6 --- [ntainer#0-0-C-1] o.a.k.c.c.internals.ConsumerCoordinator  : [Consumer clientId=consumer-2, groupId=test-group] Asynchronous auto-commit of offsets {test.topic-0=OffsetAndMetadata{offset=0, metadata=&#39;&#39;}} failed: Commit cannot be completed since the group has already rebalanced and assigned the partitions to another member. This means that the time between subsequent calls to poll() was longer than the configured max.poll.interval.ms, which typically implies that the poll loop is spending too much time message processing. You can address this either by increasing the session timeout or by reducing the maximum size of batches returned in poll() with max.poll.records.</div>2020-05-05</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/0c/c2/bad34a50.jpg" width="30px"><span>张洋</span> 👍（3） 💬（1）<div>老师有两个疑问：

1.相同的GroupId的Consumer 不应该就是同一个Consumer Group 组下的吗，或者有其他的区分条件，比如订阅的Topic不同？

2.如果这个standalone Consumer 再给他添加一个同组的standalone Conusmer，会发生什么？</div>2019-11-21</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/26/eb/d7/90391376.jpg" width="30px"><span>ifelse</span> 👍（2） 💬（2）<div>好像只说了如何避免出现异常，但是异常出现了怎么处理呢，有类似回滚什么的吗？会重复消费吗？</div>2021-08-01</li><br/><li><img src="" width="30px"><span>松鼠鱼</span> 👍（2） 💬（2）<div>老师，我的程序使用的是自动提交offset，其他interval等相关参数（max poll, session timeout等）都是默认的。消息消费的速度也算快，500条应该就是一秒的事儿。可是这样也还是偶尔会发生Offset commit failed异常，通常是在程序跑了一阵之后，请问您有没有遇到过类似的情况？这是不是和单一一个consumer同时消费多个partition有关？</div>2020-06-27</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/17/52/d8/123a4981.jpg" width="30px"><span>绿箭侠</span> 👍（1） 💬（1）<div>场景二，为什么只强调standalone 和 group之间可能会发生groupid相同，那group之间相同怎么样呢？ 是不是会在coordinator注册group时就能发现组之间重复了groupid ?!!!</div>2021-07-30</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/17/8b/4b/15ab499a.jpg" width="30px"><span>风轻扬</span> 👍（1） 💬（1）<div>
老师。在学习这一章的时候。今天正好碰到一个commitFailedException的问题。kafka是windows版本，0.10.0.0。kafka的持久化目录用的就是默认的&#47;tmp目录。用Idea开发项目时，会经常启停该kafka。今天我启动了一个消费者，生产者发送消息，消费者立马就报出了commitFailedException异常。提示的就是poll loop的处理时间过长，提示我修改session.timeout.ms的值。
我的疑问点：我停止了消费者程序，此时就会发生reblance。然后，我又启动消费者。此时再次发生reblance。应该是重新进行分区分配，不应该报出这个错误啊。我这个场景下，这个错误发生的根本原因是什么？望老师解惑 (我最后的解决方案是：把tmp目录全部删除，然后再启动消费者就可以了。)

极客没有追评的功能。只能再重复一遍问题。老师，我是自动提交，什么原因会出现以上的场景呢？忘了补充一句，我用的是：springboot，引入的是springboot整合kafka 的依赖</div>2020-05-21</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/10/b5/89/9a1b4dee.jpg" width="30px"><span>蛋炒番茄</span> 👍（1） 💬（4）<div> Synchronous auto-commit of offsets {=OffsetAndMetadata{offset=6236, leaderEpoch=null, metadata=&#39;&#39;}} failed: Commit cannot be completed since the group has already rebalanced and assigned the partitions to another member. This means that the time between subsequent calls to poll() was longer than the configured max.poll.interval.ms, which typically implies that the poll loop is spending too much time message processing. You can address this either by increasing max.poll.interval.ms or by reducing the maximum size of batches returned in poll() with max.poll.records.
我们这边有几个项目，当用原生的kafka客户端经常出现这个报错，max.poll.interval.ms是默认值300000，max.poll.records.是2000，但是实际上数据很少，每条数据处理的时间也很短。heartbeat.interval.ms是2000，session.timeout.ms是12000。为什么经常出现这个错误。
重点是，另外几个项目用的是spring-kafka却重来没有出现过这样的报错，相关配置差不多，业务场景差不多。求指点？怎么样避免这样的问题</div>2019-09-06</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/a5/56/d6732e61.jpg" width="30px"><span>小刀</span> 👍（1） 💬（1）<div>胡老师，手动提交时，当前后两次poll时间超过期望的max.poll.interval.ms时，会触发Rebalance。 那么假如是自动提交时，会触发Rebalance吗？
假如，手动提交场景，consumer消费端处理业务时间过长（特殊case导致的），发生了Rebalance，那么该consumer实例被踢出了，那么它永远‘死掉’了吗，还是会再次通过heartbeat检测让它复活？</div>2019-07-26</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/13/20/08/bc06bc69.jpg" width="30px"><span>Dovelol</span> 👍（1） 💬（4）<div>老师好，我上一个问题的具体描述是这样的，今天讲CommitFailedException的例子是调用consumer.commitSync();手动提交offset，确实当消息处理的总时间超过预设的max.poll.interval.ms时会报这个异常，但是如果是自动提交offset的情况下，也就是把enable.auto.commit=true，然后删除consumer.commitSync();代码，其它代码不变，也是max.poll.interval.ms=5s，然后循环中sleep(6s)，发现不会报异常并且会一直重复消费，想问下这是什么原因呢？</div>2019-07-16</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/10/d5/68/2201b6b9.jpg" width="30px"><span>归零</span> 👍（0） 💬（1）<div>老师，场景二出现的原因是如果你的应用中同时出现了设置相同 group.id 值的【消费者组程序】和【独立消费者程序】，还是说只要出现了设置相同的group.id就会出现呢？</div>2021-01-04</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/71/f6/5bcefe24.jpg" width="30px"><span>徐宁</span> 👍（0） 💬（1）<div>我在 spark-sql-kafka-0-10_2.11-2.4.0-cdh6.2.0 看到如下代码：
  private def strategy(caseInsensitiveParams: Map[String, String]) =
      caseInsensitiveParams.find(x =&gt; STRATEGY_OPTION_KEYS.contains(x._1)).get match {
    case (&quot;assign&quot;, value) =&gt;
      AssignStrategy(JsonUtils.partitions(value))
    case (&quot;subscribe&quot;, value) =&gt;
      SubscribeStrategy(value.split(&quot;,&quot;).map(_.trim()).filter(_.nonEmpty))
    case (&quot;subscribepattern&quot;, value) =&gt;
      SubscribePatternStrategy(value.trim())
    case _ =&gt;
      &#47;&#47; Should never reach here as we are already matching on
      &#47;&#47; matched strategy names
      throw new IllegalArgumentException(&quot;Unknown option&quot;)
  }
是不是说，Spark 中的kafkaDataSource 实现是来自于调用的 option 参数呢？</div>2020-07-22</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/17/8b/4b/15ab499a.jpg" width="30px"><span>风轻扬</span> 👍（0） 💬（1）<div>老师。在学习这一章的时候。今天正好碰到一个commitFailedException的问题。kafka是windows版本，0.10.0.0。kafka的持久化目录用的就是默认的&#47;tmp目录。用Idea开发项目时，会经常启停该kafka。今天我启动了一个消费者，生产者发送消息，消费者立马就报出了commitFailedException异常。提示的就是poll loop的处理时间过长，提示我修改session.timeout.ms的值。
我的疑问点：我停止了消费者程序，此时就会发生reblance。然后，我又启动消费者。此时再次发生reblance。应该是重新进行分区分配，不应该报出这个错误啊。我这个场景下，这个错误发生的根本原因是什么？望老师解惑    (我最后的解决方案是：把tmp目录全部删除，然后再启动消费者就可以了。)</div>2020-05-20</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/10/a7/e4/5a4515e9.jpg" width="30px"><span>成立-Charlie</span> 👍（0） 💬（2）<div>老师对于多线程消费的场景，如何保证消息的顺序呢。</div>2020-03-10</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/11/40/5e/b8fada94.jpg" width="30px"><span>Ryoma</span> 👍（0） 💬（1）<div>有两个问题：
1：请问消费者是不是只有：Consumer Group 及 Standalone Consumer 两类？
2：我在 Node 中，使用独立消费者配置相同的 groupId，启动了两个实例。使用生产者发布消息时，两个消费者实例都可以正常处理，跟老师上面说的有点不太一样呀？这个是由于客户端的不同导致的么？</div>2020-02-18</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/17/df/e5/65e37812.jpg" width="30px"><span>快跑</span> 👍（0） 💬（1）<div>老师你好，我在使用spark streaming消费kafka消息会遇到某个batch消费耗时长。请老师帮忙分析一下这个是什么原因，应该怎么去优化。谢谢老师。日志大体如下：
20&#47;02&#47;06 21:13:18 INFO KafkaRDD: Computing topic test_topic, partition 0 offsets 470985316 -&gt; 470988502
20&#47;02&#47;06 21:13:18 DEBUG NetworkClient: Disconnecting from node 1 due to request timeout.
20&#47;02&#47;06 21:13:18 DEBUG ConsumerNetworkClient: Cancelled FETCH request ClientRequest(expectResponse=true, callback=org.apache.kafka.clients.consumer.internals.ConsumerNetworkClient$RequestFutureCompletionHandler@bf99562, request=RequestSend(header={api_key=1,api_version=2,correlation_id=32,client_id=consumer-2}, body={replica_id=-1,max_wait_time=3000,min_bytes=1,topics=[{topic=test_topic,partitions=[{partition=0,fetch_offset=470986525,max_bytes=10485760}]}]}), createdTimeMs=1580994741183, sendTimeMs=1580994741183) with correlation id 32 due to node 1 being disconnected
20&#47;02&#47;06 21:13:18 DEBUG Fetcher: Fetch failed
org.apache.kafka.common.errors.DisconnectException
20&#47;02&#47;06 21:13:18 DEBUG NetworkClient: Initialize connection to node 2 for sending metadata request
20&#47;02&#47;06 21:13:18 DEBUG NetworkClient: Initiating connection to node 2 at host1:9092.</div>2020-02-07</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/11/3d/03/b2d9a084.jpg" width="30px"><span>Hale</span> 👍（0） 💬（1）<div>2019-12-23 16:43:56,367 consumer.py[line:792] WARNING Auto offset commit failed for group aff74e1e254e11ea9f47b827eb16d0ae: NodeNotReadyError: coordinator-1
2019-12-23 16:43:56,471 client_async.py[line:695] WARNING &lt;BrokerConnection node_id=coordinator-1 host=39.104.137.50:9093 &lt;connected&gt; [IPv4 (&#39;39.104.137.50&#39;, 9093)]&gt; timed out after 305000 ms. Closing connection.
2019-12-23 16:43:56,473 client_async.py[line:327] WARNING Node coordinator-1 connection failed -- refreshing metadata
2019-12-23 16:43:56,478 base.py[line:493] ERROR Error sending HeartbeatRequest_v1 to node coordinator-1 [[Error 7] RequestTimedOutError: Request timed out after 305000 ms]
2019-12-23 16:43:56,479 base.py[line:714] WARNING Marking the coordinator dead (node coordinator-1) for group aff74e1e254e11ea9f47b827eb16d0ae: [Error 7] RequestTimedOutError: Request timed out after 305000 ms.
出现上面的报错consumer就卡主了，不能接收数据了，是提交失败吧</div>2019-12-25</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/15/5f/b2/c4780c10.jpg" width="30px"><span>曹伟雄</span> 👍（0） 💬（1）<div>老师，请教2个问题，谢谢!
我想问下0.10.1.0之后的session.timeout.ms还有什么作用呢？
standalone consumer和group consumer在配置上如何区分?</div>2019-07-19</li><br/>
</ul>