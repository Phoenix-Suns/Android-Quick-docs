# Các ghi chú trong quá trình làm việc

## Action bar trong Fragment

```java
onCreate(...) {
    ...
    setHasOptionsMenu(true);
}

onCreateView(...) {
    ...
    View rootView = inflater.inflate(R.layout.fragment_layout, container, false);

    mToolbar = rootView.findViewById(R.id.toolbar_Key);
    mToolbar.setTitle(R.string.title_key);
    ((AppCompatActivity)getActivity()).setSupportActionBar(mToolbar);
}

```

## Thanh bar

```xml
<android.support.design.widget.CoordinatorLayout...>
    <!--Header, toolbar, tab-->
    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <!--Toolbar/actionbar-->
        <!--ẩn khi Kéo lên: app:layout_scrollFlags="scroll|enterAlways" -->
        <android.support.v7.widget.Toolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_scrollFlags="scroll|enterAlways"/>

    <!--Body-->
    <android.support.constraint.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
```

## Chỉnh lại kích thước ImageView

```<ImageView android:adjustViewBounds="true"/>```


