# Android toolbar

## Simple Example

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

    </android.support.design.widget.AppBarLayout>

    <!--Body-->
    <android.support.constraint.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
```

---

## Reference
