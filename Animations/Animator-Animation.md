res/animator/filename.xml

## Fast animation

```java
view.animate()
    .translationY(-((root.height - (view.height)) / 2).toFloat())
    .setInterpolator(AccelerateInterpolator()).duration = 1500

// ===== Animate View directly
viewToAnimate.animate()
    .x(positionX)
    .setDuration(600L)
    .setInterpolator(defaultAnimationInterpolator)
    .start()

// ===== Object Animator
ObjectAnimator.ofFloat(
    viewToAnimate,
    View.X,
    positionX
).apply {
    duration = 600
    interpolator = defaultAnimationInterpolator
}.start()

// ===== Value Animator
ValueAnimator.ofFloat(viewToAnimate.x, positionX).apply {
    duration = 2000
    interpolator = defaultAnimationInterpolator
    addUpdateListener {
        viewToAnimate.x = it.animatedValue as Float
    }
    start()
}
```

