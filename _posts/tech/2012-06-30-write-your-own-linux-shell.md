---
layout: post
title: "Write your own linux shell"
category: tech
description: "A quick guide to write your own linux shell from zero. This is originally a documentation for my final project of Operating System class. I'm putting it here to share the knowledge with more people. WARNING: This passage is written in Chinese."
---

## 什么是shell
shell是提供操作系统内核级服务的一个用户调度接口，一般分为两种：命令行接口（Command-Line Interface, CLI）和图形化接口（Graphical User Interface, GUI）。对于我们常用的Windows系统来说，大家一般接触的最多的是GUI。使用鼠标指指点点就可以完成大部分的工作。然而在GUI出现之前，shell就是CLI的天下，即便是到了今天windows统治了PC大部分的市场，CLI的shell依然有他不可替代的优势——更加底层、更加接近问题的中心。

CMD命令行可以认为是Windows下的CLI shell，而在Unix/Linux下，其CLI shell通常被叫做terminal。在terminal里面，我们就需要通过输入各种命令来完成平常鼠标指指点点完成的操作，例如新建文件夹，重命名文件等。

既然shell是一个介于用户和系统内核之间的一个程序，那只要知道了如何调用一些系统函数，我们就可以实现一个我们自己的shell。由于linux开源的特性，我们可以很便捷的查到linux提供的系统级函数，实现也比较方便。事实上，linux下可供选择的shell也比较多，一般我们常用的shell是[Steven Bourne][1]编写的[BASH][2]（GNU Bourne-Again Shell）。而在windows下，由于其源代码并未开放，要为其实现一个完整功能的shell比较困难，但是我们仍然可以利用现有能够调用的函数来实现一个简单的shell。
## 实现shell的基本原理

前面我们说到shell主要的功能就是把系统函数封装一遍提供给用户调用。那么shell的基本工作流程就比较清楚了：接收用户输入->分析其命令->调用系统函数->输出运行结果。

这里我们主要分析调用系统函数这部分，以linux为例进行分析。我们知道linux是一个开源的系统，所有人都可以修改其源代码，或者为其添加小工具。所以在linux下，最基本的功能都是一个个独立的小程序，由不同的人编写。例如我们使用率极高的ls、cp、mv等命令，都是对应一个程序，放在系统环境目录下。当我们输入ls时，系统会在环境目录下面去寻找ls这个程序来运行。所以在编写linux的shell时，我们就可以直接调用这些小程序。

而除了这些系统命令，shell还应该实现一些内置的命令，例如使用的最频繁的cd命令。这个命令不是系统命令，在PATH路径下是无法找到此程序的，他的实现是在shell内部实现的。所以我们需要在我们的shell的代码里面单独判断这些内置命令进行实现。

综上所述，我们可以得到一个shell的主要工作流程如下图所示：

<img src="/images/write_your_own_linux_shell/p1.png">

【shell程序主要流程】
## linux下shell的具体实现
### 词法分析

在获取到了用户输入的命令后的第一步，就是要对指令进行分解和解析。对指令的分析，实际上是对词法分析的过程。在编译原理里面，[lex][3]和[yacc][4]是两个非常有名的词法解析工具。两者都有GNU的版本，分别叫做[flex][5]和[bison][6]。使用这两者来替代我们的词法分析工作是非常惬意的事，毕竟这是拿来写编译器的利器，用来应付我们这简单的shell完全不在话下。而且在BASH里面的确也是用到了词法分析和yacc这两个工具。

但是杀鸡焉用宰牛刀，我们只是想要实现一个简单的shell，并无过多的要求，而且lex的学习成本非常之高，要熟练使用需要很长的学习时间。所以我们这里就简单使用字符串处理来完成词法分析。

我们这里的词法分析主要任务是把输入的指令分解出命令和参数，然后判断是不是shell内部命令，如果是则进行特殊处理。否则就把命令和参数传给exec函数族进行小工具调用。其中还涉及到管道的“|”、重定向的“<”和“>”、后台程序的“&”和指令结束的“;”等特殊符号的处理。由于是手工处理词法分析，引号的处理就把它暂时忽略了，如果加上引号处理的话就会使代码变得非常臃肿，而且引号在shell命令中出现的频率非常之低。支持引号带来的“性价比”并不高。

词法分析的代码我们并没有单独做成一个模块，而是把他们融合在代码执行的过程中，这样虽然耦合性较高，但是使用起来较为方便。而且如果需要解耦的话，我们完全可以使用上面说到的flex来实现。

### shell内置命令的实现

前面说到cd和exit等命令是属于shell的内部命令，也即在PATH路径下是找不到相关的程序的（你可以尝试到你的linux的PATH目录下去搜索一下cd）。所以这一些特殊的命令是需要我们单独在shell里面实现的。在我们的shell里面我们内置了如下几个命令：cd（改变工作目录）、about（显示个性化说明信息）、exit（离开shell）。

这几个命令的实现都不难，cd可以调用chdir()函数（在[unistd.h][7]里）进行工作目录的切换，注意一下返回值的错误判断即可。about和exit命令就更加简单，about就输出一段说明文字即可，exit退出还需要注意各种内存的释放，不要忘记free掉malloc得到的空间造成内存泄漏。

<img src="/images/write_your_own_linux_shell/p2.png">

【图2】cd命令的实现
### linux命令的实现
如之前所述，linux内置程序命令的执行是直接调用PAHT路径下的程序来运行实现的。我们可以使用exec函数族（在unistd.h中）函数来进行调用。只需要把命令和参数分别传给这个函数，他就会自动在PATH目录下搜索相应的程序并且运行。但是这个函数族有一个缺点，就是他会替代掉我们这个进程的上下文，导致那个小程序运行完后整个程序就运行完了。直观点看这个问题就是，我在shell里面输入ls，当前目录下的文件显示出来之后整个shell就关闭了。这显然是不符合shell的要求的。

这样我们就要利用到linux下的一个神级系统函数[fork()][8]函数，他的功能是创建一个子进程，并且把当前程序的上下文环境复制到子进程里面。利用这个函数，我们在要调用一个命令时，先开一个子进程，在子进程里面调用exec函数族的函数，这样调用结束后结束的是子进程，并不会干扰到我们的shell父进程。

<img src="/images/write_your_own_linux_shell/p3.png">

【图3】linux命令处理流程

还有一个需要注意的地方，在调用了exec进行系统函数调用之后，用户可以对程序发送一系列信号。例如按住CTRL+C来中止当前程序的运行，这对程序来说是非正常结束，返回值不为0，需要捕获一下以备以后来进行处理。还有，当在shell内等待输入的时候捕获到了CTRL+C的中止信号，shell不应该被直接结束，按照BASH的风格是不做处理。所以我们也对程序进行一个[SIGINT][9]信号的捕获，捕获之后不做任何事。

<img src="/images/write_your_own_linux_shell/p3.png">

【图4】中止信号响应函数
## 管道、流重定向以及后台运行的实现
在BASH里面，可以使用“|”符号进行管道操作，即把此符号之前的命令的输出作为之后的命令的输入。还可以使用流重定向符“<”和“>”把输入或者输出流进行重定向到文件里面。还支持“&”让程序后台运行。我们自己的shell也需要实现这两个功能。

代码中通过pipe()函数来创建管道，创建之后父进程和子进程一个只能向管道写内容，一个只能向管道读内容。然后利用dup2()函数来把进程的输入流或者输出流重定向到管道里，这样就能实现管道的操作。实现的时候注意可以使用多个“|”来迭代进行管道操作，需要使用一个循环来处理。同时还要注意最后一个操作的输出流是标准输出（即屏幕），不需要重定向到管道里，需要特殊处理一下。

流重定向的处理比较简单，可以在一开始的时候就定义文件符，并且默认指向标准输入输出流。然后判断命令里是否存在流重定向符，如果存在则将文件符指向相应的文件。如此便可以使用文件输入输出统一输入输出方式，操作比较方便。

后台运行的处理方式更加简便，开始时先判断是否存在“&”字符，如果存在就把“&”在命令里删除并且把标识符置1，否则置0。然后父进程里面直接判断标识符，如果为1则不需等待子进程，否则就等待子进程即可。

<img src="/images/write_your_own_linux_shell/p3.png">

【图5】主函数中调用相应的操作
## 可以改进的地方

Linux版的shell基本实现BASH的大部分功能，但还是有小部分没有完全实现。例如上下箭头的历史记录以及TAB自动补全的支持。这个可以使用[readline][10]库来实现。还有就是上面提到的对于引号的支持，这个我暂时还没有花时间去思考，不过应该不难实现。
另外，在编写的过程中发现了[《UNIX环境高级编程》][11]这本神书，非常推荐看。


[1]: http://en.wikipedia.org/wiki/Stephen_R._Bourne
[2]: https://www.gnu.org/software/bash/
[3]: http://dinosaur.compilertools.net/lex/
[4]: http://dinosaur.compilertools.net/yacc/
[5]: http://dinosaur.compilertools.net/flex/
[6]: http://dinosaur.compilertools.net/bison/
[7]: http://pubs.opengroup.org/onlinepubs/7908799/xsh/unistd.h.html
[8]: http://man7.org/linux/man-pages/man2/fork.2.html
[9]: http://en.wikipedia.org/wiki/SIGINT_(POSIX)#SIGINT
[10]: http://cnswww.cns.cwru.edu/php/chet/readline/rltop.html
[11]: http://book.douban.com/subject/1788421/

