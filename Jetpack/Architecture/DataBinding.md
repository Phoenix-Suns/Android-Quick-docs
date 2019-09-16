# Data Binding: hiển thị giao diện XML theo Data

- [Data Binding: hiển thị giao diện XML theo Data](#data-binding-hi%e1%bb%83n-th%e1%bb%8b-giao-di%e1%bb%87n-xml-theo-data)
  - [Cài đặt](#c%c3%a0i-%c4%91%e1%ba%b7t)
  - [Sử dụng](#s%e1%bb%ad-d%e1%bb%a5ng)
    - [Sử dụng trong Activity](#s%e1%bb%ad-d%e1%bb%a5ng-trong-activity)
    - [Trong Activity với ViewModel](#trong-activity-v%e1%bb%9bi-viewmodel)
    - [Sử dụng trong Fragment](#s%e1%bb%ad-d%e1%bb%a5ng-trong-fragment)
    - [Sử dụng trong RecyclerView](#s%e1%bb%ad-d%e1%bb%a5ng-trong-recyclerview)
  - [1 số thao tác trong View](#1-s%e1%bb%91-thao-t%c3%a1c-trong-view)
  - [BindingAdapter: Tạo thuộc tính mới cho View](#bindingadapter-t%e1%ba%a1o-thu%e1%bb%99c-t%c3%adnh-m%e1%bb%9bi-cho-view)
  - [Common Errors](#common-errors)
  - [Tham khảo](#tham-kh%e1%ba%a3o)

## Cài đặt

- build.gradle

```json
android { //...
    dataBinding {
       enabled true
    }
}
```

## Sử dụng

### Sử dụng trong Activity

- Activity

```java
override fun onCreate() {
    // setContentView(R.layout.plain_activity)
    // (plain_activity.xml = PlainActivityBinding)
    val binding : PlainActivityBinding = DataBindingUtil.setContentView(this, R.layout.plain_activity)

    // Đặt dữ liệu
    binding.name = "Your name"
    binding.lastName = "Your last name"
}
```

```xml
<!--Cài đặt Layout-->
<layout>
    Dữ liệu sẽ sử dụng
   <data>
        <variable name="name" type="String"/>
        <variable name="lastName" type="String"/>
   </data>
   <androidx.constraintlayout.widget.ConstraintLayout
           android:layout_width="match_parent"
           android:layout_height="match_parent">

       <TextView
```

```xml
<!-- Hiện dữ liệu lên View -->
<TextView
    android:id="@+id/plain_name"
    android:text="@{name}"/>

<TextView
    android:id="@+id/plain_lastname"
    android:text="@{lastName}"/>
```

### Trong Activity với ViewModel

- Activity

```java
private val viewModel by lazy {
    ViewModelProviders.of(this).get(LikeViewModel::class.java)
}

override fun onCreate() {
    //(plain_activity.xml = PlainActivityBinding)
    val binding : PlainActivityBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)

    // Gán viewmodel trong xml = viewModel trong Activity
    binding.viewmodel = viewModel
}
```

- Layout: activity_main.xml

```xml
<!-- Cài đặt -->
<data>
    <variable
            name="viewmodel"
            type="com.example.android.databinding.basicsample.data.SimpleViewModel"/>
</data>

<!-- Hiện dữ liệu lên View -->
<TextView
        android:id="@+id/plain_name"
        android:text="@{viewmodel.name}"/>
<TextView
        android:id="@+id/plain_lastname"
        android:text="@{viewmodel.lastName}"
        android:onClick="@{() -> viewmodel.onLike()}" />
```

### Sử dụng trong Fragment

```java
private val viewModel by lazy {
    ViewModelProviders.of(this).get(LikeViewModel::class.java)
}

override fun onCreateView(
    inflater: LayoutInflater, container: ViewGroup?,
    savedInstanceState: Bundle?
): View? {
    //return inflater.inflate(R.layout.fragment_like, container, false)
    // (fragment_like.xml = FragmentLikeBinding)
    val binding: FragmentLikeBinding = DataBindingUtil.inflate(inflater, R.layout.fragment_like, container, false)
    binding.lifecycleOwner = this
    binding.viewmodel = viewModel
    return binding.root
}
```

### Sử dụng trong RecyclerView

- Hiển thị 1 textView mỗi item

- list_item_my_adapter.xml

```xml
<data>
    <variable
        name="value"
        type="String" />
</data>
```

- Adapter

```java
class MyAdapter(private val myDataset: Array<String>) :
    RecyclerView.Adapter<MyAdapter.MyViewHolder>() {

    class MyViewHolder(v: View) : RecyclerView.ViewHolder(v) {
        //var textView: TextView = v.findViewById(R.id.textView)
        // ======= 1 =======
        val binding: ListItemMyAdapterBinding? = DataBindingUtil.bind(v)
    }

    override fun onCreateViewHolder(parent: ViewGroup,
                                    viewType: Int): MyViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.list_item_my_adapter, parent, false)
        return MyViewHolder(view)
    }

    override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
        // holder.textView.text = myDataset[position]
        // ======= 2 ==========
        holder.binding?.value = myDataset[position]
    }

    override fun getItemCount() = myDataset.size
}
```

## 1 số thao tác trong View

```xml
android:text="@{String.valueOf(index + 1)}"
android:visibility="@{age < 13 ? View.GONE : View.VISIBLE}"
android:transitionName='@{"image_" + id}'
```

## BindingAdapter: Tạo thuộc tính mới cho View

```json
//build.gradle(app)
apply plugin: "kotlin-kapt"
```

```java
// Đặt trong file KT bất kì

/**Ẩn file nếu number < 0
**/
@BindingAdapter("app:hideIfZero")
fun hideIfZero(view: View, number: Int) {
    view.visibility = if (number <= 0) View.GONE else View.VISIBLE
}

// Sử dụng 2 thuộc tính
@BindingAdapter(value = ["app:progressScaled", "android:max"], requireAll = true)
fun setProgress(progressBar: ProgressBar, likes: Int, max: Int) {
    progressBar.progress = (likes * max / 5).coerceAtMost(max)
}
```

```xml
<!-- Sử dụng trong Layout -->
<ProgressBar
    app:hideIfZero="@{viewmodel.likes}"
    android:max="@{100}"
    app:progressScaled="@{viewmodel.likes}"/>
```

## Common Errors

- Type parameter bound for T in fun <T : ViewDataBinding!> inflate(p0: LayoutInflater, p1: Int, p2: ViewGroup?, p3: Boolean): T!
 is not satisfied: inferred type View? is not a subtype of ViewDataBinding!

---

## Tham khảo

- <https://codelabs.developers.google.com/codelabs/android-databinding/index.html>
