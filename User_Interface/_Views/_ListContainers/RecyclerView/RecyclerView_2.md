# Special RecyclerView

- [Special RecyclerView](#special-recyclerview)
  - [Slide](#slide)
  - [Endless Scroll (circular scroll)](#endless-scroll-circular-scroll)
  - [Kiểm tra đồng nhất Item (DiffUtil)](#kiểm-tra-đồng-nhất-item-diffutil)

## Slide

```java
sliderAdapter2 = SliderHorizontalAdapter2(requireContext(), sliderList, null, recyclerViewSlider)
val sliderLayout = LinearLayoutManager(activity, RecyclerView.HORIZONTAL, false)
recyclerViewSlider.adapter = sliderAdapter2
recyclerViewSlider.layoutManager = sliderLayout

// Slide
val snapHelper = PagerSnapHelper()
snapHelper.attachToRecyclerView(recyclerViewSlider)
```

## Endless Scroll (circular scroll)

```java
// Adapter
var isCircularScroll: Boolean = true

override fun getItemCount(): Int {
    return if (isCircularScroll)
        Int.MAX_VALUE
    else
        super.getItemCount()
}

override fun onBindViewHolder(holder: MomentViewHolder, position: Int) {
    val itemPosition = if (isCircularScroll)
        position % items.size
    else
        position
    super.onBindViewHolder(holder, itemPosition)
}
```

## Kiểm tra đồng nhất Item (DiffUtil)

- <https://www.raywenderlich.com/21954410-speed-up-your-android-recyclerview-using-diffutil>

```java
class DeckCollectionAdapter : ListAdapter<UserProfile, DeckCollectionAdapter.DeckCollectionViewHolder>(
    AsyncDifferConfig.Builder(DeckCollectionDiffCallback())
        .setBackgroundThreadExecutor(Executors.newSingleThreadExecutor())
        .build()
) {

    // notifyDataSetChanged()
    fun refresh(items: List<UserProfile>) {
        submitList(items.toList())  // Duplicate old list
    }

    internal class DeckCollectionDiffCallback : DiffUtil.ItemCallback<UserProfile>() {
        override fun areItemsTheSame(oldItem: UserProfile, newItem: UserProfile): Boolean {
            return oldItem.uid == newItem.uid
        }

        override fun areContentsTheSame(oldItem: UserProfile, newItem: UserProfile): Boolean {
            return oldItem == newItem
        }

    }

}
```