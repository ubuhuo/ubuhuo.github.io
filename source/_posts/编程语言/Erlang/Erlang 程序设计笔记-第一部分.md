---
title: Erlang 程序设计笔记-第一部分
date: 2018/11/19
categories:
  - 编程语言
  - Erlang
tags:
  - Erlang 程序设计笔记
copyright: true
abbrlink: 76405d8b
---

## 第一章：什么是并发

在本书中，我们讲学习如何将 并行 活动描述为相互通信的多组 并行 进程，并学习如何编写**并发程序**。

在日常用语中，并发（concurrent）、同时（simultaneous）和并行（parallel）等词几乎表示同一个意思。但在编程语言中需要做更精确的区分。

如果只有一台单核的计算机，是无法在上面运行并行程序的。因为只有一个 CPU，而它一次只能做一件事。然而，可以在单核计算机上运行并发程序。计算机在不同任务之间分享时间，使人产生这些任务是并行运行的错觉。

### 1.1 给并发建模

我们将从一个简单的例子入手，为一种日常情景构建并发模型。设想我看见四个人出去散步，另外还有两条狗和一大群兔子。这些人正在相互交谈，而狗则想要追逐兔子。要在Erlang里模拟这些，需要编写四个模块，名字分别是person（人）、dog（狗）、rabbit（兔子）和world（世界）。person的代码会放在名为person.erl的文件里，看起来就像是这样：

```erlang
-module(person).
-export([init/1])

init(Name) -> ...
```

第 1 行 `-module(person).` 的意思是此文件包含用于 person 模块的代码。它应该与文件名一致（除了 `.erl` 这个文件扩展名）。**模块名必须以一个小写字母开头**。从技术上说，模块名是一个原子（atom）（关于原子的详细介绍，可参见 3.5 节）。模块声明之后是一条导出声明。导出声明指明了模块里哪些函数可以从模块外部进行调用。它们类似于许多编程语言里的 public 声明。没有包括在导出声明里的函数是私有的，无法在模块外调用。
`-export([init/1]).` 语法的意思是带有一个参数（`/1` 指的就是这个意思，而不是除以 1）的函数 `init` 可以在模块外调用。如果想要导出多个函数，就应该使用下面这种语法：方括号 `[ ... ]` 的意思是“列表”，因此这条声明的意思是我们想要从模块里导出一个函数列表。我们也会给 dog 和 rabbit 编写类似的代码。

#### 1.1.1 开始模拟

要启动程序，可以调用 `world:start()` 。它定义在一个名为 world 的模块里，这个模块的开头部分就像这样：

```erlang
-module(world).
-export([start/0]).

start() ->
	Joe        = spawn(person, init, ["Joe"]),
	Susannah   = spawn(person, init, ["Susannah"]),
	Dave       = spawn(person, init, ["Dave"]),
	Andy       = spawn(person, init, ["Andy"]),
	Rover      = spawn(dog,    init, ["Rover"]),
	...
	Rabbitl    = spawn(rabbit, init, ["Flopsy"]),
	...
```

`spawn` 是一个 Erlang 基本函数，它会创建一个并发进程并返回一个进程标识符。`spawn` 可以这样调用：

```erlang
spawn(ModName, FuncName, [Arg1, Arg2, ..., ArgN])
```

当 Erlang 运行时系统执行 `spawn` 时，它会创建一个新进程（不是操作系统的进程，而是一个由 Erlang 系统管理的轻量级进程）。当进程创建完毕后，它便开始执行参数所指定的代码。`ModName` 是包含想要执行代码的模块名。`FuncName` 是模块里的函数名，而 `[Arg1, Arg2, ...]` 是一个列表，包含了想要执行的函数参数。

`spawn` 的返回值是一个进程标识符（PID，Process IDentifier），可以用来与新创建的进程交互。

> **与对象类比**

> Erlang 里的模块类似于面向对象编程语言（OOPL，Object-Oriented Programming Language）里的类，进程则类似于 OOPL 里的对象（或者说类实例）。

> 在 Erlang 里，`spawn` 通过运行某个模块里定义的函数创建一个新进程。而在 Java 里，new 通过运行某个类中定义的方法创建一个新对象。

> 在 OOPL 里可以用一个类创建数千个类实例。类似地，在 Erlang 里我们可以用一个模块创建数千甚至数百万个执行模块代码的进程。所有 Erlang 进程都并发且独立执行，如果有一台百万核的计算机，甚至可以并行运行。

#### 1.1.2 发送消息

启动模拟之后，我们希望在程序的不同进程之间发送消息。**在 Erlang 里，各个进程不共享内存，只能通过发送消息来与其他进程交互**。这就是现实世界里的物体行为。

假设 Joe 想要对 Susannah 说些什么。在程序里我们会编写这样一行代码：

```erlang
Susannah ! {self(), "Hope the dogs don't chase the rabbits"}
```

`Pid ! Msg` 语法的意思是发送消息 `Msg` 到进程 `Pid`。大括号里的 `self()` 参数标明了发送消息的进程（在此处是Joe）。

#### 1.1.3 接收消息

为了让 Susannah 的进程接收来自 Joe 的消息，要这样写:

```erlang
receive
    {From, Message} ->
    ...
end
```

当 Susannah 的进程接收到一条消息时，变量 `From` 会绑定为 Joe，这样 Susannah 就知道消息来自何处，变量 `Message` 则会含此消息。

这里应该记住的关键一点是:**编程模型基于对现实世界的观察**

### 1.2 并发的益处

并发编程可以用来提升性能，创建可扩展和容错的系统，以及编写清晰和可理解的程序来控
制现实世界里的应用。

* 性能
* 可扩展性
* 容错性
* 清晰性

### 1.3 并发程序和并行计算机

尝试给出并发和并行这类术语的准确含义。

先来区分一下**并发程序**(也就是如果有并行计算机就可能会跑得更快的程序)和多核(或 CPU)的**并行计算机**

* **并发程序** 是一种用并发编程语言编写的程序。编写并发程序是为了提升性能、可扩展性和容错性。

* **并发编程语言** 拥有专门用于编写并发程序的语言结构。这些结构是编程语言的主要部分，在所有操作系统上都有着相同的表现。

* **并行计算机** 是一种有多个处理单元(CPU 或核心)同时运行的计算机。

Erlang 里的并发程序是由互相通信的多组顺序进程组成的。一个 Erlang 进程就是一个小小的虚拟机，可以执行单个 Erlang 函数。别把它和操作系统的进程相混淆。

要用 Erlang 编写一个并发程序，必须确定一组用来解决问题的进程。这种确定进程的做法被称为**并发建模**。它类似于在编写面向对象程序时确定所需对象的技艺。

现在我们了解了并发程序和并行计算机之间的区别。**并发性与软件结构有关，而并行性与硬件有关**。下面来看看顺序和并发编程语言之间的区别。

### 1.4 顺序和并发编程语言

**编程语言有两种:顺序和并发。**顺序语言被设计用于编写顺序程序，没有描述并发计算的语言结构。并发编程语言被设计用于编写并发程序，语言本身带有表达并发性的特殊结构。

在 Erlang 里，并发性由 Erlang 虚拟机提供，而非操作系统或任何的外部库。在大多数顺序编程语言里，并发性都是以接口的形式提供，指向主机操作系统的内部并发函数。

在 Erlang 里，**进程和并发是我们可以用来定型和解决问题的工具**。这让细粒度控制程序的并发结构成为可能，而用操作系统的进程是很难做到的。

## 第二章：Erlang 速览

在这一章里，我们会创建第一个并发程序。制作一个文件服务器，使其拥有两个并发进程:
一个进程代表服务器，另一个代表客户端。

### 2.1 Shell

请注意每一条表达式都必须以一个句号后接一个空白字符结尾。

#### 2.1.1 = 操作符

可以用=操作符给变量赋值(严格来说是给变量绑定一个值)，就像这样:
```erlang
1> X = 123.
123
2> X * 2.
246
```

不能重新绑定变量。Erlang是一种函数式语言，所以一旦定义了 X = 123，那么X永远就是123，不允许改变!

= 不是一个赋值操作符，它实际上是一个模式匹配操作符。(详情请参见3.3.2节)

如果你习惯了命令式语言，可能会对这个概念感到不可思议。在命令式语言里，变量其实是伪装起来的内存地址。

#### 2.1.2 变量和原子的语法

请注意 Erlang 的变量以大写字母开头。所以 X、This 和 A_long_name 都是变量。

以小写字母 开头的名称(比如 monday 或 friday )不是变量，而是符号常量，它们被称为原子(atom)。

### 2.2 进程、模块和编译

Erlang 程序是由许多并行的进程构成的。

进程负责执行模块里定义的函数。
模块则是扩展名为 `.erl` 的文件，运行前必须先编译它们。
编译某个模块之后，就可以在shell或者直接从操作系统环境的命令行里执行该模块中的函数了。

下面几节将介绍如何在 shell 或操作系统命令行里编译模块和执行函数。

#### 2.2.1 在 shell 里编译并运行 Hello World

制作一个带有以下内容的hello.erl文件:

```erlang
% hello.erl
-module(hello).
-export([start/0]).

start() ->
    io:format("hello world~n").
```

为了编译并运行它，我们从保存 hello.erl 的目录里启动 Erlang shell，然后执行下面的操作：

```powershell
$ erl
1> c(hello).
{ok,hello}
2> hello:start().
hello world
ok
3> halt().
$
```

`c(hello)` 命令编译了 hello.erl 文件里的代码。
`{ok, hello}` 的意思是编译成功。现在代 码已准备好运行了。
第2行里执行了 `hello:start()` 函数。第3行里停止了 Erlang shell。
				
在 shell 里进行操作的优点是只要平台为 Erlang 所支持，这种编译和运行程序的方法就一定可用。在操作系统的命令行里的操作可能会因平台的不同而有所差别。

#### 2.2.2 在 Erlang shell 外编译 

也可以在操作系统的命令行里编译和运行前一个例子中的代码，就像下面这样:

```powershell
$ erlc hell.erl
$ erl -noshell -s hello start -s init shop
hello world
```

erlc 从命令行启动了 Erlang 编译器。
编译器编译了 hello.erl 里的代码并生成一个名为 hello.beam 的目标代码文件。

`$erl -noshell ...` 命令加载了 hello 模块并执行 `hello:start()` 函数。
随后，它执行了 `init:stop()` ，这个表达式终止了Erlang会话。

**在 Erlang shell 之外运行 Erlang 编译器(erlc)是编译 Erlang 代码的首选方式。**

使用 erlc 的优点在于自动化。我们可以在 rakefile 或 makefile 内运行 erlc 来自动化构建过程。

### 2.3 你好，并发

如何编写并发程序呢?
**Erlang的基本并发单元是进程(process)。**
一个进程是一个轻量级的虚拟机，只能通过发送和接收消息来与其他进程通信。 

将要编写的第一个并发程序是一个文件服务器。要在两台机器之间传输文件，需要客户端和服务器两个程序。为了实现这一点，我们将制作两个模块：`afile_client` 和 `afile_server`。

#### 2.3.1 文件服务器进程

文件服务器由一个名为 `afile_server` 的模块实现。这里再提醒一下，进程和模块类似于对象和类。用于进程的代码包含在模块里，要创建一个进程，需要调用 `spawn(...)`，创建进程的实际操作由这个基本函数完成。

```erlang
% afile_server.erl
-module(afile_server).
-export([start/1, loop/1]).

start(Dir) -> spawn(afile_server, loop, [Dir]).

loop(Dir) ->
    receive
    {Client, list_dir} ->
        Client ! {self(), file:list_dir(Dir)};
    {Client, {get_file, File}} ->
        Full = filename:join(Dir, File),
        Client ! {self(), file:read_file(Full)}
    end,
    loop(Dir}.
```

这段代码的结构非常简单。如果略去大部分细节，它看起来就会像这样:

```erlang
loop(Dir) ->
    %% 等待指令
    receive
        Command ->
           ... 做点什么 ...
    end,
    loop(Dir).
```

这就是用 Erlang 编写无限循环的方法。变量 Dir 包含了文件服务器当前的工作目录。我们在这个循环内等待指令，接收到指令时我们会遵从，然后再次调用自身来获取下一个指令。

> 不用担心最后的自身调用，这不会耗尽栈空间。Erlang 对代码采用了一种所谓**“尾部调用”**的优化，意思是此函数的运行空间是固定的。这是用Erlang编写循环的标准 方式，只要在最后调用自身即可。

另一点要注意的是，loop 函数永远不会返回。在顺序编程语言里，必须要极其小心避免无限循环，因为只有一条控制线，如果这条线卡在循环里就有麻烦了。Erlang则没有这个问题。服务器只是一个在无限循环里处理请求的程序，与我们想要执行的其他任务并行运行。

现在仔细查看接收语句。回忆一下，它看起来就像这样:

```erlang
receive
    {Client, list_dir} ->
        Client ! {self(), file:list_dir(Dir)};
    {Client, {get_file, File}} ->
        Full = filename:join(Dir, File),
        Client ! {self(), file:read_file(Full)}
end,
```

这段代码的意思是如果接收到 `{Client, list_dir}` 消息，就应该回复一个文件列表;
如果接收到的消息是 `{Client, {get_file, File}}`，则回复这个文件。
作为模式匹配过程的一部分，`Client` 变量在收到消息时会被绑定。

这段代码非常紧凑，所以很容易忽略所发生的细节。这段代码里有三个要点需要加以注意。

* 回复给谁
所有接收的消息都包含变量 `Client`，它是发送请求进程的进程标识符，也是应该回复的对象。如果想要得到一条消息的回复，最好说明一下回复应该发给谁。就像在信件里写明姓名 和地址，如果不说明信件来自何处，就永远得不到回复。

* `self()` 的用法
服务器发送的回复包含了参数 `self()`(在这个案例里 `self()` 是服务器的进程标识符)。这个标识符被附在消息中，使客户端可以检查收到的消息的确来自服务器，而不是其他 13 某个进程。

* 模式匹配被用于选择消息
接收语句内部有两个模式。可以这样编写:

```erlang
receive
    Pattern1 ->
      Actions1;
    Pattern2 ->
      Actions2 ->
    ...
end
```

Erlang 编译器和运行时系统会正确推断出如何在收到消息时运行适当的代码。不需要编写任何的 `if-then-else` 或 `switch` 语句来设定该做什么。这是模式匹配带来的乐趣之一，会为你节省大量工作。

可以像下面这样在 shell 里编译和测试这段代码:

```powershell
1> c(afile_server).
{ok,afile_server}
2> FileServer = afile_server:start(".").
<0.47.0>
3> FileServer ! {self(), list_dir}.
{<0.31.0>,list_dir}
4> receive X -> X end.
{<0.47.0>,
  {ok,["afile_server.beam","processes.erl","attrs.erl","lib_find.erl","dist_demo.erl","data1.dat","scavenge_urls.erl","test1.erl",...]}
}
```

来看看相关细节。

```powershell
1> c(afile_server).
{ok,afile_server}
```

编译 `afile_server.erl` 文件所包含的 `afile_server` 模块。编译很成功，所以“编译”函数 `c` 的返回值是 `{ok, afile_server}`。

```powershell
2> FileServer = afile_server:start(".").
<0.47.0>
```

`afile_server:start(Dir)` 调用 `spawn(afile_server, loop, [Dir])`。这就创建出一个新的并行进程来执行函数 `afile_server:loop(Dir)` 并返回一个进程标识符，可以用它来与此进程通信。
`<0.47.0>` 是文件服务器进程的进程标识符。它的显示方式是**尖括号内由句号分隔的三个整数**。

> 每次运行这个程序时，进程标识符都会改变。因此，`<0.47.0>` 里的数字在不同的会话里
将会是不同的。

```powershell
3> FileServer ! {self(), list_dir}.
{<0.31.0>,list_dir}
```

这里给文件服务器进程发送了一条 `{self(), list_dir}` 消息。`Pid ! Message` 的返回值被规定为 Message ， 因 此 shell 打印出 `{self(),list_dir}` 的值即 `{<0.31.0>, list_dir}`。
`<0.31.0>` 是 Erlang shell 自身的进程标识符，它被包括在消息内，告知文件服务器应该回复给谁。

```powershell
4> receive X -> X end.
{<0.47.0>,
  {ok,["afile_server.beam","processes.erl","attrs.erl","lib_find.erl","dist_demo.erl","data1.dat","scavenge_urls.erl","test1.erl",...]}
}
```

`receive X -> X end` 接收文件服务器发送的回复。它返回元组 `{<0.47.0>, {ok, ...}`。 该元组的第一个元素 `<0.47.0>` 是文件服务器的进程标识符。第二个参数是 `file:list_dir(Dir)` 函数的返回值，它在文件服务器进程的接收循环里得出。

#### 2.3.2 客户端代码

文件服务器通过一个名为 `afile_client` 的客户端模块进行访问。这个模块的主要目的是**为了隐藏底层通信协议的细节**。


客户端代码的用户可以通过调用此客户端模块导出的 `ls` 和 `get_file` 函数来传输文件。这就**能够自由改变底层的协议而不会影响到客户端代码API部分。**

```erlang
%afile_cilent.erl
-module(afile_client).
-export([ls/1, get_file/2])

ls(Server) ->
    Server ! {self(), list_dir},
    receive
        {Server, FileList} ->
            FileList
    end.

get_file(Server, File) ->
    Server ! {self(), {get_file, File}},
    receive
        {Server, Content} ->
            Content
    end.
```

如果对比 `afile_clien` 与 `afile_server` 的代码，就会发现一种美妙的对称性。只要客户端 里有 `Server ! ...` 这类 send 语句，服务器里就会有 receive 模式，反之亦然。

现在要重启 shell 并重新编译所有代码，展示客户端和服务器如何共同工作。

```powershell
1> c(afile_server).
{ok,afile_server}
2> c(afile_client}.
{ok,afile_client}
3> FileServer = afile_server:start(".").
<0.43.0>
4> afile_client:get_file(FileServer,"missing").
{error,enoent}
5> afile_client:get_file(FileServer,"afile_server.erl").
{ok,<<"%%%-------------------------------------------------------------------\n%%% @author ryoma\n%%% @copyright (C) "...>>}
```

现在在 shell 里运行的代码和之前代码的唯一区别，是把接口程序抽象出来放入单独的模块里。我们隐藏了客户端和服务器之间消息传递的细节，因为没有其他程序对此感兴趣。

到目前为止，你看到了一个完整文件服务器的基础部分，但它尚未完成。还有很多的细节问题，涉及**启动和停止服务器、连接某个套接字(socket)等**，在这里不会提及。

从 Erlang 的角度看，如何启动和停止服务器，连接套接字，从错误中恢复等都是琐碎的细节。 问题的本质在于创建并行进程，以及发送和接收消息。

**在 Erlang 里用进程来构建问题的解决方案。思考进程的结构(也就是说哪些进程间相互有联系)，思考进程间传递的消息以及消息包含何种信息是思考和编程方式的中心。**

#### 2.3.3 改进文件服务器

我们开发的文件服务器包括了运行在同一台机器上的两个相互通信的进程，展示了编写并发程序所需的一些基本要素。对真正的服务器而言，客户端和服务器会运行在不同的机器上，所以 必须设法让**进程间的消息不仅能在同一个Erlang节点的进程之间传递，也能在物理上隔开的机器上的Erlang进程之间进行。**

在这一章里，我们了解了如何在shell里执行一些简单的操作，编译一个模块，以及用spawn、send和receive这三个基本函数创建一个简单的双进程并发程序。

### 2.4 练习

现在也许是个好机会来检查一下你对目前所做的有几分了解。

1. 启动并停止 Erlang shell。
2. 在 Erlang shell 里输入一些命令。不要忘了以句号和空白结束命令。
3. 对 hello.erl 做一些小小的改动。在 shell 里编译并运行它们。如果有错，中止E rlang shell 并重启 shell。
4. 运行文件客户端和服务器代码。加入一个名为 put_file 的命令。你需要添加何种消息?学习如何查阅手册页。查阅手册页里的file模块。

PS：Erlang 中文文档：[点击查看](https://erldoc.com/mod/file/?search=file:)