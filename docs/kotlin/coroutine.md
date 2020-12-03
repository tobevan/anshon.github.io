协程

```kotlin
val scope: CoroutineScope = MainScope();

scope.launch(Dispatchers.Main) {
    ioCode1()
    ioCode2()
}
uiCode1()

2020-12-03 00:09:12.593 7202-7202/com.anshon.kotlinlang I/System.out: uiCode1 main
2020-12-03 00:09:13.710 7202-7255/com.anshon.kotlinlang I/System.out: ioCode1 DefaultDispatcher-worker-1
2020-12-03 00:09:16.753 7202-7255/com.anshon.kotlinlang I/System.out: ioCode2 DefaultDispatcher-worker-1
-------------------------------------------
scope.launch(Dispatchers.Main) {
    coroutineScope {
        launch { ioCode2() }
        launch { ioCode1() }
    }
    uiCode1()
    uiCode2()
}

2020-12-03 00:01:44.620 7006-7082/com.anshon.kotlinlang I/System.out: ioCode1 DefaultDispatcher-worker-3
2020-12-03 00:01:46.624 7006-7079/com.anshon.kotlinlang I/System.out: ioCode2 DefaultDispatcher-worker-1
2020-12-03 00:01:46.625 7006-7006/com.anshon.kotlinlang I/System.out: uiCode1 main
2020-12-03 00:01:46.625 7006-7006/com.anshon.kotlinlang I/System.out: uiCode2 main
-------------------------------------------
scope.launch(Dispatchers.Main) {
    println("code launch  ${Thread.currentThread().name}")
    val one = async { ioCode1() }
    val two = async { ioCode2() }
    val same = (one.await() == two.await())
    println("code one == two:$same")
}

2020-12-03 00:15:21.952 7445-7445/com.anshon.kotlinlang I/System.out: code launch  main
2020-12-03 00:15:23.074 7445-7496/com.anshon.kotlinlang I/System.out: ioCode1 DefaultDispatcher-worker-1
2020-12-03 00:15:25.074 7445-7498/com.anshon.kotlinlang I/System.out: ioCode2 DefaultDispatcher-worker-3
2020-12-03 00:15:25.075 7445-7445/com.anshon.kotlinlang I/System.out: code one == two:true

-------------------------------------------
suspend fun ioCode1() {
    withContext(Dispatchers.IO) {
        Thread.sleep(1000)
        println("ioCode1 ${Thread.currentThread().name}")
    }
}

suspend fun ioCode2() {
    withContext(Dispatchers.IO) {
        Thread.sleep(3000)
        println("ioCode2 ${Thread.currentThread().name}")
    }
}

fun uiCode1() {
    println("uiCode1 ${Thread.currentThread().name}")
}

fun uiCode2() {
    println("uiCode2 ${Thread.currentThread().name}")
}

//避免协程泄露
override fun onDestroy() {
    super.onDestroy()
    scope.cancel()  //或者launch时使用   lifecycleScope.launch{}
}

```



```kotlin
user:LiveData<User> = livedata{
 emit(loadData()) //从后台推到前台
}

private suspend loadData(): User{

}
```



coroutines ->ViewModule -> liveData -> lifecycle -> Room   Repository  协程和 jetpack结合使用



1.协程怎么切线程



2.协程挂起为什么不卡线程



3.delay比sleep性能更好？错误的,  下面两种写法是等价的。当然如果直接在主线程sleep会卡，要开子线程

遗留：同时开10万个协程delay比同时开10万个线程性能好

```kotlin
scope.launch {
    delay(1000)
    textView = "test"
}

Thread{
    sleep(1000)
    runOnUiThread{
        textView = "test"
    }
}
```



<img src="coroutine.assets/image-20201203011304238.png" alt="image-20201203011304238"/>

