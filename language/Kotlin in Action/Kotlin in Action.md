https://play.kotlinlang.org/



# P1 Introducing Kotlin

## 1 Kotlin: What and why

### 1.1 A taste of Kotlin

```kotlin
data class Person(					//1
	val name: String,				//2
    val age: Int? = null			//3
)

fun main() {						//4
    val persons = listOf(			
    	Person("Alice", age = 29),	//5
        Person("Bob"),				//6
    )
    
    val oldest = persons.maxBy {	//7
        it.age ?: 0					//8
    }
    
    println("The oldest is :$oldest")//9
}
// The oldest is: Person(name=Alice, age=29) //10
```

1. **Data class**
2. **Read-only property**
3. **Nullable type (Int ?), the default value for the argument**
4. **Top-level function**
5. **Named argument**
6. **Trailing comma**
7. **Lambda expression**: The lambda expression passed to the function takes one parameter, implicitly named  it  by default (although you can assign other names to the parameter, as well).
8. **Null-coalescing Elvis operator**: providing fallback values when a variable is null.  The Elvis operator ( ?: ) returns zero if  age  is  null . Because Bob’s age isn’t specified, the Elvis operator replaces it with zero
9. **String template**
10. **Autogenerated toString**



### 1.2 Kotlin's primary traits

multiparadigm(多编程范式) 

静态类型，很多错误可以在编译期间捕获，而不是在运行时

面向对象、函数式



1.2.1 Kotlin use cases: Android, sever side, anywhere Java runs, and beyond



1.2.2 Static typing makes Kotlin performant, reliable, and maintainable

- Performance—Calling methods is faster because there’s no need to determine which method needs to be called at run time.
- Reliability—The compiler uses types to verify the consistency of the program, so there are fewer chances for crashes at run time. 
- Maintainability—Working with unfamiliar code is easier because you can see what kind of types the code is working with. 
- Tool support—Static typing enables reliable refactorings, precise code completion, and other IDE features.

dynamically typed programming languages, like Python or JavaScript. Those languages let you define variables and functions that can store or return data of any type and resolve the method and field references at run time. This allows for shorter code and greater flexibility in creating data structures, but the downside is that problems like misspelled names or invalid parameters passed to functions can’t be detected during compilation and can lead to run-time errors.

在编译期间每句表达式的类型可被知道的同时，Kotlin 不需要显式指定每个变量的类型，而是根据上下文自动推断出来。



1.2.3 Combining functional and object-oriented programming makes Kotlin safe and flexible

The key concepts of functional programming are as follows:

- First-class functions—You work with functions (pieces of behavior) as values. You can store them in variables, pass them as parameters, or return them from other functions. 
- Immutability—You work with immutable objects, which guarantees their state can’t change after their creation. 
- No side effects—You write pure functions, functions that return the same result given the same inputs and don’t modify the state of other objects or interact with the outside world.

优点一：与命令式代码相比，函数式代码更加优雅和简洁：使用函数作为值可以赋予你更强大的抽象能力，而无需改变变量并依赖循环和条件分支。避免代码重复，可以将逻辑的公共部分提取到一个函数中，并将不同部分（函数）作为参数传递。

优点二：并发安全。使用不可变数据结构和纯函数，可以确保不会发生类的不安全修改，无需复杂的同步方案。

函数式编程意味着更简单的测试。

Kotlin has a rich set of features to support functional programming from the get-go：

- Function types—Allowing functions to receive other functions as arguments or return other functions 
- Lambda expressions—Letting you pass around blocks of code with minimum boilerplate 
- Member references—Allowing you to use functions as values and, for instance, pass them as arguments 
- Data classes—Providing a concise syntax for creating classes that can hold immutable data 
- Standard library APIs—A rich set in the standard library for working with objects and collections in the functional style

以下代码演示了使用输入序列执行的一系列操作。给定一组消息，代码将找到“按名称排序的所有非空未读消息的唯一发送者”

```kotlin
messages
    .filter { it.body.isNotBlank() && !it.isRead }
    .map(Message::sender)
    .distinct()
    .sortedBy(Sender::name)
```



1.2.4 Concurrent and asynchronous code becomes natural and structured with coroutines

并发方法：threads, callbacks, futures, promises, reactive extensions, and more.

Kotlin 使用称为协程（coroutines）的可暂停计算（suspendable computation）来解决并发和异步编程问题。

例子，定义一个函数 processUser，通过调用 authenticate、loadUserData 和 loadImage 进行三次网络调用：

```kotlin
suspend func processUser(credentials: Credentials) {
    val user = authenticate(credentials)
    //1 long-running operations
    val data = loadUserData(user)
    //2 can be specified sequentially, top down
    val profilePicture = loadImage(data.imageID)
    //3 without blocking the application
    // ...
}

suspend fun authenticate(c: Credentials): User { /*...*/  } //4
suspend fun loadUserData(u: User): Data { /*...*/  }
suspend fun loadImage(id: Int): Image { /*...*/  }
```

网络调用可能会任意长时间。在执行每个网络请求时，processUser 函数的执行会暂停，等待结果。但是，运行此代码的线程不会被阻塞；在等待 processUser 的结果时，它可以同时执行其他任务，例如响应用户输入。

命令式编码，不能做到一个调用接一个调用，而不阻塞底层线程。另外，如果使用 callbacks 或 reactive streams，这种简单连续逻辑会变复杂。

示例，使用 async 并发加载两个图像，然后通过 await 等待加载完成，并返回组合图像

```kotlin
suspend fun loadAndOverlay(first: String, second: String):
	image = 
		coroutineScope {
            val fistDeferred = async { loadImage(first) }
            //1
            val secondDeferred = async { loadImage(second) }
            //2
            combineImages(firstDeferred.await(), secondDeferred.await())//3
        }
```

结构化并发帮助你管理协程的生命周期。例子中，两个加载过程以结构化的方式（从同一个协程作用域）启动。这保证了如果一个加载失败，第二个加载会自动取消。

协程是一种非常轻量级的抽象，意味着可以启动数百万个并发任务而不会显著降低性能。



1.2.5 Kotlin can be used for any purpose: It's free, open source, and open to contributes



### 1.3 Areas in which Kotlin is often used

1.3.1 Powering backends: Server-side development with kotlin

1.3.2 Mobile Development: Android is Kotlin first

1.3.3 Multiplatform: Sharing business logic and minimizing duplicate work on iOS, JVM, JS, and beyond



### 1.4 The philosophy of Kotlin

1.4.1 Kotlin is a pragmatic language

1.4.2 Kotlin is concise

1.4.3 Kotlin is safe

1.4.4 Kotlin is interoperable



### 1.5 Using the Kotlin tools

1.5.1 Setting up and running the Kotlin code

1.5.2 Compiling Kotlin code



### Summary



## 2 Kotlin basics

### 2.1 Basic elements: Functions and variables

2.1.1 Hello, world



2.1.2 Declaring functions with parameters and return values

Kotlin 中，if 是一个表达式 expression 而不是一个 statement。而且大多数控制结构，除了循环（for, while, 和 do/while），都是表达式。表达式有值，可以作为另一个表达式的一部分，而语句在其包含的块中是顶级元素，没有自己的值。



2.1.3 Making function definitions more concise by using expression bodies

= 加表达式体，可以忽略括号，以及返回类型（编译期分析推断得到）。如果编写其他开发者依赖的库，则避免在公共 API 的函数中使用推断的返回类型。



2.1.4 Declaring variables to store data

如果没有立即初始化变量，则需要显式声明类型。



2.1.5 Marking a variable as read only or reassignable

val 相当于 java 的 final

一个 val 引用是只读的且赋值后不能改变的，但是它指向的对象可以被改变。 



2.1.6 Easier string formatting: String templates

$ 引用局部变量



### 2.2 Encapsulating behavior and data: Classes and properties

2.2.1 Associating data with a class and making it accessible: Properties



2.2.2 Computing properties instead of storing their values: Custom accessors



2.2.3 Kotlin source code layout: Directories and packages





### 2.3 Representing and handling choices: Enums and when

2.3.1 Declaring enum classes and enum constants

2.3.2 Using the when expression to deal with enum classes

2.3.3 Capturing the subject of a when expression in a variable

2.3.4 Using the when expression with arbitrary objects

2.3.5 Using the when expression without an argument

2.3.6 Smart casts: Combining type checks and casts

2.3.7 Refactoring: Replacing an if with a when expression

2.3.8 Blocks as branches of if and when



### 2.4 Iterating over things: while and for loops

2.4.1 Repeating code while a condition is true: The while loop

2.4.2 Iterating over numbers: Ranges and progressions

2.4.3 Iterating over maps

2.4.4 Using in to check collection and range membership



### 2.5 Throwing and catching exceptions in Kotlin

2.5.1 Handling exceptions and recovering from errors: try, catch, and finally

2.5.2 Using try as an expression



### Summary



## 3 Defining and calling functions

## 4 Classes, objects, and lambdas

## 5 Programming with lambdas

## 6 Working with collections and sequences

## 7 Working with nullable values

## 8 Basic types, collections, and arrays





# P2 Embracing Kotlin

## 9 Operator overloading and other conventions



## 10 Higher-order functions: Lambdas as parameters and return values



## 11 Generics



## 12 Annotations and reflection



## 13 DSL construction


