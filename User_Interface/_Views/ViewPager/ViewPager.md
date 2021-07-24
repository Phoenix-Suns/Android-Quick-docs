# ViewPager

list trượt qua trượt lại

- [ViewPager](#viewpager)
  - [Simple Pager adapter](#simple-pager-adapter)
  - [Tips](#tips)
    - [show multiple Page](#show-multiple-page)
    - [Set item center](#set-item-center)
    - [Lấy giá trị tại vị trí](#lấy-giá-trị-tại-vị-trí)
    - [Re-create fragment when notifyDataSetChanged](#re-create-fragment-when-notifydatasetchanged)
  - [Reference](#reference)

## Simple Pager adapter

```xml
<android.support.v4.view.ViewPager
                android:id="@+id/viewpager_credits"
                android:layout_width="match_parent"
                android:layout_height="match_parent"/>
```

```java
// init on Activity
CreditPagerAdapter creditPagerAdapter = new CreditPagerAdapter(getContext());
viewPagerCredits.setAdapter(creditPagerAdapter);

// Class
class CreditPagerAdapter extends PagerAdapter {
    private Context context;
    private LayoutInflater layoutInflater;
    private ArrayList<Item> itemList = new ArrayList<>();

    public CreditPagerAdapter(Context context) {
        this.context = context;
        this.layoutInflater = LayoutInflater.from(context);
    }

    @Override
    public int getCount() {
        return itemList.size();
    }

    @Override
    public boolean isViewFromObject(@NonNull View view, @NonNull Object object) {
        return view == object;
    }

    @Override
    public int getItemPosition(@NonNull Object object) {
        // it will recreate all Fragments when
        // notifyDataSetChanged is called
        return PagerAdapter.POSITION_NONE;
    }

    @Override
    public void destroyItem(@NonNull ViewGroup container, int position, @NonNull Object object) {
        if (object instanceof View) {
            container.removeView((View)object);
        }
    }

    //region Current Fragment
    @NonNull
    @Override
    public Object instantiateItem(@NonNull ViewGroup container, int position) {
        View itemView = layoutInflater.inflate(R.layout.item_layout, container, false);

        Item item = itemList.get(position);
        bindItem(itemView, item);

        // Add the page to the container
        container.addView(itemView);
        return itemView;
    }

    public Item getValueAt(int position) {
        return ItemList.get(position);
    }
    //endregion

    public void setData(List<Item> ItemList) {
        this.itemList.clear();
        this.itemList.addAll(ItemList);
        notifyDataSetChanged();
    }

    public void bindItem(View rootView, Item item) {
        TextView nameTv = rootView.findViewById(R.id.textview_name);
        nameTv.setText(item.getTitle());
    }
}
```

## Tips

### show multiple Page

```xml
<android.support.v4.view.ViewPager
    android:paddingStart="@dimen/space_16dp"
    android:paddingEnd="@dimen/space_16dp" />
```

```java
viewPagerCredits.setClipToPadding(false);
viewPagerCredits.setPageMargin(ConvertUtils.dpToPx(16)); // page margin

// adapter
@Override
public float getPageWidth(int position) {
    // show multiple item on screen
    return 0.9f;
}
```

### Set item center

```java
layoutViewPagerContainer.post(new Runnable() {
    @Override
    public void run() {
        int w = layoutViewPagerContainer.getWidth();
        viewPagerCredits.setPadding((int)(w * 0.25), 0, (int)(w * 0.25), 0);
        // 25% padding on either side so pages takes exactly 50% space
        // don't use with getPageWidth
    }
});
```

### Lấy giá trị tại vị trí

```java
pagerAdapter.getValueAt(viewPager.getCurrentItem());
fun getValueAt(position: Int): TopUpPackage {
    return items[position]
}
```

### Re-create fragment when notifyDataSetChanged

```java
// adapter
@Override
public int getItemPosition(@NonNull Object object) {
    // it will recreate all Fragments when
    // notifyDataSetChanged is called
    return PagerAdapter.POSITION_NONE;
}
```

---

## Reference

- <https://guides.codepath.com/android/ViewPager-with-FragmentPagerAdapter>
- <https://developer.android.com/training/animation/vp2-migration>
