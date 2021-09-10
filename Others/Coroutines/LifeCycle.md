# Lifecycle: quản lý Coroutine trong Vòng đời 1 android component

- [Lifecycle: quản lý Coroutine trong Vòng đời 1 android component](#lifecycle-quản-lý-coroutine-trong-vòng-đời-1-android-component)
  - [Cài đặt](#cài-đặt)
  - [LifecycleScope: huỷ khi vòng đời Destroyed](#lifecyclescope-huỷ-khi-vòng-đời-destroyed)
    - [Run Coroutine: Laucher](#run-coroutine-laucher)
    - [Catch Exception](#catch-exception)
  - [Bắt đầu, kết thúc](#bắt-đầu-kết-thúc)
    - [Chạy cùng Android component life cycle](#chạy-cùng-android-component-life-cycle)
    - [Chạy cùng ViewModel: viewModelScrope.launch { }](#chạy-cùng-viewmodel-viewmodelscropelaunch--)
    - [Chạy cùng LiveData](#chạy-cùng-livedata)
  - [Tham khảo](#tham-khảo)

## Cài đặt

```js
dependencies {
    implementation 'androidx.lifecycle:lifecycle-livedata-ktx:2.3.1'
    implementation 'androidx.lifecycle:lifecycle-viewmodel-ktx:2.3.1'
    implementation 'androidx.lifecycle:lifecycle-livedata-ktx:2.3.1'
    implementation 'androidx.lifecycle:lifecycle-common-java8:2.4.0-alpha03'
}
```

## LifecycleScope: huỷ khi vòng đời Destroyed

### Run Coroutine: Laucher

```java
// Activity
lifecycle.coroutineScope.launch {...}
lifecycleScope.launch {...}

// Fragment
viewLifecycleOwner.lifecycleScope.launch {...}
lifecycle.coroutineScope.launch {...}
lifecycleScope.launch {...}
```

### Catch Exception

```java
val handler = CoroutineExceptionHandler { coroutineContext, throwable ->
    Log.e("CoroutineExceptionHandler", "$throwable")
}
lifecycleScope.launch(handler) {
    throw NumberFormatException()
}

// OR
lifecycleScope.launch {
    launch(handler) {
        //...
    }
}
```

## Bắt đầu, kết thúc

### Chạy cùng Android component life cycle

```java
lifecycleScope.launchWhenCreated {...}  // dừng khi gọi onDestroy()
lifecycleScope.launchWhenStarted {...}  // dừng khi gọi onStop()
lifecycleScope.launchWhenResumed {...}  // dừng khi gọi onPause()
```

### Chạy cùng ViewModel: viewModelScrope.launch { }

- Dừng khi viewModel cleared
- Chạy trên Dispatchers.Main (Main thread)

### Chạy cùng LiveData

```java
val dataStr = liveData { 
    emit("abc") // Gởi data

    // Gởi dữ liệu vào dataStr
    val liveData = MutableLiveData<String>("abc")
    emitSource(liveData)
 }


// OR
liveData(Dispatchers.IO, timeoutInMs = 20) { 
    emit("abc") // Gởi data
 }
```

## Tham khảo

- <https://medium.com/android-news/coroutine-in-android-working-with-lifecycle-fc9c1a31e5f3>