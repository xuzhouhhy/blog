# Handling Lifecycles with Lifecycle-Aware Components

Lifecycle-aware components perform actions in response to a change in the lifecycle status of another component, such as activities and fragments. These components help you produce better-organized, and often lighter-weight code, that is easier to maintain.

Lifecycle-aware 组件根据另一个组件lifecycle状态的变化作出反应，例如activities 和 fragments。这些组件帮助你产出组织更好的、轻量级的更好维护的代码。

A common pattern is to implement the actions of the dependent components in the lifecycle methods of activities and fragments. However, this pattern leads to a poor organization of the code and to the proliferation of errors. By using lifecycle-aware components, you can move the code of dependent components out of the lifecycle methods and into the components themselves.

在activities和fragments的lifecycle方法中，实现一个独立组件的行为是一个常用的模式。然而这种模式导致代码不容易组织，并且导致错误增多。通过使用lifecycle-aware components,你可以把独立组件的代码从lifecycle方法中迁移到组件自身中。

The android.arch.lifecycle package provides classes and interfaces that let you build lifecycle-aware components—which are components that can automatically adjust their behavior based on the current lifecycle state of an activity or fragment.

android.arch.lifecycle包提供了类和接口，使您可以构建生命周期感知组件 - 这些组件可以根据活动或片段的当前生命周期状态自动调整其行为。

Most of the app components that are defined in the Android Framework have lifecycles attached to them. Lifecycles are managed by the operating system or the framework code running in your process. They are core to how Android works and your application must respect them. Not doing so may trigger memory leaks or even application crashes.

定义在Android Framework中的大多数组件都有连接到它们的lifecycles。运行在进程中的操作系统或者framework的代码管理Lifecycles。它们是Android
工作的核心，你的application必须尊重它们。不这样做可能会引起内存泄漏甚至application crashes。

Imagine we have an activity that shows the device location on the screen. A common implementation might be like the following:

想象我们有一个activity在屏幕上展示设备位置。一个常用的实现可能像下面这样：

	internal class MyLocationListener(
        private val context: Context,
        private val callback: (Location) -> Unit
	) {

    fun start() {
        // connect to system location service
    }

    fun stop() {
        // disconnect from system location service
    }
	}

	class MyActivity : AppCompatActivity() {
    private lateinit var myLocationListener: MyLocationListener

    override fun onCreate(...) {
        myLocationListener = MyLocationListener(this) { location ->
            // update UI
        }
    }

    public override fun onStart() {
        super.onStart()
        myLocationListener.start()
        // manage other components that need to respond
        // to the activity lifecycle
    }

    public override fun onStop() {
        super.onStop()
        myLocationListener.stop()
        // manage other components that need to respond
        // to the activity lifecycle
    }
	}
Even though this sample looks fine, in a real app, you end up having too many calls that manage the UI and other components in response to the current state of the lifecycle. Managing multiple components places a considerable amount of code in lifecycle methods, such as onStart() and onStop(), which makes them difficult to maintain.

即使这个例子在一个真实的app中看起来不错，您最终会有太多调用来管理UI和其他组件以响应生命周期的当前状态。在lifecycle方法中管理多个组件的需要管来呢的大量代码，方法例如 onStart()和onStop()，这会使它们难以维护。

Moreover, there's no guarantee that the component starts before the activity or fragment is stopped. This is especially true if we need to perform a long-running operation, such as some configuration check in onStart(). This can cause a race condition where the onStop() method finishes before the onStart(), keeping the component alive longer than it's needed.

更多的是，无法保证组件在activity或者fragment被停止之前开始。当我们需要一个长时间运行的操作，例如在onStart()方法中检查配置。这可能会引起一个竞争情况：onStop()方法在onStart()完成之前结束了，这让组件比我们需要的活得更久。

	class MyActivity : AppCompatActivity() {
    private lateinit var myLocationListener: MyLocationListener

    override fun onCreate(...) {
        myLocationListener = MyLocationListener(this) { location ->
            // update UI
        }
    }

    public override fun onStart() {
        super.onStart()
        Util.checkUserStatus { result ->
            // what if this callback is invoked AFTER activity is stopped?
            if (result) {
                myLocationListener.start()
            }
        }
    }

    public override fun onStop() {
        super.onStop()
        myLocationListener.stop()
    }

	}
The android.arch.lifecycle package provides classes and interfaces that help you tackle these problems in a resilient and isolated way.

The android.arch.lifecycle package提供了类和接口来帮助你以一种弹性的、独立的方式捕获这些问题。

## Lifecycle
Lifecycle is a class that holds the information about the lifecycle state of a component (like an activity or a fragment) and allows other objects to observe this state.

Lifecycle 是一个类，持有关于组件lifecycle状态的信息，并且允许其他类来观察这个状态。

Lifecycle uses two main enumerations to track the lifecycle status for its associated component:

Lifecycle使用两个主要的枚举来追踪它关联组件的 lifecycle 状态：

 * Event  
  
  事件
  
	The lifecycle events that are dispatched from the framework and the Lifecycle class. These events map to the callback events in activities and fragments.
	
	被framework分发的Lifecycle事件和Lifecycle class.这些事件匹配到activities和fragments的回调事件。
	
 * State

 状态
 
 The current state of the component tracked by the Lifecycle object.
 
 组件的当前状态被Lifecycle类追踪。
 
Think of the states as nodes of a graph and events as the edges between these nodes.

把states当作图形节点的状态，events作为这些即节点之间的边缘。

A class can monitor the component's lifecycle status by adding annotations to its methods. Then you can add an observer by calling the addObserver() method of the Lifecycle class and passing an instance of your observer, as shown in the following example:

一个类可以通过给它的方法添加annotations监测组件的lifecycle状态.然后你可以通过调用Lifecycle类的addObserver()方法添加一个observer，并且传递你的observer实例，下面的代码展示：

	class MyObserver : LifecycleObserver {

    @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
    fun connectListener() {
        ...
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
    fun disconnectListener() {
        ...
    }
	}
In the example above, the myLifecycleOwner object implements the LifecycleOwner interface, which is explained in the following section.

上面的例子中，the myLifecycleOwner 类实现了LifecycleOwner接口，这会在下面的部分中解释。

## LifecycleOwner

LifecycleOwner is a single method interface that denotes that the class has a Lifecycle. It has one method, getLifecycle(), which must be implemented by the class. If you're trying to manage the lifecycle of a whole application process instead, see ProcessLifecycleOwner.

LifecycleOwner是一个单独方法接口，可以被有Lifecycle的类表示。它有一个方法，getLifecycle()，被许被这个类实现。如果你在尝试管理一整个application进程的lifecycle，see [ProcessLifecycleOwner](https://developer.android.google.cn/reference/android/arch/lifecycle/ProcessLifecycleOwner)

This interface abstracts the ownership of a Lifecycle from individual classes, such as Fragment and AppCompatActivity, and allows writing components that work with them. Any custom application class can implement the LifecycleOwner interface.

这个接口抽象了来自独立类的LifeCycle ownership,例如Fragment和AppCompatActivity，并且允许写组件和他们一起工作。任何自定义的application 类可以实现LifecycleOwner接口。

Components that implement LifecycleObserver work seamlessly with components that implement LifecycleOwner because an owner can provide a lifecycle, which an observer can register to watch.

实现了LifecycleObserver的组件和实现了LifecycleOwner的组件一起无缝工作，因为一个owner可以提供lifecycle，一个observer可以注册来观看。

For the location tracking example, we can make the MyLocationListener class implement LifecycleObserver and then initialize it with the activity's Lifecycle in the onCreate() method. This allows the MyLocationListener class to be self-sufficient, meaning that the logic to react to changes in lifecycle status is declared in MyLocationListener instead of the activity. Having the individual components store their own logic makes the activities and fragments logic easier to manage.

对定位追踪实例，我们创建MyLocationListener类实现LifecycleObserver，并且在activity的LifecycleonCreate()方法中初始化。这允许MyLocationListener类足够独立，意味着这个针对声明在MyLocationListener中的lifecycle status作出反应，而不是activity
的。拥有这个独立的组件存储它们自己的逻辑让activities和fragments逻辑更好管理。

	class MyActivity : AppCompatActivity() {
    private lateinit var myLocationListener: MyLocationListener

    override fun onCreate(...) {
        myLocationListener = MyLocationListener(this, lifecycle) { location ->
            // update UI
        }
        Util.checkUserStatus { result ->
            if (result) {
                myLocationListener.enable()
            }
        }
    }
	}
A common use case is to avoid invoking certain callbacks if the Lifecycle isn't in a good state right now. For example, if the callback runs a fragment transaction after the activity state is saved, it would trigger a crash, so we would never want to invoke that callback.

如果LifeCycle当前没有在一个合适的状态，通常的使用案例是去避免调用主要的回调。例如，如果在activity状态被保存后，回调运行一个fragment transaction，这回引起crash，所以我们从不调用回调。

To make this use case easy, the Lifecycle class allows other objects to query the current state.

为了让这个使用案例更简单，the Lifecycle 类允许其他的类来查询当前状态。


	internal class MyLocationListener(
        private val context: Context,
        private val lifecycle: Lifecycle,
        private val callback: (Location) -> Unit
	) {

    private var enabled = false

    @OnLifecycleEvent(Lifecycle.Event.ON_START)
    fun start() {
        if (enabled) {
            // connect
        }
    }

    fun enable() {
        enabled = true
        if (lifecycle.currentState.isAtLeast(Lifecycle.State.STARTED)) {
            // connect if not connected
        }
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
    fun stop() {
        // disconnect if connected
    }
	}
With this implementation, our LocationListener class is completely lifecycle-aware. If we need to use our LocationListener from another activity or fragment, we just need to initialize it. All of the setup and teardown operations are managed by the class itself.

在这个实现后，我们的LocationListener类是完全lifecycle-aware。如果我们需要使用来自另一个activity或者fragment的LocationListener，我们只需要初始化它。所有的设置和teardown操作被类本身管理。

If a library provides classes that need to work with the Android lifecycle, we recommend that you use lifecycle-aware components. Your library clients can easily integrate those components without manual lifecycle management on the client side.

如果一个library提供了需要和Android lifecycle一起工作的类，我们建议你使用lifecycle-aware组件。你的library客户端不需要早client side手动进行lifecycle 管理就可以简单的整合这些组件。

### Implementing a custom LifecycleOwner

Fragments and Activities in Support Library 26.1.0 and later already implement the LifecycleOwner interface.

Fragments和Activities在Support Libray 26.0.1和之后的版本都实现了LifecycleOwner接口。

If you have a custom class that you would like to make a LifecycleOwner, you can use the LifecycleRegistry class, but you need to forward events into that class, as shown in the following code example:

吐过你有一个自定义类想创建LifecycleOwner，你可以使用LifecycleRegistry类，但是你需要将事件转发到这个类，下面的代码实例展示了：

	class MyActivity : AppCompatActivity(), LifecycleOwner {

    private lateinit var mLifecycleRegistry: LifecycleRegistry

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        mLifecycleRegistry = LifecycleRegistry(this)
        mLifecycleRegistry.markState(Lifecycle.State.CREATED)
    }

    public override fun onStart() {
        super.onStart()
        mLifecycleRegistry.markState(Lifecycle.State.STARTED)
    }

    override fun getLifecycle(): Lifecycle {
        return mLifecycleRegistry
    }
	}
Best practices for lifecycle-aware components

对lifecycle-awate components 的最佳实践

* Keep your UI controllers (activities and fragments) as lean as possible. They should not try to acquire their own data; instead, use a ViewModel to do that, and observe a LiveData object to reflect the changes back to the views.

* 保持你的UI controllers(activities and fragments)尽可能精益求精。它们不应该尝试acquire它们自己的数据;相反地，使用一个ViewModel做这个，并且观察一个LiveData类来在回到views后反应这些改变

* Try to write data-driven UIs where your UI controller’s responsibility is to update the views as data changes, or notify user actions back to the ViewModel.

* 尝试写data-driven UIs，你的UI组件的责任时随着数据改变来更新views，或者notify用户操作到ViewModel

* Put your data logic in your ViewModel class. ViewModel should serve as the connector between your UI controller and the rest of your app. Be careful though, it isn't ViewModel's responsibility to fetch data (for example, from a network). Instead, ViewModel should call the appropriate component to fetch the data, then provide the result back to the UI controller.

* 把你的数据逻辑放到ViewModel类中。ViewModel应该作为UI controller 和app其他部分的connector.但是应该小心，ViewModel的责任不是fetch data（例如从网络），替代的是，ViewModel应该调用合适的组件来fetch the data,然后提供结果给UI controller。

* Use Data Binding to maintain a clean interface between your views and the UI controller. This allows you to make your views more declarative and minimize the update code you need to write in your activities and fragments. If you prefer to do this in the Java programming language, use a library like Butter Knife to avoid boilerplate code and have a better abstraction.

* 使用Data Binding,在你的views和UI controllers之间,维护一个干净的interface。这让你可以使你的views更加declarative，并且最小化你需要写在activities和fragments中的更新代码。吐过你更新在Java programming language中做这个，使用像Butter Knife的库来避免样板代码并且拥有一个更好的抽象。

* If your UI is complex, consider creating a presenter class to handle UI modifications. This might be a laborious task, but it can make your UI components easier to test.

* 如果你的UI复杂，考虑创建一个presenter类来处理UI改变。这可能是一个费力的任务，但是它可以让你的UI组件更容易测试。

* Avoid referencing a View or Activity context in your ViewModel. If the ViewModel outlives the activity (in case of configuration changes), your activity leaks and isn't properly disposed by the garbage collector.

* 避免在ViewModel中引用View或者Activity context。如果ViewModel超越了activity(在配置变化的情况下)，你的activity泄漏了并且没有被垃圾收集器合适的处理。

##Use cases for lifecycle-aware components

Lifecycle-aware components can make it much easier for you to manage lifecycles in a variety of cases. A few examples are:

在很多情况下,Lifecycle-aware components可以让你更简单的管理lifecycles：

* Switching between coarse and fine-grained location updates. Use lifecycle-aware components to enable fine-grained location updates while your location app is visible and switch to coarse-grained updates when the app is in the background. LiveData, a lifecycle-aware component, allows your app to automatically update the UI when your user changes locations.

* 在粗略的和细颗粒度的位置更新之间切换。使用lifecycle-aware组件，当你的位置app可见的时候使用细颗粒度的位置更新，当app在后台的时候切换到粗颗粒度的更新。
 
* Stopping and starting video buffering. Use lifecycle-aware components to start video buffering as soon as possible, but defer playback until app is fully started. You can also use lifecycle-aware components to terminate buffering when your app is destroyed.
* 停止和开始视频缓冲。使用lifecycle-aware组件来尽可能快的开始视频缓冲，但是直到app完全start之前延迟播放。当你的app destroy的时候，你也可以使用lifecycle-aware组件来终止缓冲，

* Starting and stopping network connectivity. Use lifecycle-aware components to enable live updating (streaming) of network data while an app is in the foreground and also to automatically pause when the app goes into the background.

* 开始和停止网络连接。使用lifecycle-aware组件，当app在前台的时候，使网络数据的实时更新，然后在app进入后台的时候自动暂停

* Pausing and resuming animated drawables. Use lifecycle-aware components to handle pausing animated drawables when while app is in the background and resume drawables after the app is in the foreground.
* 暂停和resuming animated drawables.使用 lifecycle-aware components，在app在在后台的是时候来处理暂停动画drawables，然后在app进入前台的时候resume drawables。

## Handling on stop events

When a Lifecycle belongs to an AppCompatActivity or Fragment, the Lifecycle's state changes to CREATED and the ON_STOP event is dispatched when the AppCompatActivity or Fragment's onSaveInstanceState() is called.

当Lifecycles属于一个AppCpmpatActivity或者Fragment，Lifecycle的状态变化到CREATED，并且当AppCompatActivity或者Fragment的onSavaInstanceState()被调用的时候，ON_STOP事件被分发。

When a Fragment or AppCompatActivity's state is saved via onSaveInstanceState(), it's UI is considered immutable until ON_START is called. Trying to modify the UI after the state is saved is likely to cause inconsistencies in the navigation state of your application which is why FragmentManager throws an exception if the app runs a FragmentTransaction after state is saved. See commit() for details.

当一个Fragment或者AppCompatActivity的状态通过onSaveInstanceState()被保存，它的UI被认为是不可变的，直到ON_START方法被调用。尝试在状态被保存后修改UI可能会被引起你的application导航状态不一致，这就是为什么当app在状态保存后运行FragmentTransaction会引起FragmnetManager扔出异常。See [commit()](https://developer.android.google.cn/reference/android/support/v4/app/FragmentTransaction#commit())for details.

LiveData prevents this edge case out of the box by refraining from calling its observer if the observer's associated Lifecycle isn't at least STARTED. Behind the scenes, it calls isAtLeast() before deciding to invoke its observer.

LiveData的observer关联的LifeCycle如果不是至少在STARTED状态，LiveData会通过制止它的观察者调用来阻止边缘案例发生。在这些场景后面，它在决定调用它的observer之前，她调用了isAtLeast()。

Unfortunately, AppCompatActivity's onStop() method is called after onSaveInstanceState(), which leaves a gap where UI state changes are not allowed but the Lifecycle has not yet been moved to the CREATED state.

不幸的是，AppCompatAcivity的onStop()方法在onSaveInstanceState()之后调用，这导致了一个gap，UI的状态变化是不被允许的，但是LifeCycle还没有被改变到CREATED state。

To prevent this issue, the Lifecycle class in version beta2 and lower mark the state as CREATED without dispatching the event so that any code that checks the current state gets the real value even though the event isn't dispatched until onStop() is called by the system.

为了阻止这种情况，Lifecycle 类在beta2和更低的版本在没有分发事件的情况下标记状态为CREATED，以便任何代码可以检查当前状态，拿到真正的值，即使事件直到onStop()被系统调用才会分发。

Unfortunately, this solution has two major problems:

不幸的是，这个解决方法有两个主要问题：

* On API level 23 and lower, the Android system actually saves the state of an activity even if it is partially covered by another activity. In other words, the Android system calls onSaveInstanceState() but it doesn't necessarily call onStop(). This creates a potentially long interval where the observer still thinks that the lifecycle is active even though its UI state can't be modified.

* On API level 23 and lower, Android 系统确实保存了一个activity的状态，即使被另一个activity部分覆盖了。换句话说，Android 系统调用onSaveInstance()但是他没有必要调用onStop(),这创造了一个潜在的长期间隔，observer仍然任务lifecycle是活跃的，即使它的UI不能被修改。

* Any class that wants to expose a similar behavior to the LiveData class has to implement the workaround provided by Lifecycle version beta 2 and lower.
* 任何想要暴露类似行为给LiveData的类不得不实现由Lifecycle version beta 2 and lower 的解决方案。

上次更新日期：十月 2, 2018













