# Special RecyclerView

- [Special RecyclerView](#special-recyclerview)
  - [Slide](#slide)

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
