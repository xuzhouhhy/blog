# Bilibli Android 客户端优化

## 原理：Android Framework 对 Acitivity 的回收

当Android Framework决定回收Activity，会执行以下操作：

 1. 执行Activity的生命周期，onDestroy()，AF回收系统资源
 2. Android Framework 放开对Activity的引用，虚拟机在GC的时候可以回收Activity对象.如果你有还有对Activity的强引用，就会造成内存泄漏。

## 优化：在系统回收资源的时候手动回收资源

AF回收Activity的时候会执行生命周期，所以可以在生命周期中回收使用到的资源，包括Activity应用的字段，比如：Bitmap、String等.但是在回到这个Activity的时候为了恢复场景，需要将这些保存的数据都恢复，所以这是一个需要很大工作量工程。
