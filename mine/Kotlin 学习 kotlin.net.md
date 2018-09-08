# Kotlin学习 kotlin.net
[链接](https://www.youtube.com/watch?v=H_oGi8uuDpA)
## [类和继承](https://www.kotlincn.net/docs/reference/classes.html)
* 类声明由类名、类头（指定其类型参数、主构造函数等）以及由花括号包围的类体构成。类头与类体都是可选的； 如果一个类没有类体，可以省略花括号。

* 在 Kotlin 中的一个类可以有一个主构造函数和一个或多个次构造函数。主构造函数是类头的一部分：它跟在类名（和可选的类型参数）后。

* 如果主构造函数没有任何注解或者可见性修饰符，可以省略这个 constructor 关键字

* 主构造函数不能包含任何的代码。初始化的代码可以放到以 init 关键字作为前缀的初始化块（initializer blocks）中。

* 如果构造函数有注解或可见性修饰符，这个 constructor 关键字是必需的，并且这些修饰符在它前面：

	class Customer public @Inject constructor(name: String) { …… }

* Kotlin 并没有 new 关键字

* 类上的 open 标注与 Java 中 final 相反，它允许其他类从这个类继承。默认情况下，在 Kotlin 中所有的类都是 final， 对应于《Effective Java》第三版书中的第 19 条：要么为继承而设计，并提供文档说明，要么就禁止继承。

* 我们之前提到过，Kotlin 力求清晰显式。与 Java 不同，Kotlin 需要显式标注可覆盖的成员（我们称之为开放）和覆盖后的成员：open class Base {
    open fun v() {}
    fun nv() {}
}
class Derived() : Base() {
    override fun v() {}
}

* 覆盖属性
属性覆盖与方法覆盖类似；在超类中声明然后在派生类中重新声明的属性必须以 override 开头，并且它们必须具有兼容的类型。每个声明的属性可以由具有初始化器的属性或者具有 getter 方法的属性覆盖。 

## [属性和字段](https://www.kotlincn.net/docs/reference/properties.html)
* Kotlin的类可以有属性。 属性可以用关键字var 声明为可变的，否则使用只读关键字val。
* 声明一个属性的完整语法是

var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
    
* 幕后字段
在 Kotlin 类中不能直接声明字段。然而，当一个属性需要一个幕后字段时，Kotlin 会自动提供。这个幕后字段可以使用field标识符在访问器中引用
    
## [接口](https://www.kotlincn.net/docs/reference/interfaces.html#%E6%8E%A5%E5%8F%A3)
* Kotlin 的接口与 Java 8 类似，既包含抽象方法的声明，也包含实现。与抽象类不同的是，接口无法保存状态。它可以有属性但必须声明为抽象或提供访问器实现
* 你可以在接口中定义属性。在接口中声明的属性要么是抽象的，要么提供访问器的实现。在接口中声明的属性不能有幕后字段（backing field），因此接口中声明的访问器不能引用它们。

## [可见性修饰符](https://www.kotlincn.net/docs/reference/visibility-modifiers.html)
* 类、对象、接口、构造函数、方法、属性和它们的 setter 都可以有 可见性修饰符。 （getter 总是与属性有着相同的可见性。） 在 Kotlin 中有这四个可见性修饰符：private、 protected、 internal 和 public。 如果没有显式指定修饰符的话，默认可见性是 public。

* Kotlin 中外部类不能访问内部类的 private 成员。

* also 功能：调用某对象的also函数，则该对象为函数的参数。在函数块内可以通过 it 指代该对象。返回值为该对象自己。

	示例：val a = "string".also {
    println(it)
}
println(a)

## [扩展](https://www.kotlincn.net/docs/reference/extensions.html)
* Kotlin 同 C# 和 Gosu 类似，能够扩展一个类的新功能而无需继承该类或使用像装饰者这样的任何类型的设计模式。 这通过叫做 扩展 的特殊声明完成。Kotlin 支持 扩展函数 和 扩展属性。

* 扩展是静态解析的
扩展不能真正的修改他们所扩展的类。通过定义一个扩展，你并没有在一个类中插入新成员， 仅仅是可以通过该类型的变量用点表达式去调用这个新函数。我们想强调的是扩展函数是静态分发的，即他们不是根据接收者类型的虚方法。 这意味着调用的扩展函数是由函数调用所在的表达式的类型来决定的， 而不是由表达式运行时求值结果决定的。

* 扩展属性 和函数类似，Kotlin 支持扩展属性：注意：由于扩展没有实际的将成员插入类中，因此对扩展属性来说幕后字段是无效的。这就是为什么扩展属性不能有初始化器。他们的行为只能由显式提供的 getters/setters 定义。

* 伴生对象的扩展
如果一个类定义有一个伴生对象 ，你也可以为伴生对象定义扩展函数和属性：

* 扩展的作用域
大多数时候我们在顶层定义扩展，即直接在包里：
package foo.bar
​
​fun Baz.goo() { …… }
要使用所定义包之外的一个扩展，我们需要在调用方导入它：

## [数据类](https://www.kotlincn.net/docs/reference/data-classes.html)
* Ko