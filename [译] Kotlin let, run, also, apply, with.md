# `Kotlin` `let`, `run`, `also`, `apply`, `with`

![img](https://cdn.journaldev.com/wp-content/uploads/2018/03/Kotlin-let-example.png)

在这篇教程中，我们将会实现一些很重要的 `Kotlin` 标准库函数，`kotlin-stdlib` 为我们提供了有用的高阶函数实现了我们日常惯用的一些模式。我们将来一起学习它们是如何使 `Kotlin` 编程变得更简单和更快速。



我们将要讨论以下几个方法：

- let
- run
- also
- apply
- with



## `Kotlin` `let`

`let` 使用这个对象作为参数传入，并返回 `lambda`表达式的结果；

`Kotlin` `let` 是作用域函数，内部声明的变量不能在外部使用；

以下是一个 `Kotlin` `let`的示例.

```kotlin
fun main(args: Array<String>) {
    var str = "Hello World"
    str.let { println("$it!!") }
    println(str)
}
```

`it` 关键词包含了`let`内属性的副本

如下，`let` 中的最后一个值作为参数返回

```kotlin
var strLength = str.let { "$it function".length }
println("strLength is $strLength") //prints strLength is 25
```

![Kotlin let example](https://cdn.journaldev.com/wp-content/uploads/2018/03/Kotlin-let-example.png)

### 链式 `let` 函数

```
var a = 1
var b= 2

a = a.let { it + 2 }.let { val i = it + b
        i}
println(a) //5
```

我们可以看到，我们在第二个 `let` 函数中间声明了本地变量 `i`，并把 `i` 作为`let` 函数的最后一个声明，这样`let` 将返回 `i` 的值作为外部 `a` 的属性值。

![kotlin let chaining](https://cdn.journaldev.com/wp-content/uploads/2018/03/kotlin-let-chaining.png)



### 嵌套 `let`

我们可以在一个 `let` 表达式的内部设置另一个 `let` 表达式，如下：

```kotlin
var x = "Anupam"
x.let { outer -> outer.let { inner -> print("Inner is $inner and outer is $outer") } }

//Prints
//Inner is Anupam and outer is Anupam
```

对于嵌套的 `let`，我们不能使用 `it` 关键词。我们需要在两个  `let` 函数中为“ it”分配明确的名称。仅最外层的let会返回值，如下所示。

```
var x = "Anupam"
    x = x.let { outer ->
        outer.let { inner ->
            println("Inner is $inner and outer is $outer")
            "Kotlin Tutorials Inner let"
        }
        "Kotlin Tutorials Outer let" 
    }
    println(x) //prints Kotlin Tutorials Outer let
```

![kotlin nested let](https://cdn.journaldev.com/wp-content/uploads/2018/03/kotlin-nested-let.png)

### `let` 判空

另外，`let` 是在判空方面很有用，如下：

```
var name : String? = "Kotlin let null check"
name?.let { println(it) } //prints Kotlin let null check
name = null
name?.let { println(it) } //nothing happens
```

The code inside the let expression is executed only when the property is not null. Thus let saves us from the [if else](https://www.journaldev.com/18483/kotlin-control-flow-if-else-for-while-range) null checker too!

`let` 表达式中的代码只有在属性值不为空时才会被执行。因此，`let` 从 [`if` `else` `null`](https://www.journaldev.com/18483/kotlin-control-flow-if-else-for-while-range) 检查中解救了我们！

## `Kotlin` `run`

`Kotlin` `run` 是另一个有趣的函数，以下是它的使用用例。

```kotlin
var tutorial = "This is Kotlin Tutorial"
    println(tutorial) //This is Kotlin Tutorial
    tutorial = run {
        val tutorial = "This is run function"
        tutorial
    }
    println(tutorial) //This is run function
```

![Kotlin run function](https://cdn.journaldev.com/wp-content/uploads/2018/03/kotlin-run-function.png)

Kotlin run expression can change the outer property. Hence in the above code, we’ve redefined it for the local scope.

`Kotlin` `run` 表达式可以改变外面的属性，因此在上面的代码中，我们已针对本地范围重新定义了它。

- 和 `let` 函数一样，`run` 函数也返回最后的声明；
- 和 `let` 函数不同的是，`run` 函数不支持 `it` 关键词；

### `let` and `run`

让我们把 `let` 和 `run` 函数结合一起来看

```kotlin
var p : String? = null
    p?.let { println("p is $p") } ?: run { println("p was null. Setting default value to: ")
        p = "Kotlin"}

    println(p)
//Prints
//p was null. Setting default value to: 
//Kotlin
```

## `Kotlin` `also`

顾名思义，`also` 表达式会对调用它的对象进行一些附加处理。

不像 `let`，它返回原始的对象而不是任何新的返回数据。

因此，返回对象一直是一样的类型。

和 `let` 一样， `also` 也使用 `it` 关键词。

```kotlin
var m = 1
m = m.also { it + 1 }.also { it + 1 }
println(m) //prints 1 
```

![Kotlin also function](https://cdn.journaldev.com/wp-content/uploads/2018/03/Kotlin-also-function.png)

### `Kotlin` `let` 对比 `also`

跟随着代码片段，它是一个很好的例子，用来区分 `let` 和 `also` 之间的不同

```kotlin
data class Person(var name: String, var tutorial : String)
var person = Person("Anupam", "Kotlin")

var l = person.let { it.tutorial = "Android" }
var al = person.also { it.tutorial = "Android" }
    
println(l)
println(al)
println(person)
```

![Kotlin let vs also](https://cdn.journaldev.com/wp-content/uploads/2018/03/Kotlin-let-also.png)

在上面的代码中，我们使用了 [Data classes](https://www.journaldev.com/18594/kotlin-data-class).



`also` 表达式返回了数据类对象，同时 `let` 表达式则不返回任何值，因为我们没有明确指定任何内容。

## `Kotlin` `apply`

`Kotlin` `apply` 是类型的扩展功能，它在表达式中的对象引用（也称为接收器）上运行，并在完成时返回对象引用。

```kotlin
data class Person(var name: String, var tutorial : String)
var person = Person("Anupam", "Kotlin")

person.apply { this.tutorial = "Swift" }
println(person)
```

![Kotlin apply](https://cdn.journaldev.com/wp-content/uploads/2018/03/kotlin-apply-function.png)

### `apply` 对比 `also`

```kotlin
data class Person(var n: String, var t : String)
var person = Person("Anupam", "Kotlin")

person.apply { t = "Swift" }
println(person)

person.also { it.t = "Kotlin" }
println(person)
```

![Kotlin apply vs also](https://cdn.journaldev.com/wp-content/uploads/2018/03/kotlin-apply-vs-also.png)

注意：在 `apply` 中 `it` 是不被允许使用的。如果在函数中数据类的属性名称是唯一的，你可以省略 `this` 。

只有在我们不想遮蔽 `this` 时，我们才应该使用 `also`。

## `Kotlin` `with`

与`apply`类似，`with`用于更改实例属性，而无需每次都在引用上调用 **. ** 运算符。

```kotlin
data class Person(var name: String, var tutorial : String)
var person = Person("Anupam", "Kotlin")

with(person)
    {
        name = "No Name"
        tutorial = "Kotlin tutorials"
    }
```

![Kotlin with function](https://cdn.journaldev.com/wp-content/uploads/2018/03/Kotlin-with-function.png)
同样，`with` 与 `apply` 类似，除了一些区别

### `Kotlin` `apply` 对比 `with`

- `with`  在没有对象（接收者）的情况下运行，而 `apply` 需要一个对象；
- `apply` 在对象引用上运行，而 `with` 只是将其作为参数传递；
- `with` 函数的最后一个表达式返回结果；



```kotlin
var xyz = with(person)
    {
        name = "No Name"
        tutorial = "Kotlin tutorials"
        val xyz = "End of tutorial"
        xyz
    }
    println(xyz) //End of tutorial
```

这就是 `Kotlin` 标准函数更改变量或修改函数中对象的全部功能。