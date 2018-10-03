# LiveData Overview

[原文链接](https://developer.android.google.cn/topic/libraries/architecture/livedata)

LiveData is an observable data holder class. Unlike a regular observable, LiveData is lifecycle-aware, meaning it respects the lifecycle of other app components, such as activities, fragments, or services. This awareness ensures LiveData only updates app component observers that are in an active lifecycle state.

LiveData 是一个observable 数据持有类，不同于常见的observable，LiveData是一个lifecycle-aware，意味着他能配合其然app组件的生命周期，例如activities,fragments,services.这点确保LiveData仅仅在app组件活跃的生命周期状态下更新它们。

LiveData considers an observer, which is represented by the Observer class, to be in an active state if its lifecycle is in the STARTED or RESUMED state. LiveData only notifies active observers about updates. Inactive observers registered to watch LiveData objects aren't notified about changes.

LiveData 考虑到了作为一个观察者，典型代表是[Observer](https://developer.android.google.cn/reference/android/arch/lifecycle/Observer) class,当它的生命周期在started resumed状态的时候是活跃状态。LiveData仅仅更新活跃的observers。已注册观看LiveData objects 的非活跃状态的observers不会被改变触发。

You can register an observer paired with an object that implements the LifecycleOwner interface. This relationship allows the observer to be removed when the state of the corresponding Lifecycle object changes to DESTROYED. This is especially useful for activities and fragments because they can safely observe LiveData objects and not worry about leaks—activities and fragments are instantly unsubscribed when their lifecycles are destroyed.

你可以注册一个和实现LifecycleOwner接口的类配对的observer。当实时反应的LifeCycle类变为destroyed状态的时候，这层关系允许observer被移除。这点对activities和fragments是非常有用的，因为他们可以安全的观察LiveData类，并且当activities和fragmengs的lifecycle被destroyed迅速取消订阅
的时候，不用担心泄漏。

For more information about how to use LiveData, see Work with LiveData objects.

关于怎样使用LiveData的更多信息， see Work with LiveData objects.

##Using LiveData provides the following advantages:

###Ensures your UI matches your data state

LiveData follows the observer pattern. LiveData notifies Observer objects when the lifecycle state changes. You can consolidate your code to update the UI in these Observer objects. Instead of updating the UI every time the app data changes, your observer can update the UI every time there's a change.

确保你的ui和数据状态匹配

LiveData跟随着observer 部分。当lifecycle 状态改变，LiveData触发Observer类。你可以巩固你的代码来在这些Observer类中更新UI。你的observer可以在每次有变化的时候更新UI，而不是每次app data改变的时候更新UI

###No memory leaks

Observers are bound to Lifecycle objects and clean up after themselves when their associated lifecycle is destroyed.

没有内存泄漏

Observers绑定到LifeCycle，并且在它们关联的LifeCycle destroyed的时候清空掉。

###No crashes due to stopped activities

If the observer's lifecycle is inactive, such as in the case of an activity in the back stack, then it doesn’t receive any LiveData events.

没有因为停止的activities崩溃

如果observer's lifecycle是不活跃的，例如一个在back stack中的activity的情况下，然后他不会收到任何LiveData事件

###No more manual lifecycle handling

UI components just observe relevant data and don’t stop or resume observation. LiveData automatically manages all of this since it’s aware of the relevant lifecycle status changes while observing.

UI组件仅仅observe相关的数据，并且不会停止或者resume observation.LiveData自动管理所有这些，因为在observing的时候它知道相关的lifecycle状态

###Always up to date data

If a lifecycle becomes inactive, it receives the latest data upon becoming active again. For example, an activity that was in the background receives the latest data right after it returns to the foreground.

总是更新到实时数据

如果一个lifecycle变为非活跃的，当它变为活跃的时候，会收到时间上最近的数据。例如，一个在后台的activity在它返回到前台后收到时间上最近的数据。

###Proper configuration changes

If an activity or fragment is recreated due to a configuration change, like device rotation, it immediately receives the latest available data.

适应于配置变化

如果一个activity或者fragment因为配置变化被重新创建了，比如设备旋转，它立即能拿到时间上最近的数据

###Sharing resources

You can extend a LiveData object using the singleton pattern to wrap system services so that they can be shared in your app. The LiveData object connects to the system service once, and then any observer that needs the resource can just watch the LiveData object. For more information, see Extend LiveData.

分享资源

你可以使用单例模式继承LiveData 类来包裹系统serveces，以便于他们可以在你的app内部共享。LideData类一旦连接到系统servece一次，并且任何需要这个资源的observer仅仅观察这个LiveData类就可以。获取跟多信息，see [Extend LiveData]()
