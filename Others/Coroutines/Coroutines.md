# Coroutines

- Avoid lamda CALL BACK Hell
- Hoạt động giống Thread, nhẹ hơn
- Thực hiện tuần tự, từ trên xuống

- [Coroutines](#coroutines)
  - [Install](#install)
  - [Using](#using)
  - [Suspend function](#suspend-function)
    - [Chạy 2 Functions Độc lập, đợi hoàn thành](#chạy-2-functions-độc-lập-đợi-hoàn-thành)
  - [Quản lý nhiều Coroutine cùng lúc: CoroutineScope](#quản-lý-nhiều-coroutine-cùng-lúc-coroutinescope)
    - [Huỷ hết tác vụ trong Activity, ViewModel android](#huỷ-hết-tác-vụ-trong-activity-viewmodel-android)
  - [1 số hàm](#1-số-hàm)
    - [Dừng, chờ: delay(1000L) //chờ 1 giây](#dừng-chờ-delay1000l-chờ-1-giây)
    - [lặp lại 100_000 coroutines](#lặp-lại-100_000-coroutines)
    - [Tính thời gian chạy](#tính-thời-gian-chạy)
    - [Set Context chạy](#set-context-chạy)
    - [Đặt tên Coroutine](#đặt-tên-coroutine)
    - [Lấy context + thêm 1 context test](#lấy-context--thêm-1-context-test)
    - [Lấy Tên thread: Thread.currentThread().name](#lấy-tên-thread-threadcurrentthreadname)
  - [Hàm điều khiển](#hàm-điều-khiển)
    - [Chờ hàm thực hiện xong: job.join()](#chờ-hàm-thực-hiện-xong-jobjoin)
    - [Huỷ thực hiện: job.cancel()](#huỷ-thực-hiện-jobcancel)
    - [Kiểm tra còn hoạt động: job.isActive](#kiểm-tra-còn-hoạt-động-jobisactive)
    - [Trước khi huỷ vào block finally](#trước-khi-huỷ-vào-block-finally)
    - [Tự huỷ sau 10s: withTimeout(1000L) { }](#tự-huỷ-sau-10s-withtimeout1000l--)
  - [Exception](#exception)
    - [Bắt nhiều Exception](#bắt-nhiều-exception)
    - [Bỏ qua Thread lỗi: SupervisorJob](#bỏ-qua-thread-lỗi-supervisorjob)
  - [Reference](#reference)

## Install

```js
dependencies {
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.5.1'
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.5.1'

    // Nếu dùng ViewModel, tự huỷ thread của ViewModel
    implementation "androidx.lifecycle.lifecycle-viewmodel-ktx$lifecycle_version"
}
```

## Using

```java
val job = runBlocking {
    /* Task */
    // OR
    launch { /* Task */ }
    // OR
    var task = async { /* Task */ }
    task.await()
}

// OR
// Tránh dùng GlobalScope, có thể không cancel được
val job = GlobalScope.launch {
    /* Task */
}

// OR
val deferred = GlobalScope.async {
    /* Task */
}
deferred.await()

// OR
// Coroutine Context: Ngữ cảnh chạy của Thread
// Cú pháp: Dispatchers + Job + ...
runBlocking(Dispatchers.IO + Job()) {
    /* Task */
}
```

- Coroutine Context: Job + Dispatchers + ...
  - **Job**: Quản lý Lifecycle, cancel...
  - **CoroutineName("name")**: đặt tên cho coroutine
  - **NonCancellable**: không cho cancel, kể cả khi đã gọi method cancel coroutine
  - **Dispatcher**: thread quản lý
    - **Dispatchers.Main**: chạy trên main UI thread
    - **Dispatchers.IO**: chạy trên background thread của thread pool. Thường được dùng khi Read, write files, Database, Networking
    - **Dispatchers.Default**: chạy trên background thread của thread pool. Thường được dùng khi sorting a list, parse Json, DiffUtils
    - **Dispatchers.Unconfined**: Chạy trên thread bất kì, có thể chuyển từ UI thread sang background Thread
    - **newSingleThreadContext("name_thread")**: chạy trên một thread do mình đặt tên
    - **newFixedThreadPoolContext(3, "name_thread")**: sử dụng 3 threads trong shared background thread pool

## Suspend function

```java
// Run
runBlocking {
    launch { testSuspend() }
    //or testSuspend()
}

suspend fun testSuspend() {
    delay(1000L)
    println("Hello!")
}
```

### Chạy 2 Functions Độc lập, đợi hoàn thành

```java
runBlocking {
    val one: Deferred<Int> = async { printNumber(10) }
    val two: Deferred<Int> = async { printNumber(20) }

    // Đợi one, two cùng thực hiện xong
    val result = one.await() + two.await()
    println("The answer is $result")
}

suspend fun printNumber(number: Int): Int {
    delay(1000L)
    return number
}
```

## Quản lý nhiều Coroutine cùng lúc: CoroutineScope

```java
val jobs = CoroutineScope(Dispatchers.IO).launch {
    launch { /*execute task 1*/ }
    launch { /*execute task 2*/ }
    async { /*execute task 3*/ }
}

// OR
val jobs = launch {
    launch { /*Task 1*/ }
    launch { /*Task 2*/ }
}

jobs.cancel() // Huỷ 3 task
```

### Huỷ hết tác vụ trong Activity, ViewModel android

```java
class Activity : CoroutineScope by CoroutineScope(Dispatchers.Default) {
    fun destroy() {
        cancel() // Khi activity huỷ, huỷ hết thread
    }
 
    // demo: chạy 10 coroutines
    fun doSomething() {
        repeat(10) { i ->
            launch {
                delay((i + 1) * 200L) // variable delay 200ms, 400ms, ... etc
                println("Coroutine $i is done")
            }
        }
    }
 }
 
 // VIEW MODEL
 class MyViewModel constructor(private val apiService : ApiService) : ViewModel(), CoroutineScope {  
  
   private val job = Job()
   override val coroutineContext: CoroutineContext get() = job + Dispatchers.Main
  
   fun executeCalls() {
      launch(context = coroutineContext) {
           val firstRequestDeferred = async {
                apiService.request1()
           }
           val secondRequestDeffered = async {
                apiService.request2()
           }
           handleResponse(firstRequestDeferred,await(),secondRequestDeffered.await())
      }  
   }
  
   override fun onCleared(){
       job.cancel()
   }
 }
```

## 1 số hàm

### Dừng, chờ: delay(1000L) //chờ 1 giây

### lặp lại 100_000 coroutines

```java
repeat(100_000) { 
    launch {
        Log.d("hehehe", "hello")
    }
}
```

### Tính thời gian chạy

```java
val runingTime = measureTimeMillis { testSuspend() }
```

### Set Context chạy

```java
newSingleThreadContext("thread1").use { ctx1 ->
    newSingleThreadContext("thread2").use { ctx2 ->
        runBlocking(ctx1) {
            // Chạy context 2 trong context 1
            withContext(ctx2) {
                println("Working in ctx2 - ${Thread.currentThread().name}")
            }
        }
    }
}

// Set Context chạy
GlobalScope.launch(Dispatchers.IO) {
    // do background task
    withContext(Dispatchers.Main) {
        // update UI
    }
}
```

### Đặt tên Coroutine

```java
GlobalScope.launch(CoroutineName("demo_2")) {
    // coroutine được đặt tên là demo_2
    println("Working in ctx2 - ${Thread.currentThread().name}")
}
```

### Lấy context + thêm 1 context test

```java
val coroutineContext1 = coroutineContext + CoroutineName("test")
println("A context with name: $coroutineContext1")
```

### Lấy Tên thread: Thread.currentThread().name

## Hàm điều khiển

```java
val job = GlobalScope.launch { // launch a new coroutine and keep a reference to its Job
    delay(5000L)
    println("World!")
}
```

### Chờ hàm thực hiện xong: job.join()

### Huỷ thực hiện: job.cancel()

### Kiểm tra còn hoạt động: job.isActive

### Trước khi huỷ vào block finally

```java
runBlocking {
    val job = launch {
        try {
            //...
        } finally {
            // Thực hiện trước khi huỷ

            withContext(NonCancellable) {
                // Có thể chạy job tiếp
            }
        }
    }
    job.cancel()
}
```

### Tự huỷ sau 10s: withTimeout(1000L) { }

```java
// Tự huỷ trả về null, hoàng thành trả về "Done"
val result = withTimeoutOrNull(1000L) {
    repeat(1000) { i ->
            println("I'm sleeping $i ...")
        delay(500L)
    }
    "Done"
}
println("Result is $result") 
```

## Exception

```java
fun main() = runBlocking {
    GlobalScope.launch {
        try {
            throw IndexOutOfBoundsException()
        } catch (e: IndexOutOfBoundsException) {
            // Catch Exception
        }
    }

    // OR
    val deferred = GlobalScope.async {
        throw ArithmeticException()
    }
    try {
        deferred.await()
    } catch (e: ArithmeticException) {
        // Catch Exception
    }
}
```

### Bắt nhiều Exception

```java
fun main() = runBlocking {
    val handler = CoroutineExceptionHandler { _, exception ->
        println("Caught $exception")
    }
    val job = GlobalScope.launch(handler) {
        throw AssertionError()
    }
    val deferred = GlobalScope.async(handler) {
        throw ArithmeticException()
    }
    
    // Get All Exception
    joinAll(job, deferred)  // System.out: Caught java.lang.AssertionError
}

// === OR ===
fun main() = runBlocking {
    val handler = CoroutineExceptionHandler { _, exception ->
        println("Caught $exception with suppressed ${exception.suppressed.contentToString()}")
    }
    val job = GlobalScope.launch(handler) {
        launch {
            try {
                delay(Long.MAX_VALUE) // delay vô hạn
            } finally {
                throw ArithmeticException()
            }
        }
        launch {
            try {
                delay(Long.MAX_VALUE) // delay vô hạn
            } finally {
                throw IndexOutOfBoundsException()
            }
        }
        launch {
            delay(100)
            throw IOException()
        }
        delay(Long.MAX_VALUE)
    }

    job.join() // System.out: Caught java.io.IOException with suppressed [java.lang.ArithmeticException, java.lang.IndexOutOfBoundsException]
}
```

### Bỏ qua Thread lỗi: SupervisorJob

```java
fun main() = runBlocking {
    val supervisor = SupervisorJob()

    with(CoroutineScope(coroutineContext + supervisor)) {
        // First job error
        val firstJob = launch(CoroutineExceptionHandler { _, _ ->  }) {
            throw AssertionError("First child is cancelled")
        }

        // Second Job continue after First Error
        val secondJob = launch {
            firstJob.join()
            println("job 2 continue")
        }
    }
}
```

## Reference

- <https://viblo.asia/p/cung-hoc-kotlin-coroutine-phan-1-gioi-thieu-kotlin-coroutine-va-ky-thuat-lap-trinh-bat-dong-bo-gGJ59xajlX2>
