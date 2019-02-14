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