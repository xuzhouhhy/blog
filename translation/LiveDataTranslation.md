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

通常情况下，LiveData仅仅在data改变的时候传递updates，并且仅仅对active observers生效。当observers从inactive变为active状态的时候，这个时候会发生异常。此外，如果观察者第二次从非活动状态更改为活动状态，则只有在自上次活动状态以来该值发生更改时才会收到更新。

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

在observe()方法带有nameObserver参数的时候被调用，onChanged()会被立即调用来提供最新的存储在mCurrentName中的数值。如果LiveData没有set mCurrentName的值，onChanged()方法不会被调用。

## Update LiveData objects
更新LiveData类

LiveData has no publicly available methods to update the stored data. The MutableLiveData class exposes the setValue(T) and postValue(T) methods publicly and you must use these if you need to edit the value stored in a LiveData object. Usually MutableLiveData is used in the ViewModel and then the ViewModel only exposes immutable LiveData objects to the observers.

LiveData没有公开的可以拿到的方法来更新被存储的数据。MutableLiveData类暴露了setValue(T)和postValue(T)方法，并且如果你需要编辑存储在LiveData类的数值，你必须用这些方法。在ViewModel 中MutableLivaData经常被使用，并且ViewModel仅仅暴露不会改变的LiveData类给observers。

After you have set up the observer relationship, you can then update the value of the LiveData object, as illustrated by the following example, which triggers all observers when the user taps a button:

在你已经设置了observer relationship之后，你可以之后更新LiveData类的值，下面的例子作出说明，当用户按下button出发了所有的observers

	mButton.setOnClickListener {
    val anotherName = "John Doe"
    mModel.currentName.setValue(anotherName)
	}
	
Calling setValue(T) in the example results in the observers calling their onChanged() methods with the value John Doe. The example shows a button press, but setValue() or postValue() could be called to update mName for a variety of reasons, including in response to a network request or a database load completing; in all cases, the call to setValue() or postValue() triggers observers and updates the UI.

例子中调用setValue(T)方法导致observers使用“John Doe”数值调用它们的onChanged()方法。这个例子展示了按下button，但是setValue()或者postValue()方法可能由很多原因调用来更新mName,包括响应一个网络请求，或者数据库加载完成，在所有的案例中，调用setValue()后者postValue()触发observers更新UI

Note: You must call the setValue(T) method to update the LiveData object from the main thread. If the code is executed in a worker thread, you can use the postValue(T) method instead to update the LiveData object.

注意：你必须在main thread调用setValue()方法来更新LivaData类.如果代码是在 a worker thread执行，你可以使用postValue(T)方法来更新LiveData类.

## Use LiveData with Room
和Room一起使用LivaData

The Room persistence library supports observable queries, which return LiveData objects. Observable queries are written as part of a Database Access Object (DAO).

Room持久性库支持可观察的查询，这些查询返回LiveData对象。 可观察查询作为数据库访问对象（DAO）的一部分编写。

Room generates all the necessary code to update the LiveData object when a database is updated. The generated code runs the query asynchronously on a background thread when needed. This pattern is useful for keeping the data displayed in a UI in sync with the data stored in a database. You can read more about Room and DAOs in the Room persistent library guide.

当一个数据库被更新了，Room生成了所有必要的代码来更新LivaData类。生成的代码在需要的时候会在a backfround thread 运行异步的查询。在同步存储在数据库中的数据时，持续展示在UI中，这一模式是有用的。你可以在[the Room persistent library guide](https://developer.android.google.cn/topic/libraries/architecture/room)阅读更多关于Room和DAOs。

## Extend LiveData
扩展LivaData

LiveData considers an observer to be in an active state if the observer's lifecycle is in either the STARTED or RESUMED states The following sample code illustrates how to extend the LiveData class:

LiveData认为observer的lifecycle在started或者resumed状态的时候是在一个active状态。下面的代码说明了怎样扩展LivaData类：

	class StockLiveData(symbol: String) : LiveData<BigDecimal>() {
    private val mStockManager = StockManager(symbol)

    private val mListener = { price: BigDecimal ->
        value = price
    }

    override fun onActive() {
        mStockManager.requestPriceUpdates(mListener)
    }

    override fun onInactive() {
        mStockManager.removeUpdates(mListener)
    }
	}
	
The implementation of the price listener in this example includes the following important methods:

例子中price listener的实现包括以下重要的方法：

The onActive() method is called when the LiveData object has an active observer. This means you need to start observing the stock price updates from this method.

当LiveData类拥有一个活跃的observer ，onActive()方法会被调用。这意味着你需要从这个方法开始observe the stock price 的更新。

The onInactive() method is called when the LiveData object doesn't have any active observers. Since no observers are listening, there is no reason to stay connected to the StockManager service.

当LiveData类没有任何active observers 的时候onInactived()方法被调用。因为没有observer在监听，没有理由来保持连接到StockManager service.

The setValue(T) method updates the value of the LiveData instance and notifies any active observers about the change.

setValue(T)方法更新了LiveData实例的值并且触发所有的active observers对这次改变的响应。

You can use the StockLiveData class as follows:


	override fun onActivityCreated(savedInstanceState: Bundle?) {
    super.onActivityCreated(savedInstanceState)
    val myPriceListener: LiveData<BigDecimal> = ...
    myPriceListener.observe(this, Observer<BigDecimal> { price: BigDecimal? ->
        // Update the UI.
    })
	}
	
The observe() method passes the fragment, which is an instance of LifecycleOwner, as the first argument. Doing so denotes that this observer is bound to the Lifecycle object associated with the owner, meaning:

observe()方法传递了fragment作为第一个参数,fragment是一个LifecycleOwner的实例。这样做表示此观察者绑定到与所有者关联的Lifecycle对象，这意味着：

If the Lifecycle object is not in an active state, then the observer isn't called even if the value changes.

如果LifeCycle类没有在active状态，即使数据改变了，然后observer不会被调用。

After the Lifecycle object is destroyed, the observer is automatically removed.
The fact that LiveData objects are lifecycle-aware means that you can share them between multiple activities, fragments, and services. To keep the example simple, you can implement the LiveData class as a singleton as follows:

在LifeCycle类is destroyed后，observer自动移除。LiveData是lifecycle-aware的事实意味着你可以在多个activities、fragments、services之间分享它们。为了让例子简单，你可以实现LiveData类作为一个单例：

	class StockLiveData(symbol: String) : LiveData<BigDecimal>() {
    private val mStockManager: StockManager = StockManager(symbol)

    private val mListener = { price: BigDecimal ->
        value = price
    }

    override fun onActive() {
        mStockManager.requestPriceUpdates(mListener)
    }

    override fun onInactive() {
        mStockManager.removeUpdates(mListener)
    }

    companion object {
        private lateinit var sInstance: StockLiveData

        @MainThread
        fun get(symbol: String): StockLiveData {
            sInstance = if (::sInstance.isInitialized) sInstance else StockLiveData(symbol)
            return sInstance
        }
    }
	}
	
And you can use it in the fragment as follows:

	class MyFragment : Fragment() {

    override fun onActivityCreated(savedInstanceState: Bundle?) {
        StockLiveData.get(symbol).observe(this, Observer<BigDecimal> { price: BigDecimal? ->
            // Update the UI.
        })

    }
    
Multiple fragments and activities can observe the MyPriceListener instance. LiveData only connects to the system service if one or more of them is visible and active.

多个fragments和activities可以observe MyPriceListener实例。如果其中一个或多个是visible或者active，LiveData就会连接到system service.

##Transform LiveData



You may want to make changes to the value stored in a LiveData object before dispatching it to the observers, or you may need to return a different LiveData instance based on the value of another one. The Lifecycle package provides the Transformations class which includes helper methods that support these scenarios.

在分发到observers之前，你可能想要改变存储在LiveData类中的数据，或者你可能需要返回一个不同的LiveData实例，建立在另一个数值的基础上。LifeCycle包提供了Transformation类，包括帮助方法来支持这些场景。

Transformations.map()

Applies a function on the value stored in the LiveData object, and propagates the result downstream.

对存储在LiveData对象中的值应用函数，并将结果传播到下游。

	val userLiveData: LiveData<User> = UserLiveData()
	val userName: LiveData<String> = Transformations.map(userLiveData) {
    user -> "${user.name} ${user.lastName}"
	}

Transformations.switchMap()

Similar to map(), applies a function to the value stored in the LiveData object and unwraps and dispatches the result downstream. The function passed to switchMap() must return a LiveData object, as illustrated by the following example:

类似于map(),对存储在LiveData中的数值应用函数，并且拆分和分发这些结果到下游。

	private fun getUser(id: String): LiveData<User> {
	...
	}
		val userId: LiveData<String> = ...
	val user = Transformations.switchMap(userId) 	{ id -> getUser(id) }


You can use transformation methods to carry information across the observer's lifecycle. The transformations aren't calculated unless an observer is watching the returned LiveData object. Because the transformations are calculated lazily, lifecycle-related behavior is implicitly passed down without requiring additional explicit calls or dependencies.

你可以在observer的lifecycle中使用transformation methods来carry信息。transformations是不可计算的，除非observer正在watching返回的LiveData类。因为transformations是lazily计算的，lifecycle-related行为是隐式传递，不需要明确的嗲用或者依赖。

If you think you need a Lifecycle object inside a ViewModel object, a transformation is probably a better solution. For example, assume that you have a UI component that accepts an address and returns the postal code for that address. You can implement the naive ViewModel for this component as illustrated by the following sample code:

如果你认为在ViewModel类中需一个LifeCycle类，transformation可能是更好的解决方案。例如，假设你有一个UI组件是接收一个地址并且返回这个地址的邮政编码。你可以为这个组件实现the native ViewModel，下面的代码作为说明：


	class MyViewModel(private val repository: PostalCodeRepository) : ViewModel() {

    private fun getPostalCode(address: String): LiveData<String> {
        // DON'T DO THIS
        return repository.getPostCode(address)
    }
	}
	
The UI component then needs to unregister from the previous LiveData object and register to the new instance each time it calls getPostalCode(). In addition, if the UI component is recreated, it triggers another call to the repository.getPostCode() method instead of using the previous call’s result.

UI组件人后需要对之前的LiveData类进行解注册，并且它每次调用getPostalCode()的时候注册到新的实例。此外，如果重新创建UI组件，它将触发另一个对repository.getPostCode（）方法的调用，而不是使用前一个调用的结果。

Instead, you can implement the postal code lookup as a transformation of the address input, as shown in the following example:

相反地，你可以把输入地址的转换查找the postal code,如下代码展示：

	class MyViewModel(private val repository: PostalCodeRepository) : ViewModel() {
    private val addressInput = MutableLiveData<String>()
    private val postalCode: LiveData<String> =
            Transformations.switchMap(addressInput) { address -> repository.getPostCode(address) }


    private fun setInput(address: String) {
        addressInput.value = address
    }
	}
	
In this case, the postalCode field is public and final, because the field never changes. The postalCode field is defined as a transformation of the addressInput, which means that the repository.getPostCode() method is called when addressInput changes. This is true if there is an active observer, if there are no active observers at the time repository.getPostCode() is called, no calculations are made until an observer is added.

在这个案例中,the postalCode field 是public 、final,因为这个field永远不会改变。The postalCode field 被定义为地址输入的转拜年，这意味着当输入地址变化的时候，repository.getPostCode()方法被调用。当有一个active observer的时候，这是正确的，如果repository.getPostCode()被调用的时候，没有 active observers，直到一个observer被added之后才会有计算发生。

This mechanism allows lower levels of the app to create LiveData objects that are lazily calculated on demand. A ViewModel object can easily obtain references to LiveData objects and then define transformation rules on top of them.	
这种机制允许更低级别的app来创建通过命令计算懒加载的LiveData类。一个ViewModel类可以简单的获得LiceData类的引用，并且之后定义他们的转换规则。

###Create new transformations

创建新的转换

There are a dozen different specific transformation that may be useful in your app, but they aren’t provided by default. To implement your own transformation you can you use the MediatorLiveData class, which listens to other LiveData objects and processes events emitted by them. MediatorLiveData correctly propagates its state to the source LiveData object. To learn more about this pattern, see the reference documentation of the Transformations class.

在你的app中，有许多不同的特定的转换可能会被用到，但是没有默认提供的。你可以使用MediatorLiveData类来实现你自己的转换，这个类坚挺其他的LivaData类并且处理它们发射的事件。MediatorLiveData正确的传播它的状态给源LiveData类，see the reference documentation of [Transformations](https://developer.android.google.cn/reference/android/arch/lifecycle/Transformations) cladd,来了解更多这种模式。

##Merge multiple LiveData sources

合并多个LiveData源

MediatorLiveData is a subclass of LiveData that allows you to merge multiple LiveData sources. Observers of MediatorLiveData objects are then triggered whenever any of the original LiveData source objects change.

MediatorLiveData是LiveData的超类，它允许你喝冰多个LiveData源。无论何时任意的远端的LiveData源改变了，Observers of MediatorLiveData objects都会被触发。

For example, if you have a LiveData object in your UI that can be updated from a local database or a network, then you can add the following sources to the MediatorLiveData object:

例如，如果你在UI上有可以被本地数据库或者网络更新的LiveData类，然后你可以添加如下的源到MediatorLiveData类

 * A LiveData object associated with the data stored in the database.
 * 一个关联存储在数据库中数据的LiveData
 * A LiveData object associated with the data accessed from the network.
 * 一个关联网络拿到数据的LiveData
 
Your activity only needs to observe the MediatorLiveData object to receive updates from both sources. For a detailed example, see the Addendum: exposing network status section of the Guide to App Architecture.

你的activity仅仅需要观察MediatorLiveData类就可以接受两个来源的更新么。关于细节描述，看附录：暴漏应用架构治安案的网络状态部分。

## Additional resources

额外的资源

LiveData is an Android Jetpack architecture component. See it in use in the Sunflower demo app.

For additional information on using LiveData with Snackbar messages, navigation events, and other events, read this post.

关于和Snackbar messages,navigation events,或者其他事件一起使用LivaDat，read this [post](https://medium.com/androiddevelopers/livedata-with-snackbar-navigation-and-other-events-the-singleliveevent-case-ac2622673150)得到更多的信息。

google update time 2018年10月2日



















