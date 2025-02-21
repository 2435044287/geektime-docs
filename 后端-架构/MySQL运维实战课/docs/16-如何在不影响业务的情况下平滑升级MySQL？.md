你好，我是俊达。

这一讲我们来讨论下将MySQL升级到8.0最新版本的具体操作步骤。基于数据库的当前版本，升级的路径会有一些差异。MySQL支持相邻两个大版本的物理升级，比如从5.5升级到5.6，从5.6升级到5.7，从5.7升级到8.0，但是不支持跨大版本的升级，比如不能从5.6直接升级到8.0。在同一个大版本下，可以跨越多个小版本升级，比如从8.0.x升级到8.0.z。

版本的降级就比较麻烦了，无法直接从8.0降级到5.7，所以**在升级正式环境前，需要做好充分的评估和测试，尽量避免出现版本降级的情况。**

## 原地升级

这一讲我们提到的物理升级、原地升级，实际上指的都是同一个意思，也就是使用新版本的MySQL软件来启动数据库，但是数据目录中，数据文件是在老版本下创建的。

### MySQL 8.0原地升级小版本

#### 版本升级的操作步骤

我们先来看一下MySQL 8.0原地升级的操作步骤。我们已经先将相关版本的MySQL的二进制包下载并解压到/opt目录下。

```go
# tree /opt -d -L 1
/opt
├── mysql8.0 -> mysql-8.0.32-linux-glibc2.12-x86_64
├── mysql-8.0.32-linux-glibc2.12-x86_64
├── mysql-8.0.34-linux-glibc2.17-x86_64
├── mysql-8.0.35-linux-glibc2.17-x86_64
├── mysql-8.0.37-linux-glibc2.17-x86_64
└── mysql-8.0.39-linux-glibc2.17-x86_64
```
<div><strong>精选留言（2）</strong></div><ul>
<li><img src="https://thirdwx.qlogo.cn/mmopen/vi_32/Q3auHgzwzM59PTNiaDASVicbVaeWBU1WKmOgyHcqVtl85nDwAqDicib1EUKE2RRoU0x0vZctZO4kbPDUTTke8qKfAw/132" width="30px"><span>binzhang</span> 👍（1） 💬（3）<div>mysql官方不是不支持从mysql5.8 复制到mysql5.7 吗？</div>2024-09-25</li><br/><li><img src="https://thirdwx.qlogo.cn/mmopen/vi_32/Q0j4TwGTfTIT0yX4KniaW8OvxLFd59n9xPibUjOarQjQA6xb2uianwGuD4wg7Miaw51jMz7uictxFQ40ZZgpWzM9VQw/132" width="30px"><span>Geek_2e8a42</span> 👍（0） 💬（2）<div>老师，请问MySQL5.7升级到MySQL58.0之后，5.7是使用systemctl start mysqld的方式启动的，要如何把这个启动方式也改成8.0去启动呢？</div>2024-10-12</li><br/>
</ul>