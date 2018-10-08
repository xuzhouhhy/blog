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

你可以使用单例模式继承LiveData 类来包裹系统services，以便于他们可以在你的app内部共享。LiveData类一旦连接到系统service一次，并且任何需要这个资源的observer仅仅观察这个LiveData类就可以。获取跟多信息，see [Extend LiveData](https://developer.android.com/topic/libraries/architecture/livedata#extend_livedata)

您可以使用单例模式扩展LiveData对象以包装系统服务，以便可以在应用程序中共享它们。 LiveData对象连接到系统服务一次，然后任何需要该资源的观察者都可以只观看LiveData对象。 有关更多信息，请参阅扩展LiveData。

## Work with LiveData objects

Follow these steps to work with LiveData objects:

遵循这些步骤使用LiveData：

 1. Create an instance of LiveData to hold a certain type of data. This is usually done within your ViewModel class.
 2. Create an Observer object that defines the onChanged() method, which controls what happens when the LiveData object's held data changes. You usually create an Observer object in a UI controller, such as an activity or fragment.
 3. Attach the Observer object to the LiveData object using the observe() method. The observe() method takes a LifecycleOwner object. This subscribes the Observer object to the LiveData object so that it is notified of changes. You usually attach the Observer object in a UI controller, such as an activity or fragment.

 1. 创建一个LiveData实例来持有一个特定类型的数据。这通常用ViewModel类来做
 2. 创建一个定义了onChanged()方法的Observer类，当LiveData类持有的数据改变了，这个方法控制了怎样响应。你通常在一个UI 控件中创建一个Observer，例如一个activity或者fragment。
 3. 使用observe()方法来attach到Observer类。observe()方法拿到了一个LifecycleOwner类。这订阅了LiveData类的Observer类以便它可以被改变触发。你通常在一个UI 控件中attach the Observer 类，例如一个activity或者fragment.

Note: You can register an observer without an associated LifecycleOwner object using the observeForever(Observer) method. In this case, the observer is considered to be always active and is therefore always notified about modifications. You can remove these observers calling the removeObserver(Observer) method.
When you update the value stored in the LiveData object, it triggers all registered observers as long as the attached LifecycleOwner is in the active state.

注意：您可以使用observeForever（Observer）方法注册没有关联的LifecycleOwner对象的观察者。在这种情况下，the observer 被认为总是active并且因此总是被修改所触发。你可以使用removeObserver(Observer)方法移除这些observers。当你在LiveData更新存储值，只要attached的LifecycleOwner在active状态，它触发所有已注册的observers。

LiveData allows UI controller observers to subscribe to updates. When the data held by the LiveData object changes, the UI automatically updates in response.

LiveData允许UI控件observers来订阅更新。当LiveData持有的数据改变，相应的，UI会自动更新。

## Create LiveData objects
创建LiveData类

LiveData is a wrapper that can be used with any data, including objects that implement Collections, such as List. A LiveData object is usually stored within a ViewModel object and is accessed via a getter method, as demonstrated in the following example:

LiveData是可以包裹任何数据，包括实现了Collections的类，例如List。LiveData对象通常存储在ViewModel对象中，并通过getter方法访问，如以下示例所示：


	class NameViewModel : ViewModel() {
  
    // Create a LiveData with a String
    val currentName: MutableLiveData<String> by lazy {
        MutableLiveData<String>()
    }

    // Rest of the ViewModel...
	}

Initially, the data in a LiveData object is not set.

初始化是，LiveData类中的数据没有被设置。

Note: Make sure to store LiveData objects that update the UI in ViewModel objects, as opposed to an activity or fragment, for the following reasons:
To avoid bloated activities and fragments. Now these UI controllers are responsible for displaying data but not holding data state.
To decouple LiveData instances from specific activity or fragment instances and allow LiveData objects to survive configuration changes.
You can read more about the benefits and usage of the ViewModel class in the ViewModel guide.

注意：确保存储在ViewModel类中的是更新UI的LiveData类，而不是一个activity或者fragment，有如下原因：
避免臃肿的activities和fragments。现在这些UI控件的职责是展示数据而不是holding住数据状态。将LiveData实例从特定的activity或者fragment实例分离开来，并且允许LiveData类在配置变化的时候存活。
你可以在ViewModel guide中阅读更多关于使用ViewModel类的好处

## Observe LiveData objects

In most cases, an app component’s onCreate() method is the right place to begin observing a LiveData object for the following reasons:

在大多数案例中，一个app组件的onCreate()方法是开始observing一个LiveData类的正确位置，有如下原因

 * To ensure the system doesn’t make redundant calls from an activity or fragment’s onResume() method.

 * 为了确保系统没有从activity或者fragment的resume()方法进行冗余的调用

 * To ensure that the activity or fragment has data that it can display as soon as it becomes active. As soon as an app component is in the STARTED state, it receives the most recent value from the LiveData objects it’s observing. This only occurs if the LiveData object to be observed has been set.

 * 为了确保已经有数据的activity或者fragment可以在活跃的时候立即展示数据。只要一个app组件在已经开始的状态，它可以从它正在observing的LiveData类收到最近的数值。这仅仅会在LiveData类已经被观察的时候发生。
 
Generally, LiveData delivers updates only when data changes, and only to active observers. An exception to this behavior is that observers also receive an update when they change from an inactive to an active state. Furthermore, if the observer changes from inactive to active a second time, it only receives an update if the value has changed since the last time it became active.

通常情况下，LiveData仅仅在data改变的时候传递updates，并且仅仅对active observers生效。一个对这种行为的期待是observers也

The following sample code illustrates how to start observing a LiveData object:

	class NameActivity : AppCompatActivity() {

    private lateinit var mModel: NameViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Other code to setup the activity...

        // Get the ViewModel.
        mModel = ViewModelProviders.of(this).get(NameViewModel::class.java)


        // Create the observer which updates the UI.
        val nameObserver = Observer<String> { newName ->
            // Update the UI, in this case, a TextView.
            mNameTextView.text = newName
        }

        // Observe the LiveData, passing in this activity as the LifecycleOwner and the observer.
        mModel.currentName.observe(this, nameObserver)
    }
	}
After observe() is called with nameObserver passed as parameter, onChanged() is immediately invoked providing the most recent value stored in mCurrentName. If the LiveData object hasn't set a value in mCurrentName, onChanged() is not called.

























