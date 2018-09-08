#Android开发艺术指南
##Activity的生命周期和启动模式
1. Activity a启动Activity b，a onResume -> b onCreate -> b onStart -> b onResume -> a onStop
2. onSaveInstanceState在onStop前调用，onRestoreInstanceState在onStart之后调用，说明保存和恢复数据的工作都是在activity可见的情况下调用

##IPC
###Messenger
1. Messenger是什么？[翻译]
  * Reference to a Handler, which others can use to send messages to it.This allows for the implementation of message-based communication across processes, by creating a Messenger pointing to a Handler in one process,and handing that Messenger to another process.<p>Note: the implementation underneath is just a simple wrapper around a {@link Binder} that is used to perform the communication.  This means semantically you should treat it as such: this class does not impact process lifecycle management (you must be using some higher-level component to tell the system that your process needs to continue running), the connection will break if your process goes away for any reason, etc.</p>
Handle的一个引用，其他的Handler可以给它发送menssages.通过创建一个指向一个进程中的Handler，并且拿到另一个的进程的Messenger,可以实现message-based的IPC.底层的实现仅仅是对Binbder的简单包装，Binder是用来实现通信的。这在语义上一位置你应该这样对待它：这个类不影响进程的生命周期管理（你必须通过使用一个写更高级别的组件来告诉系统你的进程需要继续执行），如果你的进程因为任何原因走掉了，这个链接就会断开。


###AIDL
1. Android Studio的工程的aidl文件必须在特定的文件夹中才能编译生成对应的.java文件
2. [翻译]ReomteCallbackList是什么？
Takes care of the grunt work of maintaining a list of remote interfaces, typically for the use of performing callbacks from a Service to its clients. In particular, this:

Keeps track of a set of registered IInterface callbacks, taking care to identify them through their underlying unique IBinder (by calling IInterface.asBinder().
Attaches a IBinder.DeathRecipient to each registered interface, so that it can be cleaned out of the list if its process goes away.
Performs locking of the underlying list of interfaces to deal with multithreaded incoming calls, and a thread-safe way to iterate over a snapshot of the list without holding its lock.
To use this class, simply create a single instance along with your service, and call its register(E) and unregister(E) methods as client register and unregister with your service. To call back on to the registered clients, use beginBroadcast(), getBroadcastItem(int), and finishBroadcast().

If a registered callback's process goes away, this class will take care of automatically removing it from the list. If you want to do additional work in this situation, you can create a subclass that implements the onCallbackDied(E) method.

维护一个远程接口的列表，这些接口是为了Service对他的Clents进行远程调用。包括如下
* 保持追踪已注册的回掉接口的集合，通过他们唯一的IBinder找到唯一的接口
* 拿到每一个注册接口的IBinder.DeathRecipient，当进程go away的时候，用来把它清除出列表
* 通过锁住接口底层list来处理多线程的inconming调用，并且可以thread-safe的不拿到list的lock的进行迭代list的快照
使用，和service一起创建单个实例，通过register和ungister在service注册client。通过beginBroadcast(), getBroadcastItem(int), and finishBroadcast()可以进行注册的客户端的回调