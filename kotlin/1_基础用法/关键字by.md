# 关键字by

## 简介
kotlin 中的委托模式依靠by关键字，语法定义样式为：**val/var name : Type by Expression**；其中**var/val**表示属性类型(可变/只读)，**name**表示属性名称，**Type**表示属性的数据类型，**Expression**表示代理类

## 使用场景
* 延迟加载属性(lazy property): 属性值只在初次访问时才会计算,
* 可观察属性(observable property): 属性发生变化时, 可以向监听器发送通知,
* 将多个属性保存在一个 map 内, 而不是保存在多个独立的域内.

## 具体用法
* 延迟加载(Lazy)

  lazy()是一个函数, 接受一个Lambda表达式作为参数, 返回一个Lazy类型的实例,这个实例可以作为一个委托, 实现延迟加载属性(lazy property): 第一次调用 get() 时, 将会执行 lazy() 函数受到的Lambda 表达式,然后会记住这次执行的结果, 以后所有对 get() 的调用都只会简单地返回以前记住的结果.

  ```
  val no: Int by lazy {

      200

  }

  val c = 200

  fun main(args: Array) {

      val b = 200

      println(no) // Log : 200

      println(no) // Log : 200

  }
  ```

  注意：

  1.var类型属性不能设置为延迟加载属性，因为在lazy中并没有setValue(…)方法  
  2.lazy操作符是线程安全的。如果在不考虑多线程问题或者想提高更多的性能，也可以使用 **lazy(LazyThreadSafeMode.NONE){ … }**。在LazyThreadSafetyMode中声明了几种，[Lazy]实例在多个线程之间同步访问的形式：   
  SYNCHRONIZED：锁定，用于确保只有一个线程可以初始化[Lazy]实例     
  PUBLICATION：初始化函数可以在并发访问未初始化的[Lazy]实例值时调用几次，，但只有第一个返回的值将被用作[Lazy]实例的值  
  NONE：没有锁用于同步对[Lazy]实例值的访问; 如果从多个线程访问实例，是线程不安全的。此模式应仅在高性能至关重要，并且[Lazy]实例被保证永远不会从多个线程初始化时使用。

* 可观察属性(Observable)

  Delegates.observable() 函数接受两个参数: 第一个是初始化值, 第二个是属性值变化事件的响应器(handler).这种形式的委托，采用了观察者模式，其会检测可观察属性的变化，当被观察属性的setter()方法被调用的时候，响应器(handler)都会被调用(在属性赋值处理完成之后)并自动执行执行的lambda表达式，同时响应器会收到三个参数：被赋值的属性, 赋值前的旧属性值, 以及赋值后的新属性值。
  ```
  var name: String by Delegates.observable("wang", {

      kProperty, oldName, newName ->

      println("kProperty：${kProperty.name} | oldName:$oldName | newName:$newName")

  })

  fun main(args: Array) {

      println("name: $name") // Log：nam：wang

      name = "zhang" // Log：kProperty：name | oldName:wang | newName:zhang

      name = "li" // Log：kProperty：name | oldName:zhang | newName:li

  }

  ```

  在这个例子中，Delegates.observable(wang, hanler),完成了两项工作，一是，将name初始化(name=wang)；二是检测name属性值的变化，每次变化时，都会打印其赋值前的旧属性值, 以及赋值后的新属性值。

* Vetoable

  Delegates.vetoable()函数接受两个参数: 第一个是初始化值, 第二个是属性值变化事件的响应器(handler),是可观察属性(Observable)的一个特例，不同的是在响应器指定的自动执行执行的lambda表达式中在保存新值之前做一些条件判断，来决定是否将新值保存。
  ```
  var name: String by Delegates.vetoable("wang", {

      kProperty, oldValue, newValue ->

      println("oldValue：$oldValue | newValue：$newValue")

      newValue.contains("wang")

  })

  fun main(args: Array) {

      println("name: $name")

      println("------------------")

      name = "zhangLing"

      println("name: $name")

      println("------------------")

      name = "wangBing"

      println("name: $name")

  }

  //Log

  name: wang

  ------------------

  oldValue：wang | newValue：zhangLing

  name: wang

  ------------------

  oldValue：wang | newValue：wangBing

  name: wangBing
  ```

  代码示例中的委托，在给name赋值是，只有字符串中含有”wang”时，将新值赋值给name.第一次给name赋值“zhangLing”时，lambda表达式的返回值为false,此时并没有对name成功赋值。而第二次，赋值”wangBing” 时，lambda表达式的返回值为true，成功赋值。

* Not Null

  在实际开发时，我们可能会设置可为null的var类型属性，在我们使用它时，肯定是对其赋值，假如不赋值，必然要报NullPointException.一种解决方案是，我们可以在使用它时，在每个地方不管是不是null，都做null检查，这样我们就保证了在使用它时，保证它不是null。这样无形当中添加了很多重复的代码。

  在Kotlin中，委托又帮我们做了一个善事，不用去写这些重复的代码，Not Null委托会含有一个可null的变量并会在我们设置这个属性的时候分配一个真实的值。如果这个值在被获取之前没有被分配，它就会抛出一个异常。
  ```
  class App : Application() {

      companion object {

          var instance: App by Delegates.notNull()

      }

      override fun onCreate() {

          super.onCreate()

          instance = this

      }

  }
  ```

* 将多个属性保存在一个map内

  使用Gson解析Json时，可以获取到相应的实体类的实例，当然该实体类的属性名称与Json中的key是一一对应的。在Kotlin中，存在这么一种委托方式，类的构造器接受一个map实例作为参数，将map实例本身作为属性的委托，属性的名称与map中的key是一致的，也就是意味着我们可以很简单的从一个动态地map中创建一个对象实例。
  ```
  Class1.

  class User(val map: Map) {

      val name: String by map

      val age: Int by map

  }

  Class2.

  fun main(args: Array) {

      val user = User(mapOf(

              "name" to "John Doe",

              "age" to 25

      ))

      println(user.name) // 打印结果为: "John Doe"

      println(user.age) // 打印结果为: 25

  }
  ```

  委托属性将从这个 map中读取属性值(使用属性名称字符串作为 key 值)。

  如果不用只读的 Map , 而改用值可变的 MutableMap , 那么也可以用作 var 属性的委托。
  ```
  class User(val map: MutableMap) {

      val name: String by map

      val age: Int by map

  }

  fun main(args: Array) {

      var map:MutableMap = mutableMapOf(

              "name" to "John Doe",

              "age" to 25)

      val user = User(map)

      println(user.name) // 打印结果为: "John Doe"

      println(user.age) // 打印结果为: 25

      println("--------------")

      map.put("name", "Green Dao")

      map.put("age", 30)

      println(user.name) // 打印结果为: Green Dao

      println(user.age) // 打印结果为: 30

  }
  ```
