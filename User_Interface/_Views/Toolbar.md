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

## Toolbar with background

```xml
<android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:fitsSystemWindows="true"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

        <!-- 
        Scroll on top to show: app:layout_scrollFlags="scroll|enterAlwaysCollapsed"
        Scroll on top to show apart: app:layout_scrollFlags="scroll|exitUntilCollapsed"
        -->
    <com.google.android.material.appbar.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:fitsSystemWindows="true"
            app:contentScrim="?attr/colorPrimary"
            app:expandedTitleMarginEnd="64dp"
            app:expandedTitleMarginStart="48dp"
            app:layout_scrollFlags="scroll|exitUntilCollapsed">

            <!-- app:layout_scrollFlags="scroll|enterAlways" 
            Scroll down to show
            -->
            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_scrollFlags="scroll|enterAlways">
            </androidx.appcompat.widget.Toolbar>

    </com.google.android.material.appbar.CollapsingToolbarLayout>
</android.support.design.widget.AppBarLayout>
```

## Tricks

```xml
<android.support.design.widget.AppBarLayout
    // shadow
    app:elevation="0dp"
    // Background
    android:background="@null" >


    <androidx.appcompat.widget.Toolbar
        // Remove start padding
        app:contentInsetStart="0dp" />
```

---

## Reference

- <https://guides.codepath.com/android/Handling-Scrolls-with-CoordinatorLayout#expanding-and-collapsing-toolbars>
