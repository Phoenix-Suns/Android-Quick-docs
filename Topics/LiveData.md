# LiveData - Data Gắn liền với Android Component Life Cycle

lifecycle-aware observable data holder. Quan sát thay đổi, tự update lên giao diện

<!-- TOC -->

- [LiveData - Data Gắn liền với Android Component Life Cycle](#livedata---data-gắn-liền-với-android-component-life-cycle)
    - [Setup Step By Step](#setup-step-by-step)
        - [Setup](#setup)
        - [File View Model](#file-view-model)
        - [Update Date trên Android Component (Activity)](#update-date-trên-android-component-activity)
    - [Lắng nghe qua Các FRAGMENT](#lắng-nghe-qua-các-fragment)
    - [Simple Kotlin](#simple-kotlin)

<!-- /TOC -->

## Setup Step By Step

### Setup

project Gradle file

```js
// build.gradle (Project: <your_app>)
allprojects {
    repositories {
        jcenter()
       google()
    }
}

// build.gradle (app)
dependencies {
    ...
    implementation 'android.arch.lifecycle:extensions:1.1.1'
    implementation 'android.arch.lifecycle:viewmodel:1.1.1'
}
```

### File View Model

Lớp lưu trữ, quản lý dữ liệu

```js
// Android View Model tồn tại dự vào vòng đời Activity
class NameViewModel : ViewModel() {

    // Create a LiveData with a String, Mutable(Có thể đổi)
    private var mCurrentName: MutableLiveData<String>? = null

    val currentName: MutableLiveData<String>
        // trigger user load.
        get() {
            if (mCurrentName == null) {
                mCurrentName = MutableLiveData<String>()
            }
            return mCurrentName!!
        }

    // Rest of the ViewModel...
}
```

### Update Date trên Android Component (Activity)

```java
class MainActivity : AppCompatActivity() {

    private var mModel: NameViewModel? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        ...
        observeName()

        button.setOnClickListener( View.OnClickListener {
            // 1 ========= Update Data => Update LiveData objects =============
            mModel!!.currentName.value = "Some thing You don't know"
        })
    }

    private fun observeName() {
        // Binding Activity with ViewModel
        mModel = ViewModelProviders.of(this).get(NameViewModel::class.java)

        // 2 ========= Create the Observer which updates the UI. Listen Change
        val nameObserver = object : Observer<String> {
            override fun onChanged(newName: String?) {
                // Update the UI, when Value change
                textView.text = newName
            }
        }

        // Observe the LiveData, passing in this activity as the LifecycleOwner and the observer.
        mModel!!.currentName.observe(this, nameObserver)
    }
}
```

## Lắng nghe qua Các FRAGMENT

```java
// ======== View Model =============
public class MasterViewModel extends AndroidViewModel {
   // Trong master có một biến là selected item, mỗi khi có sự kiện click thì selected item được thay đổi
   private MutableLiveData<String> mSelectedItem = new MutableLiveData<>();

   public MasterViewModel(@NonNull Application application) {
       super(application);
   }
   public MutableLiveData<String> getSelectedItem() {
       return mSelectedItem;
   }

   public void setSelectedItem(String selectedItem) {
       mSelectedItem.setValue(selectedItem);
   }
}

//************************************************
public class MasterFragment extends Fragment implements MasterAdapter.OnItemClickListenner{
   private MasterViewModel mViewModel;
   
   @Override
   public void onCreate(@Nullable Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       mViewModel = ViewModelProviders.of(getActivity()).get(MasterViewModel.class);
        /* mViewModel.getData().observe(this, new Observer<List<String>>() {
           @Override
           public void onChanged(@Nullable List<String> strings) {
               // Update UI, when data change
               mAdapter.addData(strings);
           }
       }); */
   }
   
    @Override
   public void onClick(String result) {
       // Đây là sự kiện click vào một item trong recyclerview
       mViewModel.setSelectedItem(result);
   }
   
//************************************************
 public class DetailFragment extends Fragment{
    private MasterViewModel mViewModel;
     
    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       // Trong DetailFragment sử dụng MasterViewModel, và lắng nghe sự kiện getSelectedItem
       mViewModel = ViewModelProviders.of(getActivity()).get(MasterViewModel.class);
       mViewModel.getSelectedItem().observe(this, new Observer<String>() {
           @Override
           public void onChanged(@Nullable String s) {
               mUserName.setText(s);
           }
       });
   }
}
```

## Simple Kotlin

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

    fun goListUser (view: View) {
        startActivity(Intent(this, ListUserActivity::class.java))
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

---
**Tham khảo**

- https://viblo.asia/p/android-livedata-oOVlY1Yrl8W
- https://viblo.asia/p/android-architecture-components-viewmodel-xu-ly-configuration-changes-chua-bao-gio-don-gian-den-the-ByEZk3A4ZQ0
- https://developer.android.com/jetpack/docs/guide#best-practices
