# Data Binding: hiển thị giao diện XML theo Data

- [Data Binding: hiển thị giao diện XML theo Data](#data-binding-hi%e1%bb%83n-th%e1%bb%8b-giao-di%e1%bb%87n-xml-theo-data)
  - [Cài đặt](#c%c3%a0i-%c4%91%e1%ba%b7t)
  - [Sử dụng](#s%e1%bb%ad-d%e1%bb%a5ng)
    - [Sử dụng cơ bản](#s%e1%bb%ad-d%e1%bb%a5ng-c%c6%a1-b%e1%ba%a3n)
    - [Sử dụng với ViewModel](#s%e1%bb%ad-d%e1%bb%a5ng-v%e1%bb%9bi-viewmodel)
    - [Sử dụng trong Fragment](#s%e1%bb%ad-d%e1%bb%a5ng-trong-fragment)
    - [Sử dụng trong RecyclerView](#s%e1%bb%ad-d%e1%bb%a5ng-trong-recyclerview)
  - [1 số thao tác trong View](#1-s%e1%bb%91-thao-t%c3%a1c-trong-view)
  - [BindingAdapter: Tạo thuộc tính mới cho View](#bindingadapter-t%e1%ba%a1o-thu%e1%bb%99c-t%c3%adnh-m%e1%bb%9bi-cho-view)
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

### Sử dụng cơ bản

- Activity

```java
override fun onCreate() {
    // Thay
    setContentView(R.layout.plain_activity)
    // Bằng
    val binding : PlainActivityBinding =
        DataBindingUtil.setContentView(this, R.layout.plain_activity)

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

### Sử dụng với ViewModel

- Activity

```java
override fun onCreate() {
    val binding : PlainActivityBinding =
        DataBindingUtil.setContentView(this, R.layout.plain_activity)

    // Đặt dữ liệu
    binding.viewmodel = viewModel
}
```

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
fun onCreateView(inflater: LayoutInflater, container: ViewGroup) {
    val binding = DataBindingUtil.inflate(inflater, R.layout.plain_activity, container, false)
    //...
}
```

### Sử dụng trong RecyclerView

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

---

## Tham khảo

- <https://codelabs.developers.google.com/codelabs/android-databinding/index.html>
