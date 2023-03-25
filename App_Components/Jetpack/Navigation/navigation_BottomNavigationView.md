# Navigation Grap with BottomNavigationView

- [Navigation Grap with BottomNavigationView](#navigation-grap-with-bottomnavigationview)
  - [Setup](#setup)
  - [Main](#main)
    - [Layout](#layout)
    - [Activity](#activity)
    - [Menu](#menu)
    - [Navigation/nav_main](#navigationnav_main)
    - [Navigation/home or list or form](#navigationhome-or-list-or-form)
  - [Reference](#reference)

## Setup

```json
dependencies {
    implementation "androidx.navigation:navigation-fragment-ktx:2.4.0-alpha05"
    implementation "androidx.navigation:navigation-ui-ktx:2.4.0-alpha05"
}
```

## Main

### Layout

```xml
<!-- Layout -->
<androidx.constraintlayout.widget.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.appcompat.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="54dp"
        app:layout_constraintTop_toTopOf="parent" />

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/navHostContainer"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:name="androidx.navigation.fragment.NavHostFragment"
        app:defaultNavHost="true"
        app:navGraph="@navigation/nav_main"
        app:layout_constraintTop_toBottomOf="@+id/toolbar"
        app:layout_constraintBottom_toTopOf="@id/bottomNavigation"/>

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNavigation"
        android:layout_width="match_parent"
        android:layout_height="56dp"
        app:menu="@menu/menu_main"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

### Activity

```java
// MainActivity
override fun onCreate(savedInstanceState: Bundle?) {
    //...

    val navHostFragment = supportFragmentManager
        .findFragmentById(R.id.navHostContainer) as NavHostFragment
    navController = navHostFragment.navController

    // Setup the bottom navigation view with navController
    val bottomNavigationView = findViewById<BottomNavigationView>(R.id.bottomNavigation)
    bottomNavigationView.setupWithNavController(navController)

    // Setup the ActionBar with navController and 3 top level destinations
    appBarConfiguration = AppBarConfiguration(
        setOf(R.id.runningFragment, R.id.listFragment,  R.id.formFragment)
    )
    val toolbar = findViewById<Toolbar>(R.id.toolbar)
    setSupportActionBar(toolbar)
    toolbar.setupWithNavController(navController, appBarConfiguration)
}

override fun onSupportNavigateUp(): Boolean {
    return navController.navigateUp(appBarConfiguration)
}
```

### Menu

```xml
<!-- menu -->
<menu>
    <item
        android:id="@+id/home"
        android:icon="@drawable/ic_home_24"
        android:title="@string/home" />
    <item
        android:id="@+id/list"
        android:icon="@drawable/ic_list_bulleted_24"
        android:title="@string/list" />
    <item
        android:id="@+id/form"
        android:icon="@drawable/ic_form_24"
        android:title="@string/form" />
</menu>
```

### Navigation/nav_main

```xml
<navigation
    android:id="@+id/nav_main"
    app:startDestination="@+id/home">

    <include app:graph="@navigation/home" />
    <include app:graph="@navigation/list" />
    <include app:graph="@navigation/form" />

</navigation>
```

### Navigation/home or list or form

```xml
<navigation
    android:id="@+id/home"
    app:startDestination="@id/runningFragment">

    <fragment
        android:id="@+id/runningFragment"
        android:name="self.tranluunghia.englishscheduler.feature.test.RunningFragment"
        android:label="fragment_home"
        tools:layout="@layout/fragment_home" />
</navigation>
```

## Reference

- <https://github.com/android/architecture-components-samples>
