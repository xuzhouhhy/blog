# ReacriveX Single

翻译文[链接](http://reactivex.io/documentation/single.html)

RxJava已经发展出Observable变种叫做“Single”

一个Single类似Observable，但不是发射一些列values--任何地方从无到无穷多--它总是发射一个value或者一个错误notification。

订阅到single不需要三个方法来对Observable做出响应，你只需要两个方法来订阅：

 * onSuccess：一个Single将此方法传递给Singe发射的唯一item
 * onError:一个Single将此方法传递给引起Single不能发射一个item的异常
 
 一个Single将仅仅调用这些方法中的一个，并且仅仅调用一次，一旦调用了其他的方法，single就会终结Subscription
 
## Composition via Single Operators

类似Observables，Singles可以被大量的operators操控。一些operators也会允许Observable和Single之间的接口，以便你可以混合使用这两类：
