# ReactiveX doc 翻译 [Observable](http://reactivex.io/documentation/observable.html)

注：我可以直接看懂的English就没翻译

在ReactiveX中，一个observer订阅到一个Observable.然后根据Observable发射的item或者一序列items，observer做出反应。这个模式有利于并发操作，因为它确实不需要为了等待Observable发射objects而阻塞。但相反，它会以观察者的形式创造一个哨兵，随时准备在Observable所做的任何时候做出适当的反应。

这个页面解释了reactive pattern 是什么，Observables和observer是什么，observers怎么订阅到Observables。其他的页面展示了怎样使用大量的Observable operators来吧Observables连接在一起并且改变他们的行为。

这个文档通过“marble diagrams”作出解释。这里是marble diagrams怎样表示Observables和Observales的转换。

## See Also

* Single — a specialized version of an Observable that emits only a single item
* Rx Workshop: Introduction
* Introduction to Rx: IObservable
* Mastering observables (from the Couchbase Server documentation)
* 2 minute introduction to Rx by Andre Staltz (“Think of an Observable as an asynchronous immutable array.”)
* Introducing the Observable by Jafar Husain (JavaScript Video Tutorial)
* Observable object (RxJS) by Dennis Stoyanov
Turning a callback into an Rx Observable by @afterecho

# Background

在许多软件编程任务中，你或多或少的期待你写的说明能够按照你写的顺序、一个一个地、一次性执行并且完成。但是在ReactiveX中，许多说明可能并行执行并且他们的结果随后才能被observers拿到，而且是以随意的顺序。不是调用一个方法，你定义了一种机制，为了以Observable的形式取回和转移数据。在某一节点，预定义好的机制被触发，并且observer站岗在准备就绪时来捕获和对她的发射作出反应。

这种方式的一个优势时，当你有一系列相互独立的任务，你可以同时开始他们，而不是等待每一个结束再去开启下一个。这种方式，你整个系列的任务仅仅和最耗时的任务时长一样。
 
批注 by hongyunhan：在客户端不建议这样使用，CPU使用率过高。而且，我理解，即使不使用ReactiveX，也可以同时开启一些列相关的任务。

有许多terms用来描述异步编程和设计的模型。这个文档使用如下terms：An observer subscribes to an Observable.An Observable emits items or sends notifications ti its observers by calling the observers' methods.

在其他的文档或者上下文中，我们有时把“observer”叫做“subscriber”,"watcher","reactor".这个模型通常被叫做“reactor pattern”反应堆模式。

# Extablishing Observers

This page uses Groovy-like pseudocode for its examples, but there are ReactiveX implementations in many languages.

In an ordinary method call — that is, not the sort of asynchronous, parallel calls typical in ReactiveX — the flow is something like this:

1. Call a method.
2. Store the return value from that method in a variable.
3. Use that variable and its new value to do something useful.

Or, something like this:


`// make the call, assign its return value to `
`returnVal`
`returnVal = someMethod(itsParameters);`
`// do something useful with returnVal`

In the asynchronous model the flow goes more like this:

1. Define a method that does something useful with the return value from the asynchronous call; this method is part of the observer.
2. Define the asynchronous call itself as an Observable.
3. Attach the observer to that Observable by subscribing it (this also initiates the actions of the Observable).
4. Go on with your business; whenever the call returns, the observer’s method will begin to operate on its return value or values — the items emitted by the Observable.

Which looks something like this:

`// defines, but does not invoke, the Subscriber's onNext handler
// (in this example, the observer is very simple and has only an onNext handler)
def myOnNext = { it -> do something useful with it };
// defines, but does not invoke, the Observable
def myObservable = someObservable(itsParameters);
// subscribes the Subscriber to the Observable, and invokes the Observable
myObservable.subscribe(myOnNext);
// go on about my business`

## onNext, onCompleted, and onError

The Subscribe method is how you connect an observer to an Observable. Your observer implements some subset(子集) of the following methods:

onNext

An Observable calls this method whenever the Observable emits an item. This method takes as a parameter the item emitted by the Observable.

onError

An Observable calls this method to indicate that it has failed to generate the expected data or has encountered some other error. <b>It will not make further calls to onNext or onCompleted.</b> The onError method takes as its parameter an indication of what caused the error.

onCompleted

An Observable calls this method after it has called onNext for the final time, <b>if it has not encountered any errors</b>.

根据Observable合约的条款，它可以调用onNext零次或多次，然后可以通过调用onCompleted或onError而不是两者来跟随这些调用，这将是它的最后一次调用。 按照惯例，在本文档中，对onNext的调用通常称为项目的“发射”，而对onCompleted或onError的调用则称为“通知”。

## Unsubscribing

在一些ReactiveX实现中，有一个特殊化的observer接口，Subscriber,它实现了unsubscribe方法。你可以调用这个方法来表明Subsciber不再对任何它当前订阅的Observables感兴趣。如果不再有感兴趣的observers，这些Observables可以选择挺尸生成新的items来发射。

被observer订阅的Observale有一系列操作符链，这个去掉订阅的结果将会通过操作符链级联回来。并且这会引起链中的每一个连接停止发射items。这不能被保证立即发生，然而，即使没有observers在观察这些发射，Observable也可能生成和发射item一段时间。

## Some Notes on Nameing Conventions

每一个特定语言实现的ReactiveX有它自己的命名特点。尽管实现之间存在许多共性，但没有规范的命名标准。

更进一步，一些名字有在不同的上下文有不同的启示，否则在特定的实现语言中显得尴尬。

For example there is the onEvent naming pattern (e.g. onNext, onCompleted, onError). In some contexts such names would indicate methods by means of which event handlers are registered. In ReactiveX, however, they name the event handlers themselves.

# “Hot" and "Cold" Observables

一个 Observable 何时开始发射一序列的item？依赖于Observable.一个“hot” Observable 可能在创建时就开始发射items，因此，任何后来订阅该Observable的观察者可能开始在中间某处开始观察序列。一个“cold" Observable直到一个observer订阅它才开始发射itemsm,因此，observer可以确保看到完整的序列从刚开始。

In some implementations of ReactiveX, there is also something called a “Connectable” Observable. Such an Observable does not begin emitting items until its Connect method is called, whether or not any observers have subscribed to it.

# Composition via Observable Operators

Observables and observers are only the start of ReactiveX. By themselves they’d be nothing more than a slight extension of the standard observer pattern, better suited to handling a sequence of events rather than a single callback.

The real power comes with the “reactive extensions” (hence “ReactiveX”) — operators that allow you to transform, combine, manipulate, and work with the sequences of items emitted by Observables.

这些Rx运算符允许您以声明方式组合异步序列，同时具有回调的所有效率优势，但没有嵌套通常与异步系统相关联的回调处理程序的缺点。

Creating Observables
Create, Defer, Empty/Never/Throw, From, Interval, Just, Range, Repeat, Start, and Timer
Transforming Observable Items
Buffer, FlatMap, GroupBy, Map, Scan, and Window
Filtering Observables
Debounce, Distinct, ElementAt, Filter, First, IgnoreElements, Last, Sample, Skip, SkipLast, Take, and TakeLast
Combining Observables
And/Then/When, CombineLatest, Join, Merge, StartWith, Switch, and Zip
Error Handling Operators
Catch and Retry
Utility Operators
Delay, Do, Materialize/Dematerialize, ObserveOn, Serialize, Subscribe, SubscribeOn, TimeInterval, Timeout, Timestamp, and Using
Conditional and Boolean Operators
All, Amb, Contains, DefaultIfEmpty, SequenceEqual, SkipUntil, SkipWhile, TakeUntil, and TakeWhile
Mathematical and Aggregate Operators
Average, Concat, Count, Max, Min, Reduce, and Sum
Converting Observables
To
Connectable Observable Operators
Connect, Publish, RefCount, and Replay
Backpressure Operators
a variety of operators that enforce particular flow-control policies
These pages include information about some operators that are not part of the core of ReactiveX but are implemented in one or more of language-specific implementations and/or optional modules.

## Chaining Operators

大多数操作符操作一个Observable返回一个Observable。这允许你一个接一个的在练中应用这些operators。链中的每一个operator修改前一个operator输出的Observable。

还有一些其他的patterns，例如在Builder Pattern中，一个类的大量方法，可以通过类自身的方法修改对象。这种模式也允许你使用类似的方式链式调用方法。但是当在Builer Pattern中，方法在链中出现的顺序通常并不重要，Observable运算符的顺序很重要。

Observable operators的链不独立地操作链源头的那个Observable，而是按照顺序操作Observable，每一个都操作上一个操作符生成的Observable。