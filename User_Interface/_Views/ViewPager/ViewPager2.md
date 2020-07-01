# View Pager (Tab kéo qua lại)

## Simple start

```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    app:layout_behavior="@string/appbar_scrolling_view_behavior">

    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginStart="36dp"
        android:paddingTop="@dimen/space_8"
        android:layout_marginEnd="36dp"
        android:background="@color/colorTransparent"
        app:tabIndicatorColor="@color/colorWhite"
        app:tabIndicatorFullWidth="true"
        app:tabIndicatorHeight="3dp">
        <com.google.android.material.tabs.TabItem
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout="@layout/tab_header_my_profile"/>
    </com.google.android.material.tabs.TabLayout>

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />

</LinearLayout>
```

```java
private var myDanceFightsFrag: MyDanceFightsFragment? = null
private var myDancesFrag: MyDancesFragment? = null
private var fragArr : Array<Fragment> = arrayOf()
private var titleArr : Array<String> = arrayOf()
private var mainPagerAdapter: MainPagerAdapter? = null

override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    initTab()
}

private fun initTab() {
    myDanceFightsFrag = MyDanceFightsFragment.newInstance()
    myDancesFrag = MyDancesFragment.newInstance()
    fragArr = arrayOf(myDanceFightsFrag!!, myDancesFrag!!)

    titleArr = arrayOf(
        resources.getString(R.string.my_profile_my_dance_fights),
        resources.getString(R.string.my_profile_my_dances)
    )

    mainPagerAdapter = MainPagerAdapter(this)
    binding.viewPager.offscreenPageLimit = 2
    binding.viewPager.adapter = mainPagerAdapter

    TabLayoutMediator(binding.tabLayout, binding.viewPager) { tab, position ->
        tab.text = titleArr[position]
    }.attach()

    // Set Custom Header All Tabs
    for (i in 0 until binding.tabLayout.getTabCount()) {
        val tab: TabLayout.Tab? = binding.tabLayout.getTabAt(i)
        tab?.customView = getTabView(i)
    }
}

private fun getTabView(position: Int): View? {
    val itemView: View = LayoutInflater.from(context).inflate(R.layout.tab_header_my_profile, null)
    val tv = itemView.findViewById<TextView>(R.id.tvTitle)
    tv.text = titleArr[position]
    return itemView
}

inner class MainPagerAdapter(fragmentContainer: Fragment) : FragmentStateAdapter(fragmentContainer) {

    override fun getItemCount(): Int {
        return fragArr.size
    }

    override fun createFragment(position: Int): Fragment {
        return fragArr[position]
    }
}
```