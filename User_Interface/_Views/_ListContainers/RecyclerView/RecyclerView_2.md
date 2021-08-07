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
