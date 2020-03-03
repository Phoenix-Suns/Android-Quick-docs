# Coroutines + Retrofit 2: Call api

## Setup

```js
dependencies {
    //...
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:1.3.3"
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.3"

    implementation 'com.google.code.gson:gson:2.8.6'

    implementation 'io.reactivex.rxjava2:rxandroid:2.1.1'
    implementation 'io.reactivex.rxjava2:rxjava:2.2.17'

    implementation 'com.squareup.retrofit2:retrofit:2.7.1'
    implementation 'com.squareup.retrofit2:converter-gson:2.7.1'
    implementation 'com.squareup.retrofit2:adapter-rxjava2:2.7.1'
}
```

## Simple Sample

```java
interface GithubWS {
    @GET("users/{username}")
    suspend fun getGitHubUserDetail2(@Path("username") userName: String) : UserDetailModel
}

// Call on Android
private fun connect2() {
    val retrofit: Retrofit = Retrofit.Builder()
        .baseUrl("https://api.github.com/")
        //.addCallAdapterFactory(RxJava2CallAdapterFactory.create())
        //.addCallAdapterFactory(CoroutineCallAdapterFactory())
        .addConverterFactory(GsonConverterFactory.create())
        .build()
    val service: GithubWS = retrofit.create(GithubWS::class.java)

    CoroutineScope(Dispatchers.IO).launch {
        val response = service.getGitHubUserDetail2("luunghiatran")
        withContext(Dispatchers.Main) {
            try {
                toast("Exception ${response.name}")
            } catch (e: HttpException1) {
                toast("Exception ${e.message}")
            } catch (e: Throwable) {
                toast("Ooops: Something else went wrong")
            }
        }
    }
}
```

---

## Reference

- <https://kotlinlang.org/docs/reference/coroutines/basics.html>
- <https://github.com/Kotlin/kotlinx.coroutines/blob/master/ui/coroutines-guide-ui.md>
- <https://github.com/Kotlin/kotlinx.coroutines>
