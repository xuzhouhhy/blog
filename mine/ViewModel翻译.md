# ViewModel Overview
[翻译链接](https://developer.android.google.cn/topic/libraries/architecture/viewmodel)

The ViewModel class is designed to store and manage UI-related data in a lifecycle conscious way. The ViewModel class allows data to survive configuration changes such as screen rotations.

ViewModel类设计用来以生命周期友好的方式存储和管理UI-关联的数据。ViewModel类允许数据在配置改变的情况下保存下来，例如屏幕方向改变。

The Android framework manages the lifecycles of UI controllers, such as activities and fragments. The framework may decide to destroy or re-create a UI controller in response to certain user actions or device events that are completely out of your control.

Android框架管理UI控件的生命周期，例如activities和fragmengs.在一些特定的用户行为或者设备事件下，框架可能决定销毁或者重新创建UI控件，完全脱离了开发者的控制。

If the system destroys or re-creates a UI controller, any transient UI-related data you store in them is lost. For example, your app may include a list of users in one of its activities. When the activity is re-created for a configuration change, the new activity has to re-fetch the list of users. For simple data, the activity can use the onSaveInstanceState() method and restore its data from the bundle in onCreate(), but this approach is only suitable for small amounts of data that can be serialized then deserialized, not for potentially large amounts of data like a list of users or bitmaps.

如果系统销毁或者重新创建了一个UI控件，任何存储在短暂的UI-关联的数据消失了，例如，你的app可能包括一系列用户在他的activities中的一个。当这个activity在配置变化的时候重建了，新的activity不得不重新拉去用户列表。对于简单数据，这个activity可以使用onSaveInstanceState()方法并且使用onCreate()的bundle恢复他的数据，但是这个方式仅仅适合可以背序列化的小量数据，不适用潜在的大量数据，比如一系列用户或者bitmaps。

Another problem is that UI controllers frequently need to make asynchronous calls that may take some time to return. The UI controller needs to manage these calls and ensure the system cleans them up after it's destroyed to avoid potential memory leaks. This management requires a lot of maintenance, and in the case where the object is re-created for a configuration change, it's a waste of resources since the object may have to reissue calls it has already made.

另一个问题是UI控件经常需要使用异步的calls，可能需要一些时间才可以返回结果。UI控件需要管理这些calls并且确保在控件被销毁后系统清理了他们，以比年潜在的内存泄漏。这个管理需要许多的维护，并且如果这个实例在配置改变的时候重新创建了，这是资源的浪费，因为这个实例可能不得不重新开启已经创造的calls。

UI controllers such as activities and fragments are primarily intended to display UI data, react to user actions, or handle operating system communication, such as permission requests. Requiring UI controllers to also be responsible for loading data from a database or network adds bloat to the class. Assigning excessive responsibility to UI controllers can result in a single class that tries to handle all of an app's work by itself, instead of delegating work to other classes. Assigning excessive responsibility to the UI controllers in this way also makes testing a lot harder.

It's easier and more efficient to separate out view data ownership from UI controller logic.

UI控件例如activities和fragmengs仅仅是用来设计展示UI数据，响应用户操作，或者处理和系统的通信，例如权限申请。要求UI控件对从数据库或者网络加载数据负责是这个类膨胀。分配过多的职责给UI控件可能导致一个单独的类尝试自己单独处理一个app的所有工作。使用这种方式给一个UI控件分配过多的职责也会使测试更加困难。

把我们的view数据拥有者从UI控件逻辑分开是更简单和有效的。

## Implement a ViewModel
Architecture Components provides ViewModel helper class for the UI controller that is responsible for preparing data for the UI. ViewModel objects are automatically retained during configuration changes so that data they hold is immediately available to the next activity or fragment instance. For example, if you need to display a list of users in your app, make sure to assign responsibility to acquire and keep the list of users to a ViewModel, instead of an activity or fragment, as illustrated by the following sample code:

架构组件为UI控件提供了ViewModel帮助类，这是为帮助给UI准备数据的情况。ViewModel类在配置改变的时候自动保留，以便他们hold的数据可以被下一个activity或者fragment实例立即拿到。例如，如果你需要在你的app展示一个用户列表，确保分配责任以便ViewMdoel拿到和保有数据列表，而不是fragment或者activity拿到，使用如下代码展示：

	public class MyViewModel extends ViewModel {
    private MutableLiveData<List<User>> users;
    public LiveData<List<User>> getUsers() {
        if (users == null) {
            users = new MutableLiveData<List<User>>();
            loadUsers();
        }
        return users;
    }

    private void loadUsers() {
        // Do an asynchronous operation to fetch users.
    }
	}

You can then access the list from an activity as follows:
你可以像下面这个从activity拿到列表：

	public class MyActivity extends AppCompatActivity {
    public void onCreate(Bundle savedInstanceState) {
        // Create a ViewModel the first time the system calls an activity's onCreate() method.
        // Re-created activities receive the same MyViewModel instance created by the first activity.

        MyViewModel model = ViewModelProviders.of(this).get(MyViewModel.class);
        model.getUsers().observe(this, users -> {
            // update UI
        });
    }
	}
	
If the activity is re-created, it receives the same MyViewModel instance that was created by the first activity. When the owner activity is finished, the framework calls the ViewModel objects's onCleared() method so that it can clean up resources.

如果activity被重建了，它收到了同样的MyViewModel实例，这个实例就是被第一个Activity创建的。当owner activity finished，框架调用ViewModel实例的onCleared()方法一遍他可以清理资源。

Caution: A ViewModel must never reference a view, Lifecycle, or any class that may hold a reference to the activity context.

注意：一个ViewMoel永远不可以引用一个view，lifeCycle，或者任何可能hold住activity context的类。

ViewModel objects are designed to outlive specific instantiations of views or LifecycleOwners. This design also means you can write tests to cover a ViewModel more easily as it doesn't know about view and Lifecycle objects. ViewModel objects can contain LifecycleObservers, such as LiveData objects. However ViewModel objects must never observe changes to lifecycle-aware observables, such as LiveData objects. If the ViewModel needs the Application context, for example to find a system service, it can extend the AndroidViewModel class and have a constructor that receives the Application in the constructor, since Application class extends Context.

ViewModel类设计活得比view或者LifecycleOwners实例更久。这个设计也意味着你可以写测试来更简单的覆盖一个ViewModel因为它不知道view和Lifecycle类。ViewModel类可以包含LifecycleObservers,例如LiveData类。然而ViewModel类永远不能观察lifecycle-aware observables，例如LiveData类。如果ViewModel需要Application context，例如拿到一个系统service，它可以继承AndroidViewModel类并且使用构造器拿到Application，因为Application类继承了Context。

## The lifecycle of a ViewModel

ViewModel objects are scoped to the Lifecycle passed to the ViewModelProvider when getting the ViewModel. The ViewModel remains in memory until the Lifecycle it's scoped to goes away permanently: in the case of an activity, when it finishes, while in the case of a fragment, when it's detached.

ViewModelProvider拿到ViewModel的时候，ViewModel类作用于ViewModelProvider传递给的LifeCycle。ViewModel保留在内存中直到LifeCycle的作用域永久消失:在activity中，对应finishes,在fragment中，对应于detached.

Figure 1 illustrates the various lifecycle states of an activity as it undergoes a rotation and then is finished. The illustration also shows the lifetime of the ViewModel next to the associated activity lifecycle. This particular diagram illustrates the states of an activity. The same basic states apply to the lifecycle of a fragment.

图1说明了一个activity在旋转然后finsih情况下，多种生命周期状态。这个说明也展示了ViewModel在关联到activity生命周期是后，ViewModel的生命周期。这个特殊的图标说明了activity的状态。同样的基础状态适用于fragmeng的生命周期。

You usually request a ViewModel the first time the system calls an activity object's onCreate() method. The system may call onCreate() several times throughout the life of an activity, such as when a device screen is rotated. The ViewModel exists from when you first request a ViewModel until the activity is finished and destroyed.

你经常第一次request ViewModel在系统调用activity类的onCreate方法。系统肯能多次调用onCreate方法，在activity的生命周期过程中，例如一个设备屏幕呢旋转了。ViewModel存在于你第一次请求ViewModel直到activity finish 并且 destroyed。

## Share data between fragments

It's very common that two or more fragments in an activity need to communicate with each other. Imagine a common case of master-detail fragments, where you have a fragment in which the user selects an item from a list and another fragment that displays the contents of the selected item. This case is never tvial as both fragments need to define some interface description, and the owner activity must bind the two together. In addition, both fragments must handle the scenario where the other fragment is not yet created or visible.

一个activity中的两个或者多个frament需要相互通信是非常普遍的。想象一个常用的master-detail fragment 使用情况，你有一个fragment，用户从一个列表中选择了一个条目，另一个fragment展示选择条目的内容。这种案例十分重要因为两个fragment需要定义一些接口描述，并且这个owner activity必须绑定在一起。在一些情况下，两个fagments必须处理另外的fragment还没有创建或者不可见的情况。

This common pain point can be addressed by using ViewModel objects. These fragments can share a ViewModel using their activity scope to handle this communication, as illustrated by the following sample code:

这种常见的痛点可能通过ViewModel类解决。这些fragments可能分享一个ViewModel通过他们activity作用域来处理通信，通过如下的代码说明：

	public class SharedViewModel extends ViewModel {
    private final MutableLiveData<Item> selected = new MutableLiveData<Item>();

    public void select(Item item) {
        selected.setValue(item);
    }

    public LiveData<Item> getSelected() {
        return selected;
    }
	}


	public class MasterFragment extends Fragment {
    private SharedViewModel model;
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        model = ViewModelProviders.of(getActivity()).get(SharedViewModel.class);
        itemSelector.setOnClickListener(item -> {
            model.select(item);
        });
    }
	}

	public class DetailFragment extends Fragment {
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        SharedViewModel model = ViewModelProviders.of(getActivity()).get(SharedViewModel.class);
        model.getSelected().observe(this, item -> {
           // Update the UI.
        });
    }
	}
	
	
Notice that both fragments use getActivity() when getting the ViewModelProvider. As a result, both fragments receive the same SharedViewModel instance, which is scoped to the activity.

当正在拿ViewModelProvider的时候，注意两个fragmengs使用getActivity()。两个fragments收到了同一个SharedViewModel 实例，它是和作用域activity的。

This approach offers the following benefits:

The activity does not need to do anything, or know anything about this communication.
Fragments don't need to know about each other besides the SharedViewModel contract. If one of the fragments disappears, the other one keeps working as usual.
Each fragment has its own lifecycle, and is not affected by the lifecycle of the other one. If one fragment replaces the other one, the UI continues to work without any problems.

这个方式提供了如下好处：
activity不需要做任何事情，或者知道关于通信的任何事情。
在SharedViewModel 的连接下 Fragment不需要相互知道。如果其中一个frament消失了，另一个照常工作。
每一个fragment有他自己的lifecycle，并且是不受另一个影响的。吐过一个fragment替代了另一个，UI继续工作，没有任何问题。

## Replacing Loaders with ViewModel

Loader classes like CursorLoader are frequently used to keep the data in an app's UI in sync with a database. You can use ViewModel, with a few other classes, to replace the loader. Using a ViewModel separates your UI controller from the data-loading operation, which means you have fewer strong references between classes.

加载者类像CursorLoader通常被用来保留一个app UI的数据，数据和一个database同步。你可以使用ViewModel，使用一些其他的类来代替loader。使用ViewModel把你的UI控件从data-loading操作分开，这意味着你有更少的类之间的强引用。

In one common approach to using loaders, an app might use a CursorLoader to observe the contents of a database. When a value in the database changes, the loader automatically triggers a reload of the data and updates the UI:

在一个通常的使用loaders的路径中，一个app可能使用CursorLoader来观察一个database的内容。当一数据库的一个值改变的时候，loader自动开始重新加载数据和更新UI：

ViewModel works with Room and LiveData to replace the loader. The ViewModel ensures that the data survives a device configuration change. Room informs your LiveData when the database changes, and the LiveData, in turn, updates your UI with the revised data.

ViewModel和Room和LiveData一起工作来代替loader.ViewModel确保在设备配置变化的时候数据存活。当数据库变化，Room通知你的LiveData，并且livedata反过来用修订后的数据更新你的UI。

##Additional resources

This blog post describes how to use a ViewModel with a LiveData to replace an AsyncTaskLoader.

As your data grows more complex, you might choose to have a separate class just to load the data. The purpose of ViewModel is to encapsulate the data for a UI controller to let the data survive configuration changes. For information about how to load, persist, and manage data across configuration changes, see Saving UI States.

The Guide to Android App Architecture suggests building a repository class to handle these functions.

ViewModel is an Android Jetpack architecture component. See it in use in the Sunflower demo app.

这个blog传递了怎样去和livedata一起使用ViewModel来替换AsyncTaskLoader.
随着你的数据曾长的更复杂，你可能会选择使用一个单独的class仅仅用来加载数据。ViewModel的目的就是去封装为UI控件使用的data，让数据在配置变化的时候存活。关于怎样加载，持有，配置变化的时候管理数据，浏览[Saving UI States](https://developer.android.google.cn/topic/libraries/architecture/saving-states)

Android App Architecture 的guide 建议建造一个仓库class来处理这些功能。

ViewModel是一个Android Jetpack 架构组件。了解关于使用在[Sunflower](https://github.com/googlesamples/android-sunflower) demo app