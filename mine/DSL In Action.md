# [DSL In Action](https://www.kotliner.cn/2018/04/dsl-in-action/)

>通过kotlin programme 就可以编写UI。它好在用DSL的方式来描述View，而缺点在于无法即时预览，在这方面导致Anko DSL的开发效率不及XML传统方式。

> 伴随着Kotlin的发展，有一个神奇的框架anko-layout，一直存在于我们的视野却又一直因为各种原因无法用于生产环境中。

>Kotlin DSL本身就是语法糖而已，所以DSL背后就是使用Kotlin代码来自己初始化View，初始化LayoutParams，进行addView之类…

>而其实LayoutInflater它本身也只是在做相似的事情而已，LayoutInflater是根据XML文件里面的配置来通过反射初始化View，根据其他字段来填充View属性以及LayoutParams什么的。所以没有什么神秘的东西…

>做个总结？
DSL最吸引人的地方就在于，它可以在布局上加入逻辑，对于布局过程，它有着编程语言级别的控制，比如说封装成类，封装成函数什么的。这些东西在XML里面都是无法做到的，因为aapt工具的局限性，XML只能按照固定的格式写布局 + 代码控制来提供动态性，反正就很蛋疼。

>而DSL可以解决很多问题，比如说用一个for循环来取代Adapter填充View功能，避免了很多无用的操作。比如说在布局里面加一个if就可以来操作一个控件的布局与否，而不是在findView之后控制Visibility，可以用Kotlin的闭包来封装一个View的初始化操作什么的，重复的操作就可以封装起来，再比如XML只能设置paddingLeft/paddingRight，在Anko DSL / 自定义DSL里面就可以很轻易的封装出一个horizontalPadding。当然Anko因为避免了反射，提高了大量的性能。

>DSL和XML并不是冲突的，DSL用于解决布局中细碎和动态的部分，而XML用于单页布局，复杂布局。同时DSL和XML也可以无缝嵌合在一起，所以两者并不是冲突的关系，也没有必要去选择“我到底该用DSL写还是XML写”，两者各有优点，了解Anko DSL并且与XML活用起来才是最优解。XML可以拿到ViewGroup的应用然后用DSL做骚操作，DSL也可以动态添加Inflate出来的XML来实现复杂页面布局的添加

>DSL和XML各有所长，DSL更适合用于页面模块的解耦，XML更多用于单页构建 / 复杂布局，两者相互结合相互服务。

## 参考文章

[构建Recyclerview DSL](https://www.kotliner.cn/2018/06/recyclerviewdsl/)