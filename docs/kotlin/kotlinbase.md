# Kotlin基础

### 变量

```kotlin
fun main() {

    // 可变变量定义：var 关键字
    // var <标识符> : <类型> = <初始化值>

    // 不可变变量定义：val 关键字，只能赋值一次的变量(有一点点类似Java中final修饰的变量)
    // val <标识符> : <类型> = <初始化值>

    // 可以修改的
    var name: String = "Derry"
    name = "张三"
    name = "李四"

    var info1 = "AAAA" // 类型推到
    var info2 = 'A' // 类型推到  Char
    var info3:Int = 99 // 类型推到  Int

    // 不可以修改的
    val age: Int = 99
    // age = "" 报错


    // 静态语言 编译期 就决定了 String类型
    var info4 = "LISI"  // info4==String类型
    // info4 = 88   // js 动态解释语言
}

class Test {

    // 可以改，可以读  get  set
    var info1 : String = "A"

    // 只能读， 只有 get
    val info2 : String = "B"
}
```

------

### 空机制

```kotlin
// TODO NULL检查机制
fun main() {
    // Kotlin的空安全设计对于声明可为空的参数，在使用时要进行空判断处理，
    // 有两种处理方式，字段后加!!像Java一样抛出空异常，另一种字段后加?

    var info: String? = null

    // println(info?.length)  // 第一种补救：? 如果info是null，就不执行 .length

    // println(info!!.length) // 第2种补救： !! 我自己负责info 不会为null ==  (不管null不null必须执行)  ==  Java

    /*if (info != null)           // 第3种补救：  ==  Java
        println(info.length)*/

    // println(testMethod("AAAAA"))

    // 刚那个提问的呢?
    // ?:  如果你一定要输出null  就让你  “你很牛逼”
    println(info?.length           ?: "你很牛逼")
}

// : Int? === 允许返回null
fun testMethod(name: String) : Int? {
    if (name == "zs") {
        return 99999
    }
    return null
}
```

------

### 字符串

```kotlin
// TODO 字符串模板
fun main() {

    // $ 表示一个变量名或者变量值

    // $varName 表示变量值

    // ${varName.fun()} 表示变量的方法返回值

    val name = "张三"

    val age = 28

    val sex = 'M'

    val info = "ABCDEFG"

    println("name:$name,  age:$age,  sex:$sex  info:$info")

    // --- 自己不用关系 \n 换行 ，不用自己调整
    val infoMesage = """
        AAAAAAAAAAA
        BBBBBBBBBBB
        CCCCCCCCCCC
        DDDDDDDDDDD
        EEEEEEEEEEE
    """  // 前置空格
    println(infoMesage)

    val infoMesage2 = """
        AAAAAAAAAAA
        BBBBBBBBBBB
        CCCCCCCCCCC
        DDDDDDDDDDD
        EEEEEEEEEEE
    """.trimIndent()  // 没空格
    println(infoMesage2)

    val infoMesage3 = """
        ?AAAAAAAAAAA
        ?BBBBBBBBBBB
        ?CCCCCCCCCCC
        ?DDDDDDDDDDD
        ?EEEEEEEEEEE
    """.trimMargin("?")  // 没空格 控制|
    println(infoMesage3)

    // 需求：显示 $99999.99
    val price = """
        ${'$'}99999.99
    """.trimIndent()
    println(price)
}
```

------

### 函数

```kotlin
// TODO 函数  方法

// void == :Unit
fun main(): Unit {

    println(add(1, 1))

    lenMethod(1, 2, 3, 4, 5, 6, 7)

    // lambda表达式函数
    val addMethod : (Int, Int) -> Int = {number1, number2 -> number1 + number2}
    val r= addMethod(9, 9)
    println(r)
}

// 返回类型Int
fun add(number1: Int, number2: Int): Int {
    return number1 + number2
}

// 返回类型  == 类型推导 Int
fun add2(number1: Int, number2: Int) = number1 + number2

// 返回类型  == 类型推导 String
fun add3(number1: Int, number2: Int) = "AAA"

// 可变参数 （可变长 参数函数）
fun lenMethod(vararg value: Int) {
    for (i in value) {
        println(i)
    }
}
```

------

### 区间

```kotlin
// TODO 区间
fun main() {

    // 1 到 9
    for (i in 1..9) {
        // println(i)
    }

    // 不会输出
    for (i in 9..1) {
        // println(i)
    }

    // 大 到 小
    for (i in 9 downTo 1) {
        // println(i)
    }

    // 用区间做判断
    val value = 88
    if (value in 1..100) {
        // println("包了 1 到 100")
    }

    // 步长指定
    for (i in 1..20 step 2) {
        // 1 3 5 7 ...
        // println(i)
    }

    // 排除 最后元素
    for (i in 1 until 10) {
        println(i)
    }
}
```

------

### 比较

```kotlin
// TODO 比较两个值
fun main() {

    val name1: String = "张三"
    val name2: String = "张三"

    // --- 比较值本身
    // == 等价 Java的equals
    println(name1.equals(name2))
    println(name1 == name2)


    // ---  比较对象地址
    val test1:Int? =  10000
    val test2:Int? =  10000
    println(test1 === test2) // false
}
```

------

### 数组

```kotlin
// TODO 数组
fun main() {
    // 第一种形式
    val numbers = arrayOf(1, 2, 3, 4, 5, 6, 7, 8)
    // println(numbers[0])
    // println(numbers[7])
    for (number in numbers) {
        // println(number)
    }

    // 第二种形式  value=0
    // 0 + 200 = 200
    // 1 + 200 = 201
    val numbers2 = Array(10,  {value: Int -> (value + 200) })
    for (value in numbers2) {
        println(value)
    }

    // 定义一个变量 value Int类型
    // value=0 + 200
    // {value: Int                ->                     (value + 200) }
}
```

------

### 条件

```kotlin
// TODO 条件
fun main() {
    val number1: Int = 9999999
    val number2: Int = 8888888

    // 表达式 比 大小 最大值
    val maxValue = if (number1 > number2) number1 else number2
    // println(maxValue)

    val max: Int = if (number1 > number2) {
        println("number1是最大的哦")
        // TODO
        // ....... 省略 200行代码
        number1
    } else {
        println("number2是最大的哦")
        // TODO
        // .......  省略 200行代码
        number2
    }
    // println(max)

    // 使用区间做判断
    val x = 80
    val y = 29
    if (x in 1..10 && y in 1..50) {
        // println("x y 轴 符合")
    } else {
        // println("x y 轴 不符合")
    }

    println()


    /* val number = 5
     when(number) {
         1 -> println("一")
         2 -> println("二")
         3 -> println("三")
         4 -> println("四")
         5 -> println("五")
         else -> println("其他")
     }*/

    /*val number = 745
    when(number) {
        in 1..100 -> println("1..100")
        in 200..500 -> println("200..500")
        else -> println("其他")
    }*/


    // Object == Any ?
    // val str : String = ""

    val number = 3
    val result = when (number) {
        1 -> {
            println("很开心")
            // 很多很多的逻辑..  省略 200行代码
            // ...
            // TODO ....
            "今天是星期一"
            99
        }
        2 -> {
            println("很开心")
            // 很多很多的逻辑..  省略 200行代码
            // ...
            // TODO ....
            "今天是星期二"
            88
        }
        3 -> {
            println("很开心")
            // 很多很多的逻辑..  省略 200行代码
            // ...
            // TODO ....
            "今天是星期三"
            true
            100
        }
        else -> 99
    }
    // println(result)


    when (8) {
        1, 2, 3, 4, 5, 6, 7 -> println("满足")
        else -> println("不满足")
    }
}
```

------

### 循环

```kotlin
// TODO 循环 与 标签
fun main() {

    // 自定义标签
    tttt@ for (i in 1..20) {

        for (j in 1..20) {
            println("i:$i, j:$j")

            if (i == 5) {
                // break // j循环 给break

                break@tttt // i循环 给break
            }
        }

    }


    // TODO 循环
    var items  = listOf<String>("李四", "张三", "王五")
    for (item in items) {
        println(item)
    }

    items.forEach {
        println(it)
    }
  
    items.forEach {aaaa->
       println(aaaa)
    }

    for (index in items.indices) {
        println("下标：$index,  对应的值：${items[index]}")
    }

}

// 自带的标签
class Derry {

    val I = "AAAA"

    fun show() {
        println(I)
        println(this.I)
        println(this@Derry.I)
    }

}
```

------

### 类

```kotlin
// 默认就是public
class Empty
```

### 构造

```kotlin
// public final class Person 默认就是这样的，不能被继承，  可以加open就可以被人家继承了
open class Person(id: Int) // 主构造
{

    // 次构造
    constructor(id: Int, name: String) : this(id) {

    }

    // 次构造
    constructor(id: Int, sex: Char) : this(id) {

    }

    // 次构造
    constructor() : this(787) {

    }

}
```

### 继承

```kotlin
class Student(id: Int) : // Person() // 次构造
                Person(id) // 主构造了
{
    // 再Kotlin 全部都是没有默认值的

    // 再Java 成员有默认值，但是方法内部没有默认值

    // lateinit 懒加载  没有赋值 就不能使用，否则报错
    lateinit var name : String
    var age: Int = 0
}
```

------

### 接口

```kotlin
// 接口默认都是 open
interface Callback {

    fun callbackMethod() : Boolean

}
```

### 接口继承

```kotlin
// 默认是 public final class Person 不能被人家继承的      abstract有open的特征 相当于open
abstract class Person : Callback , Callback2 {

    abstract fun getLayoutID() : Int

    abstract fun initView()

}
```

### 抽象类继承

```kotlin
class Student : Person() {

    override fun getLayoutID(): Int = 888

    override fun initView() { }

    override fun callbackMethod(): Boolean  = false
}
```

------

### 数据类

```kotlin
/**
 * 数据类  == Java实体Bean
 */

// 不会再修改了，可以是使用val
// get set 构造 equals hashCode toString，  copy
data class User(val id: Int, val name: String, val sex: Char)
```

```kotlin
// TODO  data数据类 ，object单例
fun main() {

    val user = User(99, "lisi", 'M')

    val(myID, myName, mySex) = user.copy()
    println("myID:$myID, myName:$myName, mySex:$mySex")

    // _ 代表不接收, 部分拷贝
    val(_, myName2, _) = user.copy()
    println("myName2:$myName2")
}
```

###  object单例

```kotlin
// 只实例一次   相当于 单例， 全部都是static函数
object MyEngine {

    fun m() {
        println("M run")
    }

    fun show() {
        println("我就只有一个实例")
    }
}
```



### 单例模式1

```kotlin
// TODO　Kotlin 单例模式1
class NetManager {

    // 只有一个实例
    object Holder {

        val instance = NetManager()

    }

    // 派生操作，全是static
    companion object {

        // 全部都是  相当于 Java static

        fun getInstance() : NetManager = Holder.instance
    }

    fun show(name: String) {
        println("show:$name");
    }

}

// 客户端
fun main() {
    val net  = NetManager.getInstance()
    net.show("kt Derry1")
}
```

### 单例模式2

```kotlin
// TODO　kt 单例模式2
class NetManager2 {

    companion object {

        private var instance: NetManager2? = null

        // 返回值：允许你返回null
        fun getInstance(): NetManager2? {
            if (instance == null) {
                instance = NetManager2()
            }

            // 如果是null，也返回回去了
            return instance

            // 第二种补救： 我来负责 instance 肯定不为null
            // return instance!!
        }

    }


    fun show(name: String) {
        println("show:$name");
    }

}

fun main() {
    // 客户端 来做补救措施
    val netManager = NetManager2.getInstance()

    // 如果 netManager == null ，就不执行 .show("AAA")
    netManager?.show("AAA")
}
```

### 内部类

```kotlin
class Test {

    val I = "AAAA"

    // 这个不是一个内部类，所有拿不到外部类的成员
    // 嵌套类 = 可以在类的里面再写一个类，但是这个类和外部类 不交互
    class Sub {

        fun show() {
            println()
        }


        class A {


            class B {


                class C {

                }

            }

        }

    }

    // 这个才是内部类
    inner class Sub2 {

        fun show() {
            println(I)
        }

    }

}

//嵌套函数，没有什么意义
fun main() {
    fun a() {

        fun b() {

            fun c() {

                fun d() {

                }

            }

        }

    }
}

```

