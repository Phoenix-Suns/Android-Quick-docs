# BottomNavigationView

## Propeties

- app:labelVisibilityMode="labeled": Hiện tất cả label

## Simple Sample

```xml
<!-- Layout -->
<androidx.constraintlayout.widget.ConstraintLayout>

    <FrameLayout
        android:id="@+id/flTabContent"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintBottom_toTopOf="@+id/navigationView"
        app:layout_constraintTop_toTopOf="parent" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/navigationView"
        android:layout_width="match_parent"
        android:layout_height="@dimen/home_bottom_tab_height"
        android:background="#1e1e1e"
        app:itemIconTint="@color/selector_home_text"
        app:itemTextColor="@color/selector_home_text"
        app:labelVisibilityMode="labeled"
        app:layout_constraintBottom_toBottomOf="parent"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

```java
data class TabFragment(
    @StringRes val title: Int,
    @DrawableRes val icon: Int,
    val fragment: Fragment
) {
    var badgeNumber = 0
}
```

```java
// Activites
private List<TabFragment> tabList;

private void setUpBottomTabNavigation() {
    // Create tab header
    tabList = getTabList();
    navigationView.getMenu().clear();
    for (int i = 0; i < tabList.size(); i++) {
        TabFragment tabFrag = tabList.get(i);
        // menu id = index
        navigationView.getMenu().add(Menu.NONE, i, i, tabFrag.getTitle()).setIcon(tabFrag.getIcon());
    }

    // show fragment when click
    navigationView.setOnNavigationItemSelectedListener(new BottomNavigationView.OnNavigationItemSelectedListener() {
        @Override
        public boolean onNavigationItemSelected(@NonNull MenuItem item) {
            // show Fragment
            TabFragment tabInfo = tabList.get(item.getItemId());
            replaceFragment(tabInfo.getFragment());
            return true;
        }
    });
}

protected List<TabFragment> getTabList() {
    List<TabFragment> tabList = new ArrayList<>();
    tabList.add(new TabFragment(R.string.main_tab_home, R.drawable.selector_tab_home, new MemberHomeFragment()));
    tabList.add(new TabFragment(R.string.main_tab_wallet, R.drawable.selector_tab_wallet, new WalletFragment()));
    tabList.add(new TabFragment(R.string.main_tab_social, R.drawable.selector_tab_social, new SocialFragment()));
    return tabList;
}

private void replaceFragment(Fragment fragment) {
    FragmentTransaction transaction = getChildFragmentManager().beginTransaction();
    Fragment exitsFragment = fm.findFragmentById(R.id.flTabContent);
    if (exitsFragment == null)
        transaction.add(R.id.flTabContent, fragment);
    else
        transaction.replace(R.id.flTabContent, fragment);
    transaction.commit();
}

/** load fragment giữ lại stage
**/
private void replaceFragment(Fragment fragment) {
    // 1 ý tưởng khác:
    // hide fragment hiện tại
    // Tìm fragment trong FragmentManager
        // có: show lên
        // không có: add vào, show lên

    String tag = fragment.getClass().getName();
    FragmentManager fm = getChildFragmentManager();
    FragmentTransaction transaction = fm.beginTransaction();

    // Hide exist fragments
    for (Fragment frag : fm.getFragments()) {
        if (frag != null && frag.isVisible()) {
            transaction.hide(frag);
        }
    }

    // check frag exist
    Fragment exitsFragment = fm.findFragmentByTag(tag);
    if (exitsFragment != null && exitsFragment.getClass().getName().equals(fragment.getClass().getName())) {
        //show Fragment already exists
        transaction.show(fragment);
    } else {
        //Add Fragment
        transaction.add(R.id.flTabContent, fragment, tag);
    }

    transaction.commit();
}

private fun FragmentManager.replaceFragment(fragment: Fragment, @IdRes containerIdRes: Int) {
        beginTransaction().apply {
            if (fragment.isAdded) {
                show(fragment)
            } else {
                add(containerIdRes, fragment)
            }

            fragments.forEach {
                if (it != fragment && it.isAdded) {
                    hide(it)
                }
            }
        }.commit()
    }
```

## Create Badge

```xml
<!-- tab_header_main_badge.xml -->
<FrameLayout
    android:id="@+id/flBadgeContainer"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/tv_badge"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="top|center_horizontal"
        android:layout_marginLeft="10dp"
        android:layout_marginStart="10dp"
        android:background="@drawable/circle_red"
        android:gravity="center"
        android:textColor="@android:color/white"
        android:textSize="12sp"
        tools:text="99"/>
</FrameLayout>
```

```java
public void updateTabBadgeNumber(@StringRes int tabTitleStringRes, int badgeNumber) {
    int tabId = getMenuTabId(tabTitleStringRes);
    if (tabId == -1) return;

    try {
        // Find item view
        BottomNavigationMenuView navigationMenuView = (BottomNavigationMenuView) navigationView.getChildAt(0);
        View view = navigationMenuView.getChildAt(tabId);
        BottomNavigationItemView itemView = (BottomNavigationItemView) view;

        // Remove exist badge view
        View badgeContainerView = itemView.findViewById(R.id.flBadgeContainer);
        if (badgeContainerView != null) {
            itemView.removeView(badgeContainerView);
        }

        // add new badge view
        if (badgeNumber > 0) {
            View mainHeaderView = LayoutInflater.from(requireContext())
                    .inflate(R.layout.tab_header_main_badge, navigationMenuView, false);
            ((TextView) mainHeaderView.findViewById(R.id.tv_badge)).setText("" + badgeNumber);
            itemView.addView(mainHeaderView);
        }
    } catch (Exception exception) {
        Timber.e(exception);
    }
}

/**
    * @param tabTitleStringId
    * @return null = -1
    */
int getMenuTabId(@StringRes int tabTitleStringId) {
    for (int i = 0; i < tabList.size(); i++) {
        if (tabList.get(i).getTitle() == tabTitleStringId) {
            return i;
        }
    }
    return -1;
}
```
