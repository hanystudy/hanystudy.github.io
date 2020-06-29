---
layout: post
title: "软件设计与架构笔记(16): 架构风格——反应式架构"
date: 2020-06-29 15:20:02 +0800
comments: true
categories: 
- architecture
tags:
- software design
- software architecture
- reactive architecture
---

## 架构风格——反应式架构

在过去10年，多核、云计算、移动/IOT、用户体验等相关领域的发展使传统上以可维护性为核心的软件架构面临着新的挑战，这主要体现在软件系统的**即时响应性**(Responsiveness)、**回弹性**(Resilience)以及**弹性**(Elasticity)等架构质量属性[JDRM14]。

即时响应性旨在合理成本范围内提供低延迟的用户体验。显然这并非是全新的质量属性，传统上围绕它的解决方案包括算法优化、摩尔定律等。然而，随着系统复杂性不断上升，即时响应性不可避免地受到损害，业界发明了许多技术解决这一问题。从架构角度看，这些技术可以被划分为**纵向**(Scale up/down)和**横向**(Scale out/in)两种基本的扩展方案。其中，前者以多核技术为代表，后者则依赖分布式技术，两者在架构方面互为补充。由此衍生的细分领域包括但不限于并发编程、分布式通信、数据一致性、节点协调、错误处理、职责分离等，这些技术在实践中形成了一系列设计原则和模式。**反应式架构**(Reactive architecture)就是以这些原则和模式为基础、进而发展为一种面向现代高即时响应性的软件系统的架构风格。

### 多核与反应式编程

多核技术提供了纵向的单机扩展能力，但要利用这种底层能力离不开上层并发编程的支持。经典的并发编程框架如Java Concurrency[BG99]，提供了最接近底层且功能强大的并发编程API，例如下列一个爬虫的代码片段(例子来源于互联网):

```
public class Crawler {
   private ConcurrentHashMap<String, Boolean> seen = new ConcurrentHashMap<String, Boolean>();
   private AtomicInteger pending = new AtomicInteger(0);
   
   public Crawler(String baseUrl, int numOfThreads) {
       this.client = HttpClientBuilder.create().build();
       this.baseUrl = baseUrl;
       this.executorService = Executors.newFixedThreadPool(numOfThreads, new ThreadFactory() {
          public Thread newThread(Runnable r) {
              return new Thread(r, "Crawler-Worker");
           }
       });
   }
   
   public void start() {
       handle(baseUrl);
   }
   
   private void handle(final String link) {        
      if (seen.containsKey(link))
          return;
      seen.put(link, true);
      pending.incrementAndGet();
      executorService.execute(new Runnable() {
          public void run() {
              List<String> links = getLinksFromUrl(link);
              for (String link : links) {
                  handle(link);
              }
              pending.decrementAndGet();
              if (pending.get() == 0) {
                  synchronized (lock) {
                      lock.notify();
                  }
              }
          }
      });
   }
}
```

客户端代码如下:

```
public class Main{
	public static void main(String[] args) throws InterruptedException {
   		if (args.length != 2) {
       		System.err.println("Invalid syntax: <baseUrl> <numOfThreads>");
       		System.exit(1);
   		}
   		String baseUrl = args[0];
   		int numOfThreads = Integer.parseInt(args[1]);
   		Crawler crawler = new Crawler(baseUrl, numOfThreads);
   		crawler.start();
   		crawler.join();
   		crawler.shutdown();
	}
}
```

该例中的爬虫实现基于Java Concurrency的ExecutorService API，一种共享状态并发式编程模型。为了保证线程安全，代码中采用了ConcurrentHashMap、AtomicInteger、Lock和Synchronized等Java特有的并发编程技术，存在复杂度较高、易理解性差、共享状态维护难度高、易出错等缺点。

**反应式编程**(Reactive programming)是一种具有异步编程风格的编程框架，其衍生自基于数据流的并发声明式编程模型，采用事件驱动和非阻塞线程技术，从而降低因为资源等待导致的并发性能瓶颈。首先来看一个基于RxJava[RXJ14]的爬虫代码片段(例子来源于互联网):

```
public class ObservableCrawler {
  private Subscriber<? super String> subscriber;
  
  public static Observable<String> create(Crawler crawler, String url, int numOfThreads) {
      ObservableCrawler o = new ObservableCrawler(crawler, numOfThreads);
      return Observable.create(subscriber -> {
          o.subscriber = subscriber;
          if (o.executorService == null) {
              o.process(url);
              subscriber.onCompleted();
          } else {
              o.processAsync(url);
          }
      });
  }
  private ObservableCrawler(Crawler crawler, int numOfThreads) {
      this.crawler = crawler;
      this.executorService = numOfThreads > 0 ?
              Executors.newFixedThreadPool(numOfThreads, r -> new Thread(r, "Crawler-" + threadIdGenerator.incrementAndGet()))
              : null;
  }
  
  private void processAsync(String url) {
         pendingTasks.incrementAndGet();
         executorService.submit(() -> {
             // If item is not unique, skip processing
             boolean isFirstTime =  results.add(url);
             if (isFirstTime) {
                 subscriber.onNext(url);
                 crawler.crawl(url, this::processAsync);
             }
             if (pendingTasks.decrementAndGet() == 0) {
                 subscriber.onCompleted();
                 executorService.shutdown();
             }
         });
  }
}
```

其客户端代码如下:

```
public class CrawlerClient {
  public CrawlerClient(Crawler crawler, String url, int numOfThreads) {
      this.observable = ObservableCrawler.create(crawler, url, numOfThreads);
      observable.subscribe(this::onNext, this::onError, this::onCompleted);
  }

  public void waitForCompletion() throws InterruptedException {
      completionLatch.await();
  }
}
```

与前面直接基于Java Concurrency的例子类似，该例同样采用了ExecutorService实现爬虫的并发执行，其中waitForCompletion也提供了阻塞客户端的能力。两者的区别在于，RxJava的版本采用观察者模式对爬虫类进行了封装，客户端不再需要等待代码执行结束后再执行后续指令。其中的一部分技术细节，例如线程安全代码被封装在API中，再通过回调函数接口提供给客户端，后者只需要关心onNext、onError和onCompleted的线程安全实现，这在一定程度上降低了Java并发编程的复杂度。

除了上例介绍中朴素的回调函数API之外，反应式编程通常还可能提供多种异步编程风格API，包括但不限于:

* Futures/Promises，一种单赋值容器，支持针对单写/多读场景的、自上而下的异步编程风格，能够有效解决回调地狱(Callback hell)的问题。

* 流(Streams)，一种无界限的数据处理流，支持多个源点、汇点间的异步、非阻塞、背压(Backpressure)的数据变换管道。例如函数组合(Functional composition)提供的map、filter、fold等流式操作。这里的背压是指当异步执行管道中的消费端计算能力不足，上游仍然持续生产事件，从而导致系统过载的问题。反应式编程API一般通过配置不同策略以防止潜在的系统灾难。

* 数据流变量(Dataflow variables)，一种单赋值变量，能够基于输入、过程和其它变量实现自动更新。

前述例子中采用的RxJava遵循了ReactiveX标准——一套反应式编程的技术规范[REX]，类似的反应式编程扩展(Rx)支持已被添加至许多编程语言中，如RxJS、Rx.NET、RxScala等。此外，RxJava还支持JVM平台的反应式流规范(Reactive Streams Specification)[RSS]，从而具备与其它反应式编程API之间的互操作性。与传统编程模型相比，反应式编程在多核利用率、并发编程、系统模块性、工作流组装方面相较于传统编程模型具有优势。但其同样可能会损害代码的易理解性，并且通常只局限于单机计算，虽然有助于加强即时响应性，但并不能满足反应式架构要求的另外两个核心质量属性:

* 回弹性，指系统在出错时能够自动恢复并保持正常的即时响应性目标。

* 弹性，是指系统在异常工作负载下，能够自适应调整自身容量从而维持其正常的即时响应性目标。

这正是分布式系统成为云计算、移动/IOT领域核心技术的重要原因。

### 分布式与异步消息传递

分布式技术的目标是为系统提供横向扩展能力。通过负载监控和自动化伸缩技术，实现系统容量的自适应调节，从而满足弹性需求。另外，通过隔离组件控制系统错误/灾难的级联传递，再将错误提交至安全上下文中即时处理，从而满足回弹性需求。

反应式架构使用**异步消息传递**作为组件间的通信模式，通过在组件间建立临时边界，实现组件间的松耦合、隔离与地址透明化，达到时间、空间二重解耦的目的。值得注意的是，这里的空间解耦既可以指单机中的线程/进程，也可以指分布式系统的组件。“消息传递”意味着组件通常是长期存活且可被调用者直接定位、实现定向通信的方式，这与另一种流行的分布式系统通信模式——“事件传递”有着重要区别。因为“事件传递”是通过事件源的状态变化引发相应事件，再通过观察者模式通知“订阅”的组件，因此其关注点在于**可定位事件源**。而在“消息传递”中则相反，调用者需要明确知道被调用者的位置信息，即更加关注**可定位接收者**——这是反应式架构实现回弹性和弹性的重要基础。因为后者具有更强的控制力:负载管理、错误检测、消息丢弃/复制/排序、通过监控和调整消息队列实现流量控制以及背压等。

以应对回弹性为例，为了使系统在出错时仍然保持正常的即时响应性，需要实现调用者和被调用者的完全隔离，使后者发生的错误不被传递至前者，同时应支持消息被传递到多个复制组件中，即便错误发生时系统仍能正常提供服务。虽然容错性也是受到普遍重视的质量属性之一，但是传统上强耦合、深度嵌套的同步调用链代码缺少一致的容错方案。反应式架构明确要求把错误信息封装在消息中传递至其它组件，并使其在出错组件外部的一个安全上下文中得到有效处理(代理模式)。这里体现的基本思想是把错误处理从原有调用链中解耦，即移除客户端中针对服务端错误进行处理的职责。

再以弹性计算为例，系统被要求能够根据实际负载需求自动增加或减少所占用资源，从而动态调整吞吐量。这种自适应性意味着无介入实现系统伸缩、状态/行为冗余、负载均衡、失效备援和系统升级等能力。其基本思想是在编程抽象和语义层面实现组件空间位置的透明化，使系统更易于伸缩，且这种可伸缩性无需局限于CPU核甚至数据中心。

### 消息传递并发式模型及其应用

消息传递并发式模型是一种通过异步通信信道实现组件间通信的编程模型，目前被广泛应用于实现反应式架构中的“异步消息传递”模式，例如经典的Actor模型[CPR73]。

Actor是一种相比基于线程的并发编程更高级的抽象模型，其旨在解决如下问题:

* 伸缩性，指包括单机和分布式环境下的系统扩容能力，即隐藏系统横向、纵向扩展的底层技术差异。

* 透明性，指同时适应单机和分布式环境下的定位能力，例如在单机环境下采用并发编程语言，在分布式环境下采用网络通信，这些完全不同的资源定位方式导致系统难以从单机向分布式演化。

* 不一致性，这里的不一致性是指在许多超大型系统中，面向人的信息系统交互存在不一致的问题，例如文档、标准等。

在Actor模型中，最基本的并发计算元素被称作actor。actor之间可以发送和接收消息，并且各自维护一个内部状态。当actor接收到消息时，可以并行执行下列响应方法:

* 创建有限数量的新actor。

* 发送有限数量的消息给其它actor。

* 定义下一次接收消息时触发的行为。

Actor模型无论在计算理论还是在实际应用中都产生了重要影响，特别是可以被用于描述一些流行的并发编程框架，例如下面要介绍的Erlang/OTP Processes[EOP]和Akka Actors[AA]。

#### Erlang/OTP Processes

Erlang是一种声明式编程语言，除了基本的语法规则外，Erlang在其内核语言的基础上还提供了一套专有运行时系统和库——OTP(Open Telecom Platform)，后者是Erlang实现分布式、软实时、高容错、高可用、热部署的基础[OTP]。其中，**进程**(Processes，注意这里不是操作系统进程)是Erlang并发编程的基本计算元素，类似actor。如下列代码所示(例子来源于互联网):

```
-module(counter).
-export([run/0, counter/1]).

run() ->
    S = spawn(counter, counter, [0]),
    send_msgs(S, 100000),
    S.

counter(Sum) ->
    receive
        value -> io:fwrite("Value is ~w~n", [Sum]);
        {inc, Amount} -> counter(Sum+Amount)
    end.

send_msgs(_, 0) -> true;
send_msgs(S, Count) ->
    S ! {inc, 1},
    send_msgs(S, Count-1).

% Usage:
%    1> c(counter).
%    2> S = counter:run().
%       ... Wait a bit until all children have run ...
%    3> S ! value.
%    Value is 100000
```

上例中实现了一个并发计数器counter，并向外部提供两个函数run和counter。其中run函数的作用是创建一个新的counter进程，然后向其发送倒计时时间消息。counter定义了消息接收行为，包括打印和增数。send_msgs通过!向S进程发送消息，然后通过递归实现倒数。与actor概念类似，Erlang的进程相互之间完全隔离，并通过消息传递相互通信。由于进程的创建和销毁十分轻量化，从而允许系统中容纳数量非常可观的进程(在普通PC中即可实现千万级进程数)，这些进程可以在运行时系统中存在很长时间，如果没有消息接收或者运行了太长时间，进程就会被重新放入调度队列，避免影响其它正常运行进程。

#### Akka Actors

Akka是一个基于JVM的并发编程框架，其中的核心模块Akka Actors的Scala语法部分借鉴自Erlang，例如:

```
object HelloWorld {
  final case class Greet(whom: String, replyTo: ActorRef[Greeted])
  final case class Greeted(whom: String, from: ActorRef[Greet])

  def apply(): Behavior[Greet] = Behaviors.receive { (context, message) =>
    context.log.info("Hello {}!", message.whom)
    message.replyTo ! Greeted(message.whom, context.self)
    Behaviors.same
  }
}
```

与此相比Java版本就显得较为繁琐一些，但也很容易理解:

```
public class HelloWorld extends AbstractBehavior<HelloWorld.Greet> {

  public static final class Greet {
    public final String whom;
    public final ActorRef<Greeted> replyTo;

    public Greet(String whom, ActorRef<Greeted> replyTo) {
      this.whom = whom;
      this.replyTo = replyTo;
    }
  }

  public static final class Greeted {
    public final String whom;
    public final ActorRef<Greet> from;

    public Greeted(String whom, ActorRef<Greet> from) {
      this.whom = whom;
      this.from = from;
    }
  }

  public static Behavior<Greet> create() {
    return Behaviors.setup(HelloWorld::new);
  }

  private HelloWorld(ActorContext<Greet> context) {
    super(context);
  }

  @Override
  public Receive<Greet> createReceive() {
    return newReceiveBuilder().onMessage(Greet.class, this::onGreet).build();
  }

  private Behavior<Greet> onGreet(Greet command) {
    getContext().getLog().info("Hello {}!", command.whom);
    command.replyTo.tell(new Greeted(command.whom, getContext().getSelf()));
    return this;
  }
}
```

从编程模型的角度看，Akka Actors与Erlang Processes本质上是一致的，然而其底层系统存在巨大差别——更多是JVM与OTP的差别。两者如今也都形成了各自庞大的生态系统，成为设计反应式架构的重要参考，并且在通信、数字金融、在线游戏、在线交易、统计、社交媒体、移动应用等领域得到了广泛应用。

## 结论

本文介绍了一种流行的架构风格——反应式架构，详细讨论了即时响应性、回弹性和弹性等质量属性以及异步消息传递模式。在具体实践层面，首先讨论了事件驱动模式的反应式编程及其在多核环境中的应用，进一步介绍了消息传递模式的Actor模型及相关的流行编程框架——Erlang Processes和Akka Actors。由此可见，**参考架构风格的关键在于理解其要解决的核心问题，即要满足的特定的功能或非功能需求是否符合期望。一旦确定架构风格，其特定的设计原则就应尽量被作为软件开发的通用设计原则。而针对架构风格中包含的多种模式、框架和系统，就需要依据具体上下文灵活做出选择。**

## 引用

JDRM14, [https://www.reactivemanifesto.org/](https://www.reactivemanifesto.org/)

BG99, [Java Concurrency in Practice](https://www.amazon.com/Java-Concurrency-Practice-Brian-Goetz/dp/0321349601)

RXJ14, [https://github.com/ReactiveX/RxJava](https://github.com/ReactiveX/RxJava)

REX, [http://reactivex.io/](http://reactivex.io/)

RSS, [http://www.reactive-streams.org/](http://www.reactive-streams.org/)

CPR73, [A universal modular ACTOR formalism for artificial intelligence](https://dl.acm.org/doi/10.5555/1624775.1624804)

EOP, [https://erlang.org/doc/reference_manual/processes.html](https://erlang.org/doc/reference_manual/processes.html)

AA, [https://doc.akka.io/docs/akka/current/typed/index.html](https://doc.akka.io/docs/akka/current/typed/index.html)

OTP, [https://erlang.org/doc/](https://erlang.org/doc/)