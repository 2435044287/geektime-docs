你好，我是 winter。

任何一段 Node.js 进程：从一段代码到完整程序 执行都会产生进程，这是一种现代多任务操作系统提供的机制。它是系统资源分配和调度的基本单位，也是操作系统对程序执行的一种抽象表示。

进程是程序的“实例”，这意味着同一段 Node.js 进程：从一段代码到完整程序 代码可能产生多个进程。

进程最重要的作用是它为程序代码提供了相互隔离的虚拟内存，正常情况下，无须担心不同进程访问同一内存地址而产生冲突。

但进程间并非无法通讯，我们可以利用操作系统提供的一些机制来让不同进程互相通讯，以达到多进程协同的目的。

进程是操作系统重要的底层基础设施，关于进程的更详细信息，你可以在专门的操作系统课程中学习，这节课我们仅做概要性介绍，给大家建立一个感性认知。

在操作系统中，我们可以使用一些工具来管理进程，此处我们以 unix 系统风格的 shell 命令行为例，结合 Node.js 视角，介绍基本的进程机制。

PS. unix 系统风格的 shell 命令行适用于绝大多数 Linux 、MacOS 环境，Windows 下也有多种方法可以获得类似的环境，比如可以使用 Windows 自带的 Linux 子系统，安装 mingw 或 cygwin 等跨平台环境。一般工程师的开发环境中，使用 git 命令行模式就可以了。

## 创建一个新的进程

执行任何代码都会创建新的进程。

我们考虑用 Node.js 执行以下代码：

```
setTimeout(()=>console.log("Finished!"), 60000);
```

利用定时器创建一个一分钟后执行的任务，这将会创建一个存在一分钟的进程。在这个进程结束之前，我们可以使用操作系统命令ps来查看这个进程：

```
ps 
```

执行此命令后，我们将会看到当前正在执行的进程列表，我们可以看到创建此进程的命令和进程 ID（ps：命令还可以使用不同参数看到更多信息，与本文无关不再赘述，请参考操作系统文档）。

当执行完自己预定的所有任务后（60 秒后的定时器），这个进程会自动结束。我们还可以强行杀死这个进程.

```
kill -9 {进程id}
```

需要注意的是，我们不能把 Node.js 看做纯粹的 JS 代码。一个 Node.js 应用程序执行后，首先是一个操作系统中的进程，然后才是一个用 JavaScript 编写逻辑的进程。所以我们必须把系统编程相关的知识放在更重要的位置去学习。

## 进程的标准输入输出

在操作系统中，进程有标准输入和标准输出。

Node.js 中，可以用 `process.stdin`和 `process.stdout`来处理进程的输入输出。

```
// 使用 process.stdout 进行标准输出
process.stdout.write('请输入你的名字: ');

// 监听标准输入
process.stdin.on('data', (data) => {
    // 输出用户输入的信息
    process.stdout.write(`你好, ${data.toString().trim()}!\n`);
    
    // 结束进程
    process.exit();
});
```

process.stdin 是一个可读流（Readable Stream），我们在后面的课程中再来详细讲解关于流的知识。

默认情况下，进程的标准输入输出都绑定到运行的命令行环境上。但实际上，标准输入输出有丰富的利用方式。最基本的方法是重定向输入输出到文件。

```
node my.js >output.txt <input.txt
```

这里我们通过命令行的重定向运算符，使得进程的输入被重定向为 input.txt。

我们还可以利用 | 运算，把一个程序的标准输出给到另一个程序的标准输入：

```
node my1.js | node my2.js
```

而在操作系统中，“文件”其实是个相当宽泛的概念，我们甚至可以通过 /dev 目录来访问一些硬件设备。

这意味着，**当我们编写代码时，不应该假设标准输入输出的类型。如果我们要做一些依赖特定输入输出环境的程序，则应该在代码中做嗅探。**

在后面的课程中我们再来详细讨论针对不同的输入输出类型，如何进行更精细的处理。

## 参数与返回值

在操作系统中，创建进程的可执行程序其实更像是一个函数，所以有些语言会有“入口函数”的概念。NodeJS 的设计则是依照 JavaScript 习惯，整个文件作为一个段可执行代码或者模块。

**但是我们仍然需要处理进程的参数和返回值。**通常用 process.argv 来处理进程的参数。process.argv 是一个数组，包含了启动 Node.js 进程时传递的命令行参数。

假设我们用以下命令启动一个 Node.js 进程：

```
node ./my.js arg1 arg2
```

我们可以直接在 JS 代码中查看:

```
console.log(process.argv); //[ '/usr/local/bin/node', '/Users/winter/my.js', 'arg1', 'arg2' ]
```

可以看到，`argv` 中包含了四个参数。注意这里前两个参数固定为 Node.js 的路径和当前执行的 JS 路径。

如果我们使用 Node.js 时，给 Node.js 本身加上了参数，并不会影响 argv。

```
node --harmony my.js arg1 arg2
//my.js
console.log(process.argv);
```

此时执行结果为：

```
[ '/usr/local/bin/node', '/Users/winter/my.js', 'arg1', 'arg2' ]
```

有时我们会把 Node.js 代码直接包装成可执行文件(文件名为 my):

```
#/usr/bin/env node
console.log(process.argv);
```

执行时命令为：

```
./my arg1 arg2
```

此时执行结果为：

```
[ '/usr/local/bin/node', '/Users/winter/my', 'arg1', 'arg2' ]
```

所以可以知道，我们能够放心地使用 `process.argv.slice(2)`来获取所需要的参数，而无需关心 Node.js 代码调用时实际使用的命令。

我们通常可以用全局变量 `__dirname` 来获取当前 JS 文件所在的目录，如果你不喜欢非标准的写法，也可以在入口模块利用`process.argv`。

```
import process from "node:process";
import path from "node:path";
console.log(path.dirname(process.argv[1]));
```

> PS. 注意两者区别，在被 import 或者 require 的模块中，process.argv 获取的是入口 js 文件的路径。

对于返回值处理，则非常简单，调用 `process.exit()`即可。

```
process.exit(1);
```

按照一般的操作系统程序编程习惯，返回 0 表示进程执行成功，非 0 则代表进程执行失败。

通常非 0 的结果代表不同的错误类型，如果我们用 Node.js 编写了一个小工具，应该在文档中写明非 0 返回值对应的错误原因。

**shell 脚本可能会使用 Node.js 程序的返回值，所以请务必遵循操作系统的潜规则。**例如：

```
# 运行 app.js 并捕获返回码
node app.js

# 获取上一个命令的退出码
exit_code=$?

# 根据退出码进行不同处理
if [ $exit_code -eq 0 ]; then
    echo "✅ Node.js 脚本成功执行！"
else
    echo "❌ Node.js 脚本执行失败，退出码: $exit_code"
    exit $exit_code  # 传递错误码给外部
fi
```

## 环境

process.cwd() 返回当前工作目录（Current Working Directory）。

这是指执行 Node.js 进程时所在的目录，而不是当前脚本文件所在的目录。注意与 \_\_dirname 区分。

```
console.log('Current Working Directory:', process.cwd());
```

process.env 是一个对象，包含了当前进程的环境变量。

```
console.log('Environment Variables:', process.env);
console.log('NODE_ENV:', process.env.NODE_ENV);
```

操作系统中环境变量本身来源非常复杂，有些来自操作系统中的应用程序，有些来自当前控制台的初始脚本设置，你甚至可以为单独的某次运行来指定一些环境变量，例如：

```
NODE_ENV=production node app.js
```

在编写系统工具时，通常会用环境变量来提供丰富的配置选项，以符合用户习惯。

我们平时使用的各种 Node.js 编写的工具，也多数会依赖一些环境变量。如 `NODE_ENV` 就是多数工具用以区分生产环境和开发环境的变量。

如果我们要编写命令行工具，也应该入乡随俗，关注这些环境变量。

## 事件循环

Node.js 设计 JS 运行环境时，保持了 JS 文件最外层语句直接执行的特性。而 JavaScript 语言本身对多线程运行并不友好。

大部分常驻执行的进程，都不会彻底占满 CPU，更多时候处于等待资源的状态。所以事件循环其实是一个非常常见的多任务操作系统程序设计的模式。我们可以在 Windows 的 C++ 教程中看到类似的结构——消息循环：

```
MSG msg = { };
while (GetMessage(&msg, NULL, 0, 0) > 0)
{
    TranslateMessage(&msg);
    DispatchMessage(&msg);
}
```

在几乎所有系统级编程语言中都会有类似的结构，对于 C++ 这样比较底层的语言来说，消息循环由使用者实现，但对 Node.js 来说，事件循环完全由 Node.js 底层控制。

事件的最终来源是多样的，可能是用户输入、定时器、操作系统、硬件、其它进程。我们无需深究究竟有哪些事件类型，只需要关注上层 JS 代码的影响即可。

等待事件的过程中，实际上已经把 CPU 的控制权让渡给了操作系统，此时进程仍然在运行，但是 CPU 处于空闲状态，操作系统会进行调度，允许其它进程使用 CPU 资源。

当 Node.js 底层接收到事件时，如果判断需要执行 JavaScript，则会在 JavaScript 引擎中执行对应的代码。

## 进程协作

#### 1. child\_process spawn

在 Node.js 中，我们还可以使用多个进程来进行协作。

你可以通过 spawn() 启动另一个 Node.js 进程来执行脚本。我们能够直接操作子进程的标准输入和输出，这意味着我们实现了让多个进程有效地协作。

```
const { spawn } = require('child_process');

// 启动 Node.js 子进程执行 myScript.js
const child = spawn('node', ['myScript.js']);

// 监听子进程的标准输出
child.stdout.on('data', (data) => {
  console.log(`stdout: ${data}`);
});

// 监听子进程的退出事件
child.on('exit', (code) => {
  console.log(`child process exited with code ${code}`);
});
```

对于 spawn 创建的子进程，我们可以通过 kill 给它发送信号。

spawn 允许我们任意创建子进程，并通过操作系统提供的方式对它进行控制。但是当我们希望用 Node.js 做一个完整的进程集群进行更复杂的进程协作时，就显得不太够用了。这时候我们可以使用 fork。

#### 2. child\_process fork

fork 允许我们创建一个 worker 进程，它具有 Node.js 内置的消息机制，除了操作系统提供的通讯机制以外，我们还能够进行消息通讯。

```
const { fork } = require('child_process');

// 启动子进程
const worker = fork('./worker.js');

// 向子进程发送消息
worker.send('Hello from parent');

// 监听子进程发送的消息
worker.on('message', (message) => {
  console.log('Received from worker:', message);
});

// 监听子进程的退出事件
worker.on('exit', (code) => {
  console.log(`Worker exited with code ${code}`);
});
// worker.js
// 监听父进程发送的消息
process.on('message', (msg) => {
  console.log('Worker received message:', msg);
  // 向父进程发送消息
  process.send('Hello from worker');
});

// 向父进程发送初始消息
process.send('Worker is ready');
```

#### 3. cluster

fork 创建的工作进程可以很好地分配 CPU 资源。但有时候，我们又需要在进程间共享一些资源，比如网络端口号，这就要用到另一种创建子进程的方案——cluster。

```
const cluster = require('cluster');
const http = require('http');
const os = require('os');

const numCPUs = os.cpus().length; // 获取系统的 CPU 核心数

if (cluster.isMaster) {
  // 如果是主进程，创建与 CPU 核心数相等的工作进程
  console.log(`Master process is running on PID: ${process.pid}`);

  for (let i = 0; i < numCPUs; i++) {
    cluster.fork(); // 启动一个子进程
  }

  // 监听工作进程退出事件
  cluster.on('exit', (worker, code, signal) => {
    console.log(`Worker ${worker.process.pid} died`);
  });
} else {
  // 如果是工作进程，创建一个 HTTP 服务器
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end('Hello from worker process!\n');
  }).listen(8000);

  console.log(`Worker process started with PID: ${process.pid}`);
}
```

cluster 的 fork 是非常特殊的一种机制，工作进程和主进程共享同一份代码。

而同样的模块，主进程和工作进程中行为会有变化。比如 cluster 模块本身，在工作进程中根本没有 fork 方法，这杜绝了死循环创建工作进程。

而工作进程中的 http 模块，行为也有较大变化，这些工作进程不再会独占网络端口，而会形成一个进程集群，均匀地消费网络端口的请求，在没有 cluster 模块前，要做到这个非常困难。

## 结语

学习 Node.js，**首先要面对的是从浏览器环境到系统环境的思维转变**，如果我们抱着“都是 JS，只是 API 变化”的心态去学习 NodeJS，那么一定不会好的效果。

在本节课中，我们讲解了操作系统中进程的一些基础知识，以及与之对应的 Node.js 相关 API。我们已经了解了如何通过输入输出、参数返回值、环境变量等跟操作系统交互。此外，我们学习了 Node.js 体系中的进程协作方案，了解了如何在 Node.js 环境中创建一系列进程来共同完成一定的任务。

在后面的课程中，我们将会继续深入探讨 NodeJS 在工具和网络应用场景下的必要知识和技巧。