# Android 照片墙

##Glide 使用和源码

### 源码
#### Glide.with()
1. Glide.class is a singleton,lock is Glide.class
2. isInitializing variable is volatile,to avoid call Glide.get() in registerComponents()
3. RequestManagerRetriever.class to retriever RequestManager
4. RequestManaer 的实例数量和图片请求数量一致

#### RequestManager.load()
1. 每一个图片加载都有对应的ReauestBuilder实例,在RequestManager.as()生成
2. RequestBuilder.class包含了大多数加载图片的api
3. RequestManager.load()实际调用的是RequestBuilder.load()

## 参考项目

[TakePhoto](https://github.com/crazycodeboy/TakePhoto)

1. 使用ContentResolver获取Album数据和Album下的iamges数据。
2. 使用Glide加载图片，Glide完成了什么工作。图片获取尺寸？内存击中优化？本地图库是否需要图片缓存？图片缓存？

[ImagePicker](https://github.com/jeasonlzy/ImagePicker)

1. 使用LoaderManager加载数据，底层也是使用ContentProvider的内容共享。
2. 支持Glide，Picasso加载图片。也有上面同样的疑问。

## LoaderManager

[Google Android Developer guide 加载器](https://developer.android.com/guide/components/loaders?hl=zh-cn)

[Goolge reference](https://developer.android.com/reference/android/support/v4/app/LoaderManager)


## 参考文章
[Android高效加载大图、多图解决方案，有效避免程序OOM](https://blog.csdn.net/guolin_blog/article/details/9316683)

[Golde vs Picasso Google推荐的图片加载库Glide介绍](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0327/2650.html)

[Handling bitmaps](https://developer.android.com/topic/performance/graphics/)

## ImageView

