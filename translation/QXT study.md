# QXT study
## 多进程

## Account
AccountManager.java

## 快速建课
### PPT+短音频页面第二次进入如何定位音频
难点

* 编辑模式下，第二次进入后PPT+短音频页面，需要将新添加的内容加载在最后。

方案

* 先加载尾部数据，支持下拉加载顶部输入，参考聊天记录。但是在上课模式下，不支持切换ppt切换数据
* 最终方案：预加载全部数据，包括id，rank，对应的ppt页id。滑动时加载对应条目的全部数据。

#### 如何分页加载课程数据

* 如何加载每个item的详细内容，比如图片、文字、短音频（id、时长、是否播放、播放链接等信息）。

方案

1. 维护一个请求课程数据的网络请求栈，在单线程中执行。单线程是因为只需请求当前页面的条目数据。每次都执行栈顶的网络请求。需要在滑动过程中更新网络请求栈，这不好处理。

2. 维护两个网络请求，分别running、preparing，在滑动过程中更新preparing状态的网络请求。也需要在滑动过程中更新网络请求。

3. 维护两个网络请求，分别running、preparing，在滑动过程中更新preparing状态的网络请求。在滑动结束后计算需要进行哪些网络请求。然后更新网络请求。

notice:

* 如果网络请求失败了，需要进行重试。
* 每个页面的分页请求的offset、count都是一致的。比如都是0-9，10-19。。。。等网络请求。为了确保每页进行更少的网络请求，所以每页请求的数量可以大一些。考虑到即使上课条目没有数据，但是条目前面会有一个头像，所以至少有一个占位高度。

### 快速录课数据上传
上课数据包括音频、文字、图片

* 考虑正在上传的数据删除被删除，上传结果返回后通过id删除。

* 为了确保上传数据的顺序，需要维护一个上课内容item的index列表，通过大小排序进行上传后的排序。首先上传条目的顺序是无法控制的，其次上传成功的先后顺序也是无法控制的，所以上传时需要维护一个递增的index，所以即使删除某个条目，其余index也不变。

## 安全策略


## 文件上传
[Retrofit Multipart 多文件上传](http://w4lle.com/2016/11/28/retrofit-multipart/)

[关于HTTP协议，一篇就够了](https://www.cnblogs.com/ranyonsue/p/5984001.html)

## 文件下载

[DownloadManager使用详解](https://blog.csdn.net/qq_19431333/article/details/52798105)

[Android中Uri和Path之间的转换](https://www.jianshu.com/p/33bc363290e9)

[Android Uri to Path](https://www.jianshu.com/p/f9a63fcc0b91)

	文件保存路径
	//设置文件的保存的位置[三种方式]
	//第一种
	//file:///storage/emulated/0/Android/data/your-package/files/Download/update.apk
	req.setDestinationInExternalFilesDir(context, Environment.DIRECTORY_DOWNLOADS, "update.apk");
	//第二种
	//file:///storage/emulated/0/Download/update.apk
	req.setDestinationInExternalPublicDir(Environment.DIRECTORY_DOWNLOADS, "update.apk");
	//第三种 自定义文件路径
	req.setDestinationUri()

