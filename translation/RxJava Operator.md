# RxJava Operator

## from
from只会发射一次
RxJava2 remove Observable.from(), but add Observable.fromCallable() ect.

## just
Observable.just(a,b,c)
a,b,c都会被分别发射一次

## Map
transform the items emitted by an Observable by applying a function to each item

## FlatMap 
transform the items emitted by an Observable into Observables,them flatten the emission from those into a single Observable

This method is useful, for example, when you have an Observable that emits a series of items that themselves have Observable members or are in other ways transformable into Observables, so that you can create a new Observable that emits the complete collection of items emitted by the sub-Observables of these items.

## Create

## Defer
直到observer subscribe才穿件Observable，并且为每一个obsrever创建一个新的Observable

Defer operator 等待observer订阅它，然后它使用一个Observable factory function，生成一个Observable。它为每一个subscriber重新创建，所以尽管每一个subscriber可能任务它正在订阅同一个Observable，实际上，每一个subsciber都会拿到他自己的序列。

在一些情况下，会等到最后一刻才会生成Observable，可以确保这个Observable包含最新的data。

## Empty/Never/Throw

Empty 创建一个不发射items的Observable，但是正常终止（terminates normally）

Never 创建一个不发射items的Observable，也不终止

Throw 创建一个不发射items的Observable，使用一个错误终止

Empty, Never, and Throw operators 使用非常特定和限制的行为生成Observables。适合测试，与其他Observables相结合，或作为参数，与期望其他Observables作为参数的运算符。

## Interval
create an Observable that emits a sequence of integers spaced by a given time interval