# The Navigation Architecture Component

[原文链接](https://developer.android.google.cn/topic/libraries/architecture/navigation/)

The Navigation Architecture Component simplifies the implementation of navigation in an Android app.

Navigation 架构组件简化了Android
app的navigation的实现。

This section begins by providing the guiding principles of navigation as implemented by the Navigation Architecture Component. The principles are followed by Implementing navigation with the Navigation Architecture Component providing the fundamental tasks to implement the Navigation Architecture Component in your app. Finally, you can choose from the following topics, depending on your needs for your app:

这部分通过提供Navigation架构组件实现navigation的指导原则作为开始。由Navigation架构组件实现navigation所遵循的原则提供了基本任务来实现app中的Navigation架构组件。最后，你可以根据你的app的需要选择以下的topics：

* Migrate to the Navigation Architecture Component.
* 迁移到Navigation架构组件
* Add support for new destination types.
* 添加对新目标类型的支持。
* Implement conditional navigation.
* 实现条件导航
* Identify a common destination for several UI elements.
* 为一些UI元素定义一个通用的destination

## Principles of Navigation

The goal of any in-app navigation should be to provide a consistent and predictable experience to users. To meet this goal, the Navigation Architecture Component helps you build an app that adheres to each of the navigation principles below.

任何app内的navigation的目的应该是提供一个一致的并且可预测的体验给用户。

### The app should have a fixed starting destination
app应该有一个固定的开始目的地。

Apps have a fixed destination which is the screen the user sees when they launch your app from the launcher. This destination should also be the last screen the user sees when they return to the launcher after pressing the back button.

Apps有一个固定的目的地，这个目的地是当用户打开app在屏幕上看到的。这个目的地也应该用户通过按下返回按钮后返回到launcher看到的。

Note: An app might have a one-time setup or series of login screens. These conditional screens should not be considered the starting destination of your app.

注意：app可能有一个一次性的安装展示屏或者一系列登陆屏。这些条件性的屏幕不应该被考虑作为app的starting destination.

### A stack is used to represent the "navigation state" of an app

栈用来代表app的“navigation state”

The navigation state of your app should be represented with a last in, first out structure. This "navigation stack" should have the start destination of the app at the bottom of the stack and the current destination as the top of the stack.

app的navigation state应该代表一个“last in first out”的结构。这个“navigation stack”应该有start destination在栈的底部，并且当前的destinationg作为栈的顶部。

Operations that change the navigation stack should always operate on the top of the navigation stack, either by "pushing" a new destination onto the top of the stack or "popping" the top-most destination off the stack.

改变navigation栈的操作应该总是在操作navigation stack的顶部。要么是通过“pushing”一个新的destination到栈的顶部，要么是“poping”栈的最顶层的destionation。

### The Up button never exits your app

“向上”按钮永远不会退出您的应用

If a user is at the start destination, the Up button should not be shown. When your app is launched using a deep link on another app's task, Up should take users to the hierarchical parent destination and not back to the other app.

如果用户在start destionation,Up 按钮不应该展示。当你的app是通过使用另一个app的task上的deep link 展示出来的。Up 应该带用户到the hierarchical parent destionation ,不带回到其他的app

### Up and Back are equivalent within your app's task

Up和Back在您的应用程序任务中是等效的

When the system Back button would not exit your app, such as when you are on your own task and not on the start destination, the Up button should function identically to the system Back button.

当系统返回按钮在你的app中不存在，例如当你在你自己的task并且不再start destionation,Up按钮应该和系统Back按钮其同样的功能。

### Deep linking to a destination or navigating to the same destination should yield the same stack

Deep linking到destionation或者导航到同样的destionation应该产生相同的栈

A user can enter an app at the start destination and navigate to a destination. A user can also use a deep link, if available, to navigate to the same destination. In both of these cases, the navigation stack should have the same stack of destinations. Specifically, the user should be able to use the Back or Up button, regardless of how they got to a destination, to navigate through destinations back to the start destination. Any existing navigation stack is removed and replaced with the deep link’s navigation stack.

一个用户可以在start destionation和navigate to a destiongation 进入一个app。如果可以的话，用户也可以使用deep link 来导航到相同的目的地。在这两种情况下，navigation stack应该有同样的destionations的栈。特别情况下，无论它们如何到达目的地，用户应该可以使用back和up按钮通过destionations导航回到start destionation。任何存在的navigation stack被用deep link的navigation stack移除和替换.

### Next Steps

This document explained some of the principles behind the design of the Navigation Architecture Component. Continue to Implementing navigation with the Navigation Architecture Component to learn how to integrate the Navigation Architecture Component in your app.

这个文档解释了设计Navigation Architecture Component的原则。继续[使用Navigation Architecture Component实现navigation](https://developer.android.google.cn/topic/libraries/architecture/navigation/navigation-implementing)来学习如何在app中集成Navigation

For further reading on the app architecture, refer to:

Guide to app architecture
How should I design my Android Application?

## Implement navigation with the Navigation Architecture Component

使用Navigation Architecture Component 实现navigation

The Navigation Architecture Component simplifies the implementation of navigation between destinations in your app. A set of destinations compose an app’s navigation graph.

The Navigation Architecture Component 简化了app中destinations之间的导航的实现。destinations的集合组成了app的navigation图形。

A destination is any place you can navigate to in your app. While destinations are usually Fragments representing specific screens, the Navigation Architecture Component supports other destination types:

destionation是你在app中你可以导航到的任何地方。虽然destinations通常是代表特定屏幕的Fragments，但是Navigation Architecture Component支持其他的destionation类型：

* Activities
* Navigation graphs and subgraphs - when the destination is a graph or subgraph, you navigate to the starting destination of that graph or subgraph
* Navigation 图形或者子图。当destination是一个图形或者子图，你可以导航到图形或者子图的starting destination
* [Custom destination types](https://developer.android.google.cn/topic/libraries/architecture/navigation/navigation-add-new)

In addition to destinations, a navigation graph has connections between destinations called actions. Figure 1 shows a visual representation of a navigation graph for a sample app containing 6 destinations connected by 5 actions.

除了destionations,一个navigation 图形有destionations之间的连接，叫做actions。Figure1展示了一个navigation的可视化呈现，这个navigation包含了由5个actions连接的6个destionations。

Figure 1. A navigation graph
The Navigation Architecture Component is implemented based on the Principles of navigation.

Note: If you want to use Navigation Architecture Components with Android Studio, you must use Android Studio 3.2 Canary 14 or higher.

## Set up navigation in a project

Before you can create a navigation graph, you must set up the Navigation Architecture Component for your project. To set up your project in Android Studio, perform the following steps.

在创建一个navigation graph之前，你必须为你的工程建立Navigation Architecture Component.为了在AS中建立你的工程，实行以下步骤：

 1. If using a Beta, Release Candidate, or Stable build, you must enable the Navigation Editor. Click File > Settings (Android Studio > Preferences on Mac), select the Experimental category in the left pane, check Enable Navigation Editor, and then restart Android Studio.
 2. Add the following Navigation Architecture Component to your app or module's build.gradle file. For more information on adding Archtecture Components to build.gradle, refer to Adding components to your project.
 3. In the Project window, right-click on the res directory and select New > Android Resource File. The New Resource File dialog appears.
 4. Type a name in the File name field, such as "nav_graph".
 5. Select Navigation from the Resource type drop-down list.
 6. Click OK. The following occurs:
 
 		a. A navigation resource directory is created within the res directory.
	
		b. A nav_graph.xml file is created within the navigation directory.
	
		c. The nav_graph.xml file opens in the Navigation Editor. This xml file contains your navigation graph.
		
 7. Click the Text tab to toggle to the XML text view. The XML for an empty navigation graph looks like this:

		<?xml version="1.0" encoding="utf-8"?>
		<navigation xmlns:android="http://schemas.android.com/apk/res/android">
		</navigation>

 8. Click Design to return to the Navigation Editor.

## Tour the Navigation Editor

## Identify destinations

The first step in creating a navigation graph is to identify the destinations for your app. You can create blank destinations or create destinations from fragments and Activities in an existing project.

创建一个navigation graph第一步是定义你的app的destinations。你可以创建空白destionsations或者从工程中已存在的fragments和Activities创建destionations。

Note: The Navigation Architecture Component is designed for apps that have one main activity with multiple fragment destinations. The main activity “hosts” the navigation graph. In an app with multiple activity destinations, each additional activity hosts its own navigation graph. Modifying an activity to host navigation is discussed later in this document.

注意：The Navigation Archteture Component是为有一个有main activity with multiple fragment destinations的app而设计的。主activity “hosts” the navigation graph.在一个有多个activity destinations 的app中，每一个多余的activity持有它自己的navigation graph.修改一个activity来持有navigation在这个文档的后面会讨论。

## Connect destinations

## Designate (指定) a screen as the start destination

## Modify an activity to host navigation

An activity hosts navigation for an app through the implementation of a NavHost interface which is added to your activity’s layout. The NavHost is an empty view whereupon destinations are swapped in and out as a user navigates through your app.

activity通过实现添加到activity布局的NavHost接口持有navigation。NavHost是一个空视图，当用户浏览您的应用程序时，destionation会被换入和换出。

The Navigation Architecture Component’s default NavHost implementation is NavHostFragment.

The Navigation Architecture Component默认的NavHost实现是NavHostFragment.

After you have included a NavHost, you must associate your navigation graph with the NavHostFragment using the navGraph attribute. The following snippet shows how to include a NavHostFragment and associate a navigation graph with that NavHostFragment in an activity's layout file:

在你已经包括了NavHost之后，你必须把你的navigation graph和使用navGraph 属性的NavHostFragment关联起来。下面的片段展示了怎样包含一个NavHostFragment，并且把navigation graph和activity的布局文件中的NavHostFragment关联起来。

	<?xml version="1.0" encoding="utf-8"?>
	<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <fragment
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/my_nav_host_fragment"
        android:name="androidx.navigation.fragment.NavHostFragment"
        app:navGraph="@navigation/nav_graph"
        app:defaultNavHost="true"
        />

	</android.support.constraint.ConstraintLayout>
	
The previous sample contains an app:defaultNavHost="true" attribute. This attribute ensures your NavHostFragment intercepts the system Back button. You will also overwrite AppCompatActivity.onSupportNavigateUp() and call NavController.navigateUp as follows:

前面的例子包含了一个属性app:defaultNavHost="true"。这个属性确保你的NavHostFragment 拦截了系统返回按钮。你也可以重写AppCompatActivity.onSupport
avigateUp()并且像如下调用调用NavController.navigateUp

	override fun onSupportNavigateUp()
        = findNavController(R.id.nav_host_fragment).navigateUp()

### Create the NavHostFragment programmatically

You can also use NavHostFragment.create() to programmatically create a NavHostFragment with a specific graph resource, as shown in the example below:

你也可以使用NavHostFragment.create()来通过编程使用一个特定的graph 资源创建NavHostFragment，如下例子所示：

	val finalHost = NavHostFragment.create(R.navigation.example_graph)
	supportFragmentManager.beginTransaction()
    .replace(R.id.nav_host, finalHost)
    .setPrimaryNavigationFragment(finalHost) // this is the equivalent to app:defaultNavHost="true"
    .commit()
    
## Modify an activity to host navigation

An activity hosts navigation for an app through the implementation of a NavHost interface which is added to your activity’s layout. The NavHost is an empty view whereupon destinations are swapped in and out as a user navigates through your app.

The Navigation Architecture Component’s default NavHost implementation is NavHostFragment.

After you have included a NavHost, you must associate your navigation graph with the NavHostFragment using the navGraph attribute. The following snippet shows how to include a NavHostFragment and associate a navigation graph with that NavHostFragment in an activity's layout file: