# ViewModel

Lắng nghe biến thay đổi, thay đổi UI theo

## Simple Sample

```java
// https://medium.com/rocknnull/exploring-kotlin-using-android-architecture-components-and-vice-versa-aa16e600041a
class MainActivity : AppCompatActivity() {
    val TAG = MainActivity::class.java.name

    private lateinit var viewModel: NameViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        /* Called on Activity onCreate() */
        viewModel = ViewModelProviders.of(this).get(NameViewModel::class.java)
        viewModel.getUsername().observe(this, Observer { text ->
            Log.e(TAG, text)
        })

        /*  Called if there is no active network request */
        viewModel.initNetworkRequest()
    }
}

class NameViewModel : ViewModel() {
    private val username = MutableLiveData<String>()

    fun initNetworkRequest() {
        /* expensive operation, e.g. network request */
        username.value = "Peter"
    }

    fun getUsername(): LiveData<String> {
        return username
    }
}
```
