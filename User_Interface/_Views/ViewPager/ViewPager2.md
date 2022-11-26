# View Pager (Tab kéo qua lại)

- [View Pager (Tab kéo qua lại)](#view-pager-tab-kéo-qua-lại)
  - [So sánh với ViewPager2 vs ViewPager](#so-sánh-với-viewpager2-vs-viewpager)
  - [ViewPager2 với TabLayout](#viewpager2-với-tablayout)
  - [ViewPager2 với Buttons cố định](#viewpager2-với-buttons-cố-định)
  - [Reference](#reference)

## So sánh với ViewPager2 vs ViewPager

- Hỗ trợ RecyclerView
- DiffUtil (thay đổi không cần refresh dữ liệu)
- Hỗ trợ RTL (Right to left layout)
- Vertical Orientation (Hỗ trợ vuốt layout kiểu dọc)
- Nhiều kiểu layout
- Adapter như RecyclerView
- Pager snap

## ViewPager2 với TabLayout

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
private var firstFragment: FirstFragment? = null
private var secondFragment: SecondFragment? = null
private var fragArr : Array<Fragment> = arrayOf()
private var titleArr : Array<String> = arrayOf()
private var mainPagerAdapter: MainPagerAdapter? = null

override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    initTab()
}

private fun initTab() {
    firstFragment = FirstFragment.newInstance()
    secondFragment = SecondFragment.newInstance()
    fragArr = arrayOf(firstFragment!!, secondFragment!!)

    titleArr = arrayOf(
        resources.getString(R.string.my_profile_my_dance_fights),
        resources.getString(R.string.my_profile_my_dances)
    )

    mainPagerAdapter = MainPagerAdapter(this)
    binding.viewPager.offscreenPageLimit = 2
    binding.viewPager.adapter = mainPagerAdapter
    // Set Selected background
    binding.viewPager.registerOnPageChangeCallback(object : ViewPager2.OnPageChangeCallback() {
        override fun onPageSelected(position: Int) {
            super.onPageSelected(position)

            setSelectedTab(position)
        }
    })

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
    // Match parent root
    // itemView.layoutParams = ViewGroup.LayoutParams(
    //     ViewGroup.LayoutParams.MATCH_PARENT,
    //     ViewGroup.LayoutParams.MATCH_PARENT
    // )
    val tv = itemView.findViewById<TextView>(R.id.tvTitle)
    tv.text = titleArr[position]
    return itemView
}

// OR
// private fun getTabView(position: Int): View {
//     val tabBinding: TabHeaderGoldPriceTypeBinding = DataBindingUtil.inflate(
//         LayoutInflater.from(requireContext()),
//         R.layout.tab_header_my_profile,
//         null,
//         false
//     )
// Match parent root
        // tabBinding.root.layoutParams = ViewGroup.LayoutParams(
        //     ViewGroup.LayoutParams.MATCH_PARENT,
        //     ViewGroup.LayoutParams.MATCH_PARENT
        // )
//     tabBinding.tvTitle.text = titleArr[position]
//     return tabBinding.root
// }

private fun setSelectedTab(position: Int) {
    // Clear selected tab
    for (i in 0 until binding.tabLayout.tabCount) {
        binding.tabLayout.getTabAt(i)?.customView?.apply {
            val cvSelected = this@apply.findViewById<View>(R.id.cardViewSelected)
            cvSelected.gone()
        }
    }

    // Selected tab
    binding.tabLayout.getTabAt(position)?.customView?.apply {
        val cvSelected = this@apply.findViewById<View>(R.id.cardViewSelected)
        cvSelected.visible()
    }
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

## ViewPager2 với Buttons cố định

```java

override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    initTab()

    binding.buttonFirst.onClick = {
        binding.viewPager.currentItem = 0
    }

    binding.buttonSecond.onClick = {
        binding.viewPager.currentItem = 1
    }
}

private fun initTab() {
    firstFragment = FirstFragment.newInstance()
    secondFragment = SecondFragment.newInstance()
    fragArr = arrayOf(firstFragment!!, secondFragment!!)

    mainPagerAdapter = MainPagerAdapter(this)
    binding.viewPager.offscreenPageLimit = 2
    binding.viewPager.adapter = mainPagerAdapter
    binding.viewPager.registerOnPageChangeCallback(object : ViewPager2.OnPageChangeCallback() {
        override fun onPageSelected(position: Int) {
            super.onPageSelected(position)

            val selectedView = when (position) {
                0 -> binding.buttonFirst.root
                1 -> binding.buttonSecond.root
                else -> binding.buttonFirst.root
            }
            setBottomButtonSelect(binding.bottomNavigation, selectedView)
        }
    })
}

private fun setBottomButtonSelect(container: ViewGroup, selectedView: View) {
    for (view in container.children) {
        view.isSelected = false
    }
    selectedView.isSelected = true
}
```

---

## Reference

- <https://developer.android.com/training/animation/vp2-migration>
