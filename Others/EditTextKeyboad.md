# Hiện key board ko đè EditText

```java
/**
 * Fragment Scale when Keyboard
 * Using:
 *
 *  override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
    super.onCreateView(inflater, container, savedInstanceState)

        // Fix keyboard scale
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
            activity?.window?.setDecorFitsSystemWindows(false)
            binding.root.addKeyboardVisibleListener { keyboardVisible ->
                val imeHeight = binding.root.rootWindowInsets?.getInsets(WindowInsets.Type.ime())?.bottom

                if (keyboardVisible) {
                    val navHeight =
                    binding.root.rootWindowInsets?.getInsets(WindowInsets.Type.navigationBars())?.bottom
                    binding.root.setPadding(0, 0, 0, (imeHeight ?: 0) - (navHeight ?: 0))
                } else {
                    binding.root.setPadding(0, 0, 0, (imeHeight ?: 0))
                }
        }
    }
    return binding.root
}

override fun onDestroyView() {
    // remove Fix keyboard scale
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q)
        activity?.window?.setDecorFitsSystemWindows(true)
}

private var originalSoftInputMode: Int? = null

override fun onResume() {
    // Fix keyboard scale
    if (Build.VERSION.SDK_INT < Build.VERSION_CODES.Q) {
        this.originalSoftInputMode = activity?.window?.attributes?.softInputMode
        activity?.window?.setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_RESIZE)
    }
    super.onResume()
}

override fun onPause() {
// remove Fix keyboard scale
    if (Build.VERSION.SDK_INT < Build.VERSION_CODES.Q)
        originalSoftInputMode?.let { activity?.window?.setSoftInputMode(it) }
    super.onPause()
}
 */
@RequiresApi(Build.VERSION_CODES.R)
fun View.addKeyboardVisibleListener(keyboardCallback: (keyboardVisible: Boolean) -> Unit) {
    doOnLayout {
        var keyboardVisible = rootWindowInsets?.isVisible(WindowInsets.Type.ime()) == true
        keyboardCallback(keyboardVisible)

        //whenever the layout resizes/changes, callback with the state of the keyboard.
        viewTreeObserver.addOnGlobalLayoutListener {
            val keyboardUpdateCheck = rootWindowInsets?.isVisible(WindowInsets.Type.ime()) == true
            //since the observer is hit quite often, only callback when there is a change.
            if (keyboardUpdateCheck != keyboardVisible) {
                keyboardCallback(keyboardUpdateCheck)
                keyboardVisible = keyboardUpdateCheck
            }
        }
    }
}
```

OR

```java
override fun onStart() {
    super.onStart()

    binding.root.setOnApplyWindowInsetsListener { _, windowInsets ->
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
            val imeHeight = windowInsets.getInsets(WindowInsets.Type.ime()).bottom
            requireView().setPadding(0, 0, 0, imeHeight)
            windowInsets
        } else
            windowInsets
    }
}

override fun onDestroyView() {
    activity?.window?.setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_PAN)
    super.onDestroyView()
}
```