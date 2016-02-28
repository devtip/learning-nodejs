## Introduction
Welcome to Learning Node.js. Node.js is an exciting new platform for writing network and web applications that has created a lot of buzz(流言) over the past couple of years and rapidly gathered a　sizeable following in the developer community. In this book, I teach you more about it, why it is special, and get you up and writing Node.js programs in short order(短期内). You’ll soon find that　people are rather flexible with the name of Node.js and will refer to it frequently as just Node　or even “node.” I certainly do a lot of that in this book as well.

欢迎学习Node.js,Node.js是一个编写网络和Web应用的激动人心的新平台！过去几年，它总是“绯闻”不断，并且在社区里聚集了大量的人气！本书中，我将会教会你更多关于它的内容，它为何如此特别，与及开发者为何钟情于它，并让你可以在短期内上手Node.js程序。你很快就发现开发者更喜欢为它起一个简洁的名字，Node或者"node"。当然，我尽最大的努力介绍它！


##　Why Node.js?
Node.js has arisen for a couple of primary reasons, which I explain next.

下文将会解释促使Node.js的兴起的一系列主要原因.

### The Web(Web)
In the past, writing web applications was a pretty standard process. You have one or more servers on your machine that listens on a port (for example, 80 for HTTP), and when a request is received, it forks(分叉) a new process(进程) or a thread(线程) to begin processing and responding to the query.

过去，编写Web应用程序是一个非常标准的过程。你机器上有一个或多个服务器侦听端口 (例如，http的80端口)，当服务器收到一个请求时，您的机器上会 forks(分叉，你可以理解为创建，或者开的意思) 新的 process(进程) 或 thread(线程)，开始处理查询并和响应查询。

This work frequently involves communicating with external services, such as a database, memory cache, external computing server, or even just the file system. 

这项工作频繁地涉及到与外部服务的通信，如数据库，内存缓存，外部计算服务器，甚至只是文件系统。

When all this work is finally finished, the thread or process is returned to the pool of “available” servers, and more requests can be handled.

当这项所有涉及这项工作的部分最终完成时，线程或进程返回到“可用”服务器池中，这样才使得它可以继续处理更多的请求。



It is a reasonably linear process(合理的线性过程), easy to understand, and straightforward(直接) to code.

这种Web应用架构模型是合理的线性过程，易于理解、并且编码变得更为直接。



There are, however, a couple of disadvantages that continue to plague(困扰) the model:
但是,这种模型是有一些缺点一直“困扰”着它:

- Each of these threads or processes carries some overhead(开销) with it. On some machines, PHP + Apache can take up as much as 10–15MB per process. Even in environments where a large server runs constantly and forks threads to process the requests, each of these(ps: 线程) carries some overhead to create a new stack and execution environment, and you frequently run into the limits of the server’s available memory.
- In most common usage scenarios where a web server communicates with a database, caching server, external server, or file system, it spends most of its time sitting around doing nothing and waits for these services to finish and return their responses. While it is sitting there doing nothing, this thread is effectively “blocked” from doing anything else. The resources it consumes(消耗) and the process or thread in which it runs are entirely frozen waiting for those responses to come back.

每一个线程或进程都携带着一些开销。在某些PHP + Apache机器上，每个进程携带者多达10–15MB的开销。即使是无休止运行的大型服务器环境中，为处理请求进行fork线程操作，每个线程携带者创建一个新的堆栈和执行环境的开销，你将会经常遇到服务器可用内存的限制。

最常见使用场景是，Web服务器与数据库、缓存服务器、外部服务器或文件系统“打交道”，Web服务器大部分时间都无所事事地等待这些服务的完成，并返回它们的响应内容。Web服务器虽然无所事事，这个线程是有效的“阻塞”其它操作。资源请求的内存消耗，运行中的进程或线程的被完全冻结，只是为了等待着响应的返回。


Only after the external component(组件) has finally sent back its response will that process or thread be free to finish processing, send a response to the client, and then reset to prepare for another incoming(接踵而来的) request.

当外部组件最终返回响应时，进程或线程的“冻结”状态将会被解除，然后向客户端发送响应，接着再准备接踵而来的的请求。

So, although it’s pretty easy to understand and work with, you do have a model that can be quite inefficient if your scripts spend most of their time waiting for database servers to finish running a query—an extremely common scenario for a lot of modern web applications.

尽管这样的模型易于理解并跟它"打交道"！当编程脚本花费大量的时间等待数据库服务器完成查询操作的响应，这种模型显得是非常低效的！对于很多现代Web应用，这是一种极为常见的场景。


Many solutions to this problem have been developed and are in common use. You can buy ever bigger and more powerful web servers with more memory. You can replace more powerful and feature-rich HTTP servers such as Apache with smaller, lightweight ones such as lighttpd or nginx. You can build stripped-down or reduced versions of your favorite web programing language such as PHP or Python. (Indeed, Facebook has taken this one step further and built a
system that converts PHP to native C++ code for maximal speed and optimal size.) Or you can throw more servers at the problem to increase the number of simultaneous(同时发生的) connections you can accommodate(容纳).

解决阻塞这个问题的方案已被开发出来并得到广泛地适用。花更多的钱购买更多的服务器;将现有服务器更换为更强大、功能丰富的HTTP服务器，比如使用更小，轻量的Lighttpd和Nginx替换Apache。你可以基于开源代码DIY出对应的网络编程语言(如PHP、Python)的版本版本减少。(事实上，Facebook已经迈出了这一步，并建立了一个系统将PHP代码原生C++代码，视图达到最大的运行速度和最优的性能体验);或者你可以租用更多的服务器来容纳请求的同时连接数。


## New Technologies
Although the web developers of the world have continued their eternal struggle against server resources and the limits on the number of requests they can process, a few other interesting things have happened in the meantime.

尽管全世界的Web开发人员通过限制请求数来提供服务器资源服务，与此同时，一些有趣的事情发生了。


JavaScript, that old (meaning 1995 or so) language that came to be most well known (and frequently reviled) for writing client-side scripts in the web browser, has been growing in popularity again. Modern versions of web browsers are cleaning up their implementations of it and adding in new features to make it more powerful and less quirky. With the advent of client libraries for these browsers, such as jQuery, script.aculo.us, or Prototype, programming in JavaScript has become fun and productive. Unwieldy APIs have been cleaned up, and fun, dynamic effects have been added.

JavaScript，这门旧的(1995年左右被创建)的编写客户端脚本的编写语言，它人气很高，却经常被辱骂，真让人抱打不平，可喜的是，已经越来越流行了。现代Web浏览器正在清理它那些糟糕的API实现,并带来全新的功能，这使得JavaScript变得更强大，而不至于让人感觉它是一个另类的存在。随着为浏览器量身定制的客户端库出现，如jQuery，Script.aculo.us或Prototype库，JavaScript的编程变得有趣、高出。笨重的API已被清理干净，相反的是，却增加了乐趣和动态效果。

At the same time, a new generation of browser competition has erupted(爆发), with Google’s Chrome, Mozilla’s Firefox, Apple’s Safari, and Microsoft’s Internet Explorer all vying for the crown of browser king. As part of this, all these companies are investing heavily in the
JavaScript portion of these systems as modern web applications continue to grow ever-more dynamic and script-based. In particular, Google Chrome’s V8 JavaScript runtime is particularly fast and also open-sourced for use by anybody.

与此同时, 新一场浏览器竞争正在爆发, Google的Chrome、Mozilla的Firefox、Apple的Safari和Microsoft的Internet Explorer 正为争夺浏览器之王的王冠而斗得你死我活！ 作为其中的一部分，所有这些公司都在大举投资在这些系统中的JavaScript部分，并让它作为现代网络应用，不断让它变得更加动态和基于脚本的。特别是，谷歌Chrome的V8 JavaScript运行时特别快而且开源，任何人都可以使用它。

With all these things in place, the opportunity arose for somebody to come along with a new approach to network (web) application development. Thus, the birth of Node.js.

正因为这些关键因素的存在，开发者有了一个创建网络(web)应用开发的新方法。因此，Node.js诞生了。


##　What Exactly Is Node.js?
In 2009, a fellow named Ryan Dahl was working for a company called Joyent, a cloud and virtualization services company in California. He was looking to develop push capabilities for web applications, similar to how Gmail does it, and found most of what he looked at not quite appropriate. He eventually settled on JavaScript because it lacked a robust input/output (I/O) model (meaning he could write his own new one) and had the fast and fully programmable V8 runtime readily available.

2009年，Ryan Dahl在Joyent公司(一个专注于云计算和虚拟化服务的互联网公司，位于美国加利福尼亚州)寻求一种为web应用程序开发推送功能的方案，这个方案就像Gmail一样，他发现大多数方案不是很合适。他最终确定选择了JavaScript这门编程语言，因为它缺乏一个强大的输入/输出(I /O)模型(ps:意味着他需要“自力更生”, 方能“丰衣足食”,牛人是怎么定义的了？)，看起来，现在像是"万事俱备，只欠东风"，然而，“快速而且完全可编程的V8运行时”这东风正好来得很合时。



Inspired by some similar projects in the Ruby and Python communities, he eventually took the Chrome V8 runtime and an event-processing library called libev, and came up with the first versions of a new system called Node.js. 

受到Ruby和Python社区类似项目的启发，他最终"看上"了Chrome V8运行时和一个称作libev的事件处理库，并产生一个新系统的第一个版本，这个系统称之为Node.js。



The primary methodology or innovation in Node.js is that it is built entirely around an event-driven nonblocking model of programming. In short, you never (well, rarely) write code that blocks.

Node.js的主要原理或者说创新是完全是基于事件驱动非阻塞编程模型打造出来。简而言之，你几乎不可能或者很少写出阻塞型的代码！



If your web application—in order to process a request and generate a response—needs to run a database query, it runs the request and then tells Node.js what to do when the response returns. In the meantime, your code is free to start processing other incoming requests or, indeed, do any other task it might need, such as cleaning up data or running analyses.

如果您的Web应用程序---为了处理请求并生成一个来自数据库查询操作的响应，需要运行一个数据库查询，首先应用程序会运行请求，然后告诉Node.js什么时候做该返回响应。在此期间，你的代码可以自由地开始处理其他传入的请求，或者更确切地说，可以做它需要做的其他任务，如清理数据或运行分析。

Through this simple change in the way the application handles requests and work, you are able to trivially write web servers that can handle hundreds, if not thousands, of requests simultaneously on machines without much processing or memory resources. Node runs in a single process, and your code executes largely in a single thread, so the resource requirements are much lower than for many other platforms.


通过应用程序中处理请求和工作的方式，这样简单的变化，你编写的Web服务器，在机器没有太多的处理器或内存的情况，依然能轻松对抗数百甚至数千的请求。Node运行在单个进程中，而你的代码执行主要是在一个单独的线程中，所以资源对内存的要求远比其它平台低。


This speed and capacity come with a few caveats, however, and you need to be fully aware of them so you can start working with Node with your eyes wide open.

这样的速度和能力给我们带来一些“警示”，但是，在你开始Node编程前必须瞪大眼睛充分解Node的原理。


First and foremost, the new model is different from what you may have seen before and can sometimes be a bit confusing. Until you’ve wrapped your brain fully around some of the core concepts, some of the code you see written in Node.js can seem a bit strange. Much of this book is devoted to discussing the core patterns many programmers use to manage the challenges of the asynchronous, nonblocking way of programming that Node uses and how to develop your own.

首先，新模式于你先前看到的可能已经有所出入，甚至会让你感到有点混乱。直到你大脑对这些核心概念形成一个很好的吸收，您看到写在Node.js的代码似乎有点奇怪。本书很多是关于探讨Node的核心模式，比如很多程序员如何用Node管理异步逻辑和非阻塞编程所面临的挑战，以及如何进行相应的开发。


Another limitation with this model of programming is that it really is centered around applications that are doing lots of different things with lots of different processes, servers, or services.

这个编程模型的另一个局限性是:它真的是以那些需要很多不同的进程、服务器和服务做很多事情的应用程序的中心。



Node.js truly shines when your web application is juggling connections to databases, caching servers, file systems, application servers, and more. The flip side of this, however, is that it’s actually not necessarily an optimal environment for writing compute servers that are doing serious, long-running computations.

当你的web应用程序于数据库、缓存服务器、文件系统、应用服务器等"打交道"，以及更多的连接Node.js真是让人眼前一亮。这样做的另一面是，它不属于那类需要进行复杂却需要长时间运行的计算服务器的最佳环境。

For these, Node’s model of a single thread in a single process can create problems if a given request is taking a ton of time to generate a complicated password digest or processing an image. 

对于这些，Node在单进程级别的单线程编程模型带来一个问题，如果一个给定的请求花费大量的时间来生成一个复杂的密码摘要或处理图像。


 In situations in which you’re doing more computationally intensive work, you need to be careful how your applications use resources, or perhaps even consider farming those tasks out to other platforms and run them as a service for your Node.js programs to call.

你正在做计算密集型工作的情况下，你需要小心你的应用程序如何使用资源，甚至可能考虑将这些任务移到耕这些任务到其他平台和运行它们为您的Node.js程序调用服务。


Finally, Node.js is a reasonably new platform and under active development. It has not yet (as of February 2013) hit version 1.0, and is constantly seeing new releases, sometimes at a truly dizzying pace.

最后，Node.js是一个相当新的平台，并且处于正在积极发展的状态。2013年2月创下1.0版本，并不断看到新的版本中的诞生，更新速度真是令人眩目。


To reduce the randomization and troubles caused by these frequent updates, the developers have taken to labeling portions of the system with different degrees of stability, ranging from Unstable to Stable to Locked. Changes to Stable or Locked portions of the runtime are rare and involve much community discussion to determine whether it will generate too much pain. As you work your way through this book, we point out which areas are less stable than others and suggest ways you can mitigate the dangers of changing APIs.


为了减少因频繁的更新带来的随机化和问题，开发商已经采取了不同程度的稳定性版本，范围从从Unstable(不稳定)到Stable(稳定)再到Locked(锁定)。Node运行时版本稳定状态到锁定状态是罕见的，这涉及到社区之间的讨论，以确定它是否会对开发者带来太多的痛苦。通过这本书，我们会指出哪些Nodejs API那些部分相对其它不是太稳定，并且建议你减轻使用不断变化的API所带来的风险。



The good news is that Node.js already has a large and active user community and a bunch of mailing lists, forums, and user groups devoted to promoting the platform and providing help where needed. A simple Google search will get you answers to 99 percent of your questions in a matter of seconds, so never be afraid to look!

好消息是，Node.js的已经有一个庞大而活跃的用户社区和一堆邮件列表、论坛，用户组致力于促进平台开展，并对有需要的用户群体根据需要提供帮助。简单地使用谷歌搜索就可以在几秒钟之内回答你99%的问题，因此不要害怕看下去！

##　Who Is This Book For? 
