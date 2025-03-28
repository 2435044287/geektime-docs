你好，我是葛俊。今天，我来和你聊聊VIM的使用技巧。

在“[特别放送 | 每个开发人员都应该学一些VIM](https://time.geekbang.org/column/article/144470)”这篇文章中，我和你详细介绍了VIM提高研发效能背后的原因。我推荐每个开发者都应该学一些VIM的原因，主要有两个：

- 独特的命令模式，可以大量减少按键次数，使得编辑更高效；
- 支持跨平台，同时可以在很多其他IDE、编辑器中作为插件使用，真正做到一次学习，处处使用。

VIM确实可以帮助我们提高效率，但面对这样一个学习曲线长而且陡的编辑器，我们很容易因为上手太难而放弃。所以，如何性价比高地学习VIM的使用技巧非常重要。

我推荐你按照以下三步，来高效地学习如何使用VIM：

1. 学习VIM的命令模式和命令组合方式；
2. 学习VIM最常用的命令；
3. 在自己的工作环境中使用VIM，比如与命令行环境的集成使用。

接下来，我们分别看看这三步吧。

## VIM的模式机制

VIM的基本模式是命令模式，在命令模式中，敲击主体键的效果不是直接插入字符，而是执行命令实现对文本的修改。

### 使用VIM的最佳工作流

在我看来，在命令模式下工作，效率高、按键少，所以我推荐你**尽量让VIM处于命令模式，使用各种命令进行工作。进入编辑模式完成编辑工作之后也立即返回命令模式。**

事实上，我们从命令模式进入编辑模式修改文件，之后再返回命令模式的全过程，就是一个编辑命令。它跟其他的命令，比如使用dd删除一行，并没有本质区别。接下来，我们一起看个具体的例子吧。

比如，我要在一行文字后面加上一个大括号，后面再写一行代码。开始编辑时，光标处于这一行的开头。

```
  config = 
^ 
```

修改的目标是这样：

```
  config = {
    timeout: 1000ms,
  }
```

在VIM中，我的操作是，首先敲击大写字母A，将光标移到这一行的末尾，并进入编辑模式：

```
 config = 
          ^
```

然后，输入“{ timeout: 1000ms,回车}”，在文件中插入内容。最后，敲击Esc键回到命令模式。编辑完成。

```
  config = {
    timeout: 1000ms,
  }
   ^
```

实际上，整个过程就是执行了一条“在本行末尾插入文字”的命令。整条命令的输入是“A{ timeout: 1000ms,回车}Esc”。虽然比较长，但仍然是一条文本编辑命令。

所以实际上，**我们在VIM中的工作，正是在命令模式里执行一条条的命令完成的**。理解了这一点，你就可以有意识地学习、设计命令来高效地完成工作了。

为了让命令更加高效，VIM还提供了强大的命令组合功能，使得命令的功能效果呈指数级增长。

### 命令的组合方式

在VIM中，有相当一部分命令可以扩展为3部分：

- 开头的部分是一个数字，代表重复次数；
- 中间的部分是命令；
- 最后的部分，代表命令的对象。

比如，命令3de中，3表示执行3次，d是删除命令，e表示从当前位置到单词的末尾。整条命令的意思就是，从当前位置向后，删除3个单词。类似的，命令3ce表示从当前位置向后，删除三个单词，然后进入编辑模式。

可以看到，命令组合的前两个部分比较简单，但第三个部分也就是命令对象，技巧就比较多了。所以接下来，我就与你详细介绍下到底有**哪些命令对象可以使用。**

其实，对命令对象并没有一个完整的分类。但我根据经验，将其总结为光标移动命令和文本对象两种。

**第一种是光标移动命令**。比如，`$`命令是移动光标到本行末尾，那么`d$`就表示删除到本行末尾；再比如，`4}`表示向下移动4个由空行隔开的段落，那么`d4}`就是删除这4个段落。

这一类命令功能很强大，我们也很熟悉了，但它有一个缺陷，就是选择的出发点始终是当前光标所在位置。而我们在处理文本的过程中，尤其是在编写程序的时候，光标所在位置常常是处于需要修改内容的中间，而不是开头。比如，我们常常在写一个注释字符串的时候，需要修改整个字符串：

```
    comment: 'this is standalone mode',
                          ^
```

如果使用上面的光标移动作为命令对象的话，我们需要执行bbbct’命令，也就是向左移动三个单词之后，再向右删除到第一个单引号的地方，操作起来很麻烦。

针对这种情况，VIM提供了第二种命令对象：**文本对象**。具体来说就是，用字符代表一定的文字单位。比如，i"代表两个双引号之间的字符串，aw表示当前的单词。使用这种文本对象很方便，比如上面的编辑例子，我们只需要命令ci"就可以实现，比bbbct’命令方便了很多。

具体来说，文本对象命令由两部分组成：

- 第一部分只能是字符i或者a，表示是否包含对象边界。比如，i"表示不包括两边的引号，而a"就包括引号；
- 第二部分选择比较多，表示各种不同的文本对象。比如

![](https://static001.geekbang.org/resource/image/17/22/17ef1f7dba10731966b0048dac716c22.jpg?wh=2996%2A1602)

如果你要查看完整的文本对象，可以使用:help text-objects。

这一组文本对象选择功能很强大，而且是VIM自带功能，不需要安装任何插件。神奇的是，不知道为什么，很多使用VIM很久的人都不知道这个功能。我个人把它叫作vip功能，如果你以前没有用过，可以在文件中输入命令vip看看效果。相信不会让你失望。

可见，VIM的命令操作及其组合功能非常强大，要高效使用VIM，我们就必须使用命令模式以及命令组合。我看到有些开发者使用VIM时，一上来就使用i命令进入编辑模式，然后在编辑模式中工作。但是，编辑模式的功能很有限，完全发挥不出VIM提供的高效文本编辑功能。

接下来，我们进入第二步，也就是学习VIM最常用的命令。

## 命令模式中的基础命令

VIM有大量的命令可供我们使用，这里我主要与你介绍针对单个文件编辑的命令，因为这是编辑工作最基础的部分，也是各种跨平台场景，尤其在其他IDE中通过插件使用VIM的方式的共性部分。

至于更多的命令和细节，你可以参考我的“[命令模式中的基础命令](https://jungejason.github.io/vim-commands/)”这篇博客，或者自行搜索查询。

接下来，我会按照以下编辑文件的逻辑顺序，来与你介绍关键知识点和技巧：

1. 打开文件，以及进行设置操作；
2. 移动光标；
3. 编辑文本；
4. 查找、替换。

### 第一组常用命令是，打开文件以及对文件的设置

这些命令包括打开文件、退出、保存、设置等。关于设置，如果在远端的服务器上，我常常运行一条命令进行基本设置：

```
:set ic hls is hid nu
```

其中，ic表示搜索时忽略大小写，hls表示高亮显示搜索结果，is表示增量搜索，也就是在搜索输入的过程中，在按回车键之前就实时显示当前的匹配结果，hid表示让VIM支持多文件操作，nu表示显示行号。

如果要关闭其中某一个选项的话，在前面添加一个no即可。比如，关闭显示行号，使用

```
:set nonu
```

### 第二组常用命令是，移动光标

VIM提供了非常细粒度的光标移动命令，包括水平移动、上下移动、文字段落的移动。这些命令之间的差别很细微。比如，w和e都是向右边移动一个单词，只不过w是把光标放到下一个单词开头，而e是把光标放到这一个单词结尾。

虽然差别很小，但正是这样的细粒度，才使得VIM能够让我们使用最少的按键次数去完成编辑任务。

### 第三组常用命令是，编辑命令

编辑工作中我们应该大量使用命令组合来提高效率。关于组合命令的威力，我建议你看下“[命令模式中的基础命令](https://jungejason.github.io/vim-commands/)”那篇博客，我与你详细解释了一个ct"的例子。

这里，**我重点与你分享一个设计命令的技巧**。VIM的取消命令u、重复命令.，都是针对上一个完整的编辑命令而言。所以，我们可以设计一个完整的编辑命令，从而可以使用重复命令.来提高效率。比如，你要把一段文本中的几个时间都修改成20ms，这段文本原来是

```
    timeout: 1000000ms,
    waiting: 300000ms,
    starting: 40000ms,
```

希望改变成

```
    timeout: 20ms,
    waiting: 20ms,
    starting: 20ms,
```

在VIM中有多种实现方法。一种还不错的方法是，使用修改命令c和移动命令l的组合，在每一行用一个命令搞定。具体操作是，将光标挪到1000000ms里数字1的地方，用c7l20命令对第一行进行修改，然后在第二行、第三行的相同位置分别使用c6l20和c5l20进行修改。

这个命令可以完成工作，但每条命令都不一样，不能重复。实际上，还有一种更好的方法。

你可以设计一条命令，在每一行重复使用。在这个例子里，这个命令是ctm20，意思是从当前位置删除到下一个字母m的位置，进入编辑模式，插入20，然后返回命令模式。

使用这条命令对第一行进行修改之后，你可以把光标挪到第二、三行使用重复命令.来完成编辑任务，如下所示。

![](https://static001.geekbang.org/resource/image/92/5e/92124bb53e6bfa3c5fb0c1f3d5273a5e.gif?wh=618%2A402)

当然了，你还可以使用正则表达式进行查找替换，但是远比上面这个重复命令要复杂。这个设计命令并重复使用的方法非常好用，建议你上手实践下。

关于重复命令，VIM另外还有一个录制命令q和重复命令@，功能更强大，但也比较重。如果你想深入了解的话，[可以参考这篇文章](https://xu3352.github.io/linux/2018/11/04/practical-vim-skills-chapter-11)。

### 第四组常用命令是，查找、替换

这组命令主要有/、\*和s等，很常用，网络上也有很多描述，这里我就不再详细介绍了。比如，我在“[命令模式中的基础命令](https://jungejason.github.io/vim-commands/)”这篇博客中详细介绍了s命令，可供你参考。

除了上述4种常见命令外，VIM中还有一种很强大的可视模式（Visual Mode），可以提高编辑体验。接下来，我们再一起看看。

### 可视模式

可视模式一共有3种，包括基于字符的可视模式、基于行的可视模式和基于列的可视模式，详见“[命令模式中的基础命令](https://jungejason.github.io/vim-commands/)”这篇博客中的介绍。

接下来，我以基于列的可视模式为例，带你看看可视模式是如何提高编辑体验的吧。

我们先来看看第一个例子。有一段文本，每一行都用var声明变量（比如var gameMode），现在我希望改成使用const来声明。我们来看看具体的实现方式。

![](https://static001.geekbang.org/resource/image/ca/63/ca00ba7234b33295581aa3ff04469963.png?wh=241%2A177)

第一步，把光标挪到第一行开头的 v 字符处，使用组合键Ctrl-v进入列可视模式，然后使用7j下移动七行，再用e向右移动一个单词，从而选中一个包含所有var的矩形区域。

![](https://static001.geekbang.org/resource/image/71/b4/71de442222209adc9b9c86813da264b4.png?wh=247%2A177)

第二步是开始编辑。输入c，删除所有的var，并进入编辑模式。

![](https://static001.geekbang.org/resource/image/1b/58/1bcd320829837a41bb513978b3432a58.png?wh=215%2A177)

第三步是输入const，并输入Esc完成编辑操作，回到命令模式，整个修改完成。

![](https://static001.geekbang.org/resource/image/37/e8/377269a6d42aa02d4d8f6ade37bf77e8.png?wh=252%2A176)

![](https://static001.geekbang.org/resource/image/58/ff/58aaa90b1938cd51464f6bdf2330acff.png?wh=252%2A174)

我们再来看看第二个例子。我希望在上面这段文字的每一行末尾都添加一个分号。具体的操作步骤如下所示：

首先，用$命令把光标挪到最后一行的末尾，然后Ctrl-v进入列模式，再用7j命令将光标挪到第一行，这时，每一行的末尾都被包含到了一个方块里。不过因为每一行的长度不同，所以方块没有显示完全。

![](https://static001.geekbang.org/resource/image/3c/ad/3cea82dcf9ba1c4a9053ccd4d5e839ad.png?wh=261%2A175)

然后，输入A命令，光标挪到每一行的末尾，并进入编辑模式。

![](https://static001.geekbang.org/resource/image/db/84/dbecf5cbb5b75cf002270c154abbdc84.png?wh=261%2A178)

最后，输入要插入的分号，输入Esc完成编辑。这样，每一行的末尾都添加了一个分号。

![](https://static001.geekbang.org/resource/image/be/8b/be6fa0acecd2dee663df3deb1929b68b.png?wh=269%2A178)

![](https://static001.geekbang.org/resource/image/a7/4b/a7ac6e2c639334b799058b2dd243c64b.png?wh=262%2A174)

最后，关于可视模式我还有三个比较有用的小贴士：

- 第一，在退出了可视模式之后，可以使用gv命令重新进入可视模式并选择上一次的选择。这个命令出乎意料得有用，因为我们常常需要对上一次的选择进行进一步的操作。
- 第二，进入可视模式之后，可以直接使用v、V、Ctrl-v在三种模式之间切换，而不需要退出可视模式再重新进入。比如，你一开始使用v进入了字符可视模式，这时发现你需要删除几行，就可以直接输入V进入行的可视模式。
- 第三，修改选取的区域范围。当你进入可视模式之后，光标默认处于所选区域的结尾处，你可以挪动光标调节选择区域的结尾部分。但，如果你想调节的是所选区域的开头部分，则可以使用命令o将光标跳到选择区域的开始部分，再次输入命令o光标又会跳到选择区域的末尾。一个更好玩的情况是，在列模式中，你可以使用小写o在对角线上跳转，大写O在水平方向跳转，从而可以灵活调整所选矩形的四个角。

以上就是命令模式中的常用基础命令了。理解了这些命令，再加上命令组合，你就可以比较高效地使用VIM进行文本编辑了。但，要更高效地使用VIM，我们还有一个问题没有解决：VIM虽然强大，但也只是整个工作环境中的一个工具，怎样才能在整个工作中高效地使用VIM技能呢？

所以，接下来我将带你探索如何寻找合适的VIM使用场景。

## 寻找合适的VIM使用场景

寻找合适的适用场景，常常包括两种情况，一是VIM不是主力IDE的情况，二是VIM与其他工具的集成。接下来，我们先看第一种情况。

### 我的工作环境中，VIM不是主力IDE怎么办？

这种情况下，我会建议你先看一下你的主力IDE是否支持VI模式。目前，绝大部分主流IDE都支持，而且大都做得不错。如果你的主力IDE中能使用VIM命令的话，那从VIM特有的命令模式就能让你收益颇丰。

从我的经验来看，我只有在Facebook那几年使用VIM作为主力IDE，其他时间使用的编辑器主要是Intellij的IDE系列和VS Code。在这些IDE里，我一直在使用VI模式，效果也都很好。

除了在主力IDE中使用VI模式外，你还可以选择把VIM只作为一个单纯的文本编辑器，需要强大的编辑功能时，临时用一下就可以。比如，我在写微信小程序的时候，一开始使用的是原生的微信开发工具，编辑功能不是太强。所以在遇到一些重量级的编辑工作时，我就打开VIM快速搞定，然后再回到微信原生开发工具IDE里继续工作。

### VIM能在Linux命令行环境中与其他工具结合的很好

Unix/Linux系统的一个设计理念是，每个工具都只做一个功能，并且把这个功能做到极致，然后由操作系统把这些功能集成起来。VI当初就是作为Unix系统里的编辑器而存在的，到了40年后的今天，虽然VIM已经可以被配置成为一个强大的IDE，但很大程度上依然是Unix/Linux系统的基础编辑器，仍然可以很方便地和Unix/Linux的其他工具集成。

为了帮助你理解，我再和你分享3个适用场景。

**第一个场景：使用VIM作为其他工具的编辑器。**

在大部分Linux系统里，默认的编辑器就是VIM。如果不是的话，可以使用如下命令来设置：

```
// 全局使用VIM
export EDITOR='vim'
```

**第二个适用场景：使用管道（Pipe）。**

管道是Linux环境中最常用的工具之间的集成方式。VIM可以接收管道传过来的内容。比如，我们要查看GitHub上用户xxx的代码仓的情况，可以使用下面这条命令

```
curl -s https://api.github.com/users/xxx/repos | vim -
```

curl命令访问GitHub的API，把输出通过管道传给VIM，方便我直接在VIM中查看用户xxx的代码仓的细节。

这里需要注意的是，在VIM命令后面有一个-，表示VIM将使用管道作为输入。

**第三个适用场景：在VIM中调用系统工具**

除了系统调用VIM和使用管道进行集成之外，我们还可以在VIM中反过来调用系统的其他工具。这里，我与你介绍一个最实用的命令：**在可视模式中使用!命令调用外部程序。**

比如，我想给一个文件里的中间几行内容前加上从1000开始的数字序号。

![](https://static001.geekbang.org/resource/image/f7/cc/f72d9d852cdca00c79544ef3a41dd3cc.png?wh=292%2A203)

在Linux系统里，我们可以使用nl这个命令行工具完成，具体的命令是nl -v 1000。所以，我们可以把这一部分文本单独保存为文件，使用nl处理后，再把处理结果拷贝回VIM中。

虽然可以达到目的，但过程繁琐。幸运的是，我们可以在VIM里面直接使用nl，并把处理结果插入VIM中，具体操作如下。

首先，使用命令V进入可视模式，并选择这一部分文字。

![](https://static001.geekbang.org/resource/image/69/37/6962fb43054994be72fcd30f89802437.png?wh=303%2A267)

然后，输入命令!，VIM的最后一行会显示’&lt;,’&gt;! ，表示由外部程序操作选中的部分。这时，我们输入nl -v 1000并回车。

![](https://static001.geekbang.org/resource/image/0d/7d/0dd3edaf5ca34ae776d1527fbbee547d.png?wh=307%2A265)

VIM就会把选择的文本传给nl，nl在每一行前面添加序号，再把处理结果传给VIM，从而完成了我们想要的编辑结果，也就是在所选的几行文字前面添加从1000到1006的序号。

![](https://static001.geekbang.org/resource/image/5a/fd/5a7aa3f398c73283234d386a603bc3fd.png?wh=380%2A270)

同时，在VIM的底部，会显示7 lines filtered，意思是VIM里面的7行文本被使用外部工具处理过。是不是很方便？

我每次使用这样的功能时都觉得很爽，因为这可以让我聚焦在工作上，而不会把时间花在繁琐的操作上。

以上就是3个最常用的VIM和其他工具集成的例子。这种工具的集成工作方式，可以大大提高单个工具所能带来的效率提升，我还会在后面的文章中与你详细讨论。

## 总结

在今天这篇文章中，我与你介绍了高效学习VIM的三个步骤，即：第一，学习VIM的命令模式和命令组合方式；第二，学习文本编辑过程中各个环节最常用的命令；第三，在自己的工作环境中找到适用VIM的场景。

事实上，我今天与你讲述的VIM命令和使用技巧，只是最常见的跨平台使用中的共性部分，对效能提升带来的效果也最直接、最明显。

但，这些远没有覆盖VIM的强大功能。比如，我没有与你讨论多文件、多Tab、多窗口编辑的场景，以及插件的话题。如果你需要的话，可以把VIM配置成一个类似IDE的开发环境，进入沉浸式的VIM使用体验。关于插件方面的内容，我推荐3个插件：

- [pathogen](https://github.com/tpope/vim-pathogen)。它是一个插件管理软件，很好地解决了VIM自带插件删除不理想的问题。关于pathogen的使用，你可以参考下[这篇文章](https://gist.github.com/romainl/9970697)。
- [nerdtree](https://github.com/scrooloose/nerdtree)，在VIM中添加文件夹管理的功能。
- [fugitive](https://github.com/tpope/vim-fugitive)，在VIM中添加查看、编辑Git内容的功能。它的功能简直是强大到变态。

其中，pathogen和fugitive的作者都是[Tim Pope](https://github.com/tpope)，一个VIM牛人。如果你想了解更多的插件的话，可以看看他的其他VIM插件。

不容置疑的是，VIM的学习曲线非常长，即使我已经使用了15年，仍然会时不时地学习到一些新东西。只要你愿意，就可以一直学习一直提高。

## 思考题

你想要分享的最炫酷的VIM技巧是什么呢？

感谢你的收听，欢迎你在评论区给我留言分享你的观点，也欢迎你把这篇文章分享给更多的朋友一起阅读。我们下期再见！
<div><strong>精选留言（11）</strong></div><ul>
<li><span>吴新星</span> 👍（4） 💬（2）<p>老师你好 在使用vim的过程中 一直被中文输入法困扰着：

在Insert模式下录入中文，然后进入Normal模式进行一些操作，这时候一定要把输入法切换成英文，否则录入的命令会被输入法拦截，当操作完成后又需要进入到Insert模式录入中文，这时候又需要切换输入法，多出来的两次输入法切换，比较影响效率

请问老师有好的解决方案吗（我在Mac环境下使用vim）
</p>2019-12-27</li><br/><li><span>Marvin</span> 👍（4） 💬（1）<p>gg到文档头，o插入行，yy复制行，p粘贴，s删除并进入编辑，a光标移动到当前字之后进入编辑，v&#47;ctrl+v视图选择，ctrl+i移动到行首进入编辑…喜欢vim，服务器无障碍，nice。</p>2019-10-24</li><br/><li><span>我来也</span> 👍（3） 💬（2）<p>我常用的几个小命令：
普通模式下的 zt zz zb
用于把当前行移动到窗口顶部&#47;中间&#47;底部。

再就是插入模式下的Ctrl+o，再结合zz。
从编辑模式临时切回普通模式，执行了一个命令后继续回到编辑模式。
避免按esc退出编辑模式。</p>2019-10-22</li><br/><li><span>二狗</span> 👍（1） 💬（3）<p>在Windows下用gvim学vim。d的组合键怎么用
我按d的组合键容易触发长按效果dd
比如我按dw 结果把整个文本全删了
按d（  结果触发d+shift 把光标后面都删了</p>2019-11-01</li><br/><li><span>于小咸</span> 👍（1） 💬（1）<p>发现了葛俊老师的个人博客！</p>2019-10-21</li><br/><li><span>Jxin</span> 👍（1） 💬（1）<p>刚好明天周末，开始照着练手。</p>2019-10-18</li><br/><li><span>搏未来</span> 👍（1） 💬（1）<p>看完发现我是小白，去学习了</p>2019-10-18</li><br/><li><span>三件事</span> 👍（0） 💬（2）<p>老师 在一个1000多行文件里 我需要在三个不同的方法里改代码，要来回切换这三个方法。有没有啥好的办法？我现在是用 m 进行标记，但感觉还是不方便。</p>2020-03-29</li><br/><li><span>Alvin-L</span> 👍（0） 💬（1）<p>我在其他通用编辑器里有这么个功能，ctrl+d是向下复制一行当前行内容。vim里的操作就要yyp，如何设置成ctrl+d同样功能呢，这个习惯了</p>2019-11-13</li><br/><li><span>Geek_a03aa5</span> 👍（1） 💬（0）<p>推荐两个高性价比插件

1. vim-surround 方便快捷地编辑成对的符号
2. easymotion 搜索并移动光标到指定位置</p>2021-12-27</li><br/><li><span>Rootrl</span> 👍（0） 💬（0）<p>关于那个可是模式多行编辑，老师演示的是VS 编辑器，其他终端中使用（比如MobaXterm)好像不行，选中多行后，按c想编辑，但只能编辑第一行</p>2022-05-25</li><br/>
</ul>