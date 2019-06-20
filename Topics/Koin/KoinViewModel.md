
# Koin View Model - MVVM

## build.gradle

```json
dependencies {
    ...

    implementation "android.arch.lifecycle:extensions:1.1.1"
    implementation "android.arch.lifecycle:viewmodel:1.1.1"

    // Koin for Android - Scope feature
    // include koin-android-scope & koin-android
    implementation 'org.koin:koin-android-viewmodel:2.0.1'
}
```

## NameViewModel

```java
import androidx.lifecycle.LiveData
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel

class NameViewModel : ViewModel() {
    private val username = MutableLiveData<String>()

    fun setName(name: String) {
        /* expensive operation, e.g. network request */
        username.value = name
    }

    fun getUsername(): LiveData<String> {
        return username
    }
}
```

## MyApplication

### manifest

```java
import org.koin.android.ext.koin.androidContext
import org.koin.android.ext.koin.androidLogger
import org.koin.android.viewmodel.dsl.viewModel
import org.koin.core.context.startKoin
import org.koin.dsl.module

class MyApplication : Application(){
    override fun onCreate() {
        super.onCreate()
        // Start Koin
        startKoin{
            androidLogger()
            androidContext(this@MyApplication)
            modules(appModule)
        }
    }
}

val appModule = module {
    viewModel { NameViewModel() }
}
```

```xml
<manifest ...>
    <application
            android:name=".MyApplication"
            ... />
</manifest>
```


## Activity

```java
import android.os.Bundle
import android.view.View
import androidx.appcompat.app.AppCompatActivity
import androidx.lifecycle.Observer
import kotlinx.android.synthetic.main.activity_main.*
import org.koin.android.viewmodel.ext.android.viewModel

class MainActivity : AppCompatActivity() {
    val TAG = "MYSUN"
    var mCounter = 0

    //private lateinit var nameViewModel: NameViewModel // Simple ViewModel

    // Lazy Inject ViewModel
    private val nameViewModel: NameViewModel by viewModel()


    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        setupViewModel()
    }

    private fun setupViewModel() {
        /* Simple ViewModel */
        /*nameViewModel = ViewModelProviders.of(this).get(NameViewModel::class.java)
        nameViewModel.getUsername().observe(this, Observer { text ->
            textView_export.text = text
        })*/

        nameViewModel.getUsername().observe(this, Observer { text ->
            textView_export.text = text
        })
    }

    fun onExportClick(view: View) {
        mCounter += 1
        val newName = textView_export.text.toString() + "\n" + mCounter

        /*  Called if there is no active network request */
        nameViewModel.setName(newName)
    }
}
```
