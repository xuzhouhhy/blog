# ReactiveX
## [整体介绍](http://reactivex.io/)
### 设计思想
观察者模式干得好。ReacticeX是观察者模式、迭代模式、函数式编程的最好的思想的组合。

 * create 轻松创建事件流和数据流
 * combine 使用query-like操作符组成和转发流
 * listen 订阅任何可观察的流，执行操作

### 应用场景
Ubiquitous无所不在

 * frontent前端，在web上使用RxJS，在mobile上使用Rx.NET和RxJAva来操作ui事件和对应的API。
 * cross-platform Available for idiomatic Java, Scala, C#, C++, Clojure, JavaScript, Python, Groovy, JRuby, and others
 * Backend 后台，拥抱ReactiveX的asynchronicity异步性,可以使并发和接口实现独立

### better codebases更好的代码库
 * functional 通过观察流使用洁净的输入输出函数，避免错综复杂状态的编程
 * less is more ReactiveX的操作符经常reduce（减少，还原）常常是负责的挑战变为几行代码
 * async error handling 异步计算中的错误，传统的try/catch所做甚微，ReactiveX使用了合适的mechanisms（机械）来处理错误
 * concurrency made easy ReactiveX 的Observables和Schedulers允许编程者抽象的远离低级别县城、同步操作、并发议题

### Reacticve Revolution
ReacticeX不仅仅API,它是编程中的思想和突破。它已经影响了一些其他的APIs，框架，甚至一些编程语言

## Introduction
### ReacticeX
ReacticeX是一个使用观察者序列来组成异步和基于事件的编程的library。
它扩展了the observer pattern来支持数据和事件的序列，并且添加允许你把序列组合声明在一起的操作符，抽象的原理关心一些事情，比如低级别线程、同步性、线程安全、并发数据结构、非阻塞io。
Observable是通过访问多个项目的异步序列的理想方式填补空白。
它又是被叫做“functional reactive programming”,但这是用词不当。ReactiveX可能是functional，并且可能是reactive，但是和“functional reactive programming”是不同的物种。一个主要的不同点是frp操作随着时间连续变化的值，人儿ReactiveX操作随着时间推移的离散值 (See [Conal Elliott’s work for more-precise information on functional reactive programming.](https://github.com/conal/talk-2015-essence-and-origins-of-frp))
### 为什么使用Observables?
ReacticeX Observale模型使你使用针对一系列数据集合的同样排序的简单的、本质的操作符来处理异步流的事件。它把你从纠结的回调网络释放出来，是你的code更加可读性、不容易出错。
#### Observables Are Composable可组合的
像Java Feature这样的技术可以直接用于单级别的异步执行，但是当它们嵌套起来的时候开始增加non-trivial复杂性。
很难使用特性优化组合条件异步执行流程（或者是不可能，因为每个请求的延迟是运行时的）当然，可以做到，但是他很快变得复杂（因此容易出错）或者它过早的阻塞了Feature.get(),这消除了异步执行的好处。
ReacticeX Observables另一方面是用来构成异步数据的流程和序列
#### Observables Are Flexible
ReactiveX Observables不仅仅支持单个标量值的放射（如Futures那样），但是也是值的序列或者甚至无限的流。Observale是可以用来任何其他用例的单个抽象。Observable具有与Iterable镜像相似的所有灵活性和优雅。
#### Observales Are Less Opinionated自以为是
ReactiveX不偏向一些特定的并发和异步场景。Observables可以实现线程池、时间循环、non-blocking I/O、actors、或者任何适合你的需求、styles、专业知识。客户端code将所有他和Observales的交互作为异步处理，无论你的底层实现是阻塞的、非阻塞的，或者你选择实现它。
重要：使用ReactiveX你可以迟后改变你的想法，并且彻底修改你的Observable实现的底层逻辑，并且不需要破坏Observale的consumers.
#### Callbacks hava their own problems
Callbacks通过不允许任何阻塞来解决Future.get()过早阻塞的问题。它们当然是有效的，因为它们当response是有效的时候执行。
但是和Futures一样，单个级别的异步执行的回调很容易，但是嵌套的组合很难处理。
#### ReacticeX is a Polyglot Implementation
ReavtiveX在很多语言中都实现了，通过尊重这些语言成语的方式，并且更多的语言被添加到a rapid clip

### ReavticeX Programming
Reacticex 提供了一系列可以过滤、选择、转换、combine、compose Observables操作符.这允许有效的执行和组成。
你可以把Observables class当作与Iterable相对应的“push”，Iterable是“pull”。使用Iterable，消费者从生产者pull values并且阻塞住线程知道values到达。相比之下，使用Observales，无论何时valus是可达的生产者push values给消费者。这个路径是更灵活的，因为values可能异步到达也可能同步达到。






















