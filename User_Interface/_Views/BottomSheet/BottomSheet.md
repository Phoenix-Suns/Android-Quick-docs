# 1. Bottom Sheet - Dialog nhỏ, hiện bên dưới

**Tham Khảo:**
https://www.androidhive.info/2017/12/android-working-with-bottom-sheet/
https://material.io/develop/android/components/bottom-sheet-behavior/
https://developer.android.com/reference/android/support/design/widget/BottomSheetBehavior

<!-- TOC -->

- [1. Bottom Sheet - Dialog nhỏ, hiện bên dưới](#1-bottom-sheet---dialog-nh%e1%bb%8f-hi%e1%bb%87n-b%c3%aan-d%c6%b0%e1%bb%9bi)
  - [1.1. Setup](#11-setup)
  - [1.2. Bottom Sheet Trong giao diện chính](#12-bottom-sheet-trong-giao-di%e1%bb%87n-ch%c3%adnh)
    - [1.2.1. MainActivity.xml](#121-mainactivityxml)
    - [1.2.2. bottom_sheet.xml](#122-bottomsheetxml)
    - [1.2.3. MainActivity.java](#123-mainactivityjava)
    - [1.2.4. CallBack](#124-callback)
  - [1.3. Bottom Sheet Dialog](#13-bottom-sheet-dialog)
    - [1.3.1. BottomSheetFragment.java](#131-bottomsheetfragmentjava)
    - [1.3.2. MainActivity.java: Open Bottom Sheet](#132-mainactivityjava-open-bottom-sheet)
  - [Reference](#reference)

<!-- /TOC -->

## 1.1. Setup

```js
dependencies {
    ...
    implementation 'com.android.support:appcompat-v7:28.0.0'
    implementation 'com.android.support:design:28.0.0'
}
```

## 1.2. Bottom Sheet Trong giao diện chính

### 1.2.1. MainActivity.xml

```xml
<android.support.design.widget.CoordinatorLayout ...>

    <!-- ... -->
    <include layout="@layout/content_main" />
    <include layout="@layout/bottom_sheet" />

</android.support.design.widget.CoordinatorLayout>
```

### 1.2.2. bottom_sheet.xml

```xml
<LinearLayout ...
    android:id="@+id/bottom_sheet"
    app:behavior_hideable="true"
    app:behavior_peekHeight="56dp"
    app:layout_behavior="android.support.design.widget.BottomSheetBehavior">
    <!--...-->
</LinearLayout>
```

-Properties:
--behavior_peekHeight: Chiều cao hiện, không ẩn đi.

### 1.2.3. MainActivity.java

```java
// Setup
layoutBottomSheet = findViewById(R.id.bottom_sheet);
BottomSheetBehavior sheetBehavior = BottomSheetBehavior.from(layoutBottomSheet);

/** manually opening / closing bottom sheet on button click */
@OnClick(R.id.btn_bottom_sheet)
public void toggleBottomSheet() {
    if (sheetBehavior.getState() != BottomSheetBehavior.STATE_EXPANDED) {
        sheetBehavior.setState(BottomSheetBehavior.STATE_EXPANDED);
    } else {
        sheetBehavior.setState(BottomSheetBehavior.STATE_COLLAPSED);
    }
}
```

### 1.2.4. CallBack

```java
/**
* bottom sheet state change listener
* we are changing button text when sheet changed state
* */
sheetBehavior.setBottomSheetCallback(new BottomSheetBehavior.BottomSheetCallback() {
    @Override
    public void onStateChanged(@NonNull View bottomSheet, int newState) {
        switch (newState) {
            case BottomSheetBehavior.STATE_HIDDEN:
                break;
            case BottomSheetBehavior.STATE_EXPANDED: {
                btnBottomSheet.setText("Close");
            }
            break;
            case BottomSheetBehavior.STATE_COLLAPSED: {
                btnBottomSheet.setText("Expand");
            }
            break;
            case BottomSheetBehavior.STATE_DRAGGING:
                break;
            case BottomSheetBehavior.STATE_SETTLING:
                break;
        }
    }

    @Override
    public void onSlide(@NonNull View bottomSheet, float slideOffset) {

    }
});
```

## 1.3. Bottom Sheet Dialog

### 1.3.1. BottomSheetFragment.java

```java
public class BottomSheetFragment extends BottomSheetDialogFragment {
    public BottomSheetFragment() {
        // Required empty public constructor
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        return inflater.inflate(R.layout.fragment_bottom_sheet_dialog, container, false);
    }
}
```

### 1.3.2. MainActivity.java: Open Bottom Sheet

```java
/** showing bottom sheet dialog */
@OnClick(R.id.btn_bottom_sheet_dialog)
public void showBottomSheetDialog() {
    View view = getLayoutInflater().inflate(R.layout.fragment_bottom_sheet_dialog, null);

    BottomSheetDialog dialog = new BottomSheetDialog(this);
    dialog.setContentView(view);
    dialog.show();
}


/**
    * showing bottom sheet dialog fragment
    * same layout is used in both dialog and dialog fragment
    */
@OnClick(R.id.btn_bottom_sheet_dialog_fragment)
public void showBottomSheetDialogFragment() {
    BottomSheetFragment bottomSheetFragment = new BottomSheetFragment();
    bottomSheetFragment.show(getSupportFragmentManager(), bottomSheetFragment.getTag());
}
```

---

## Reference 

- https://stackoverflow.com/questions/35618998/how-to-implement-bottom-sheets-using-new-design-support-library-23-2
- https://developer.android.com/jetpack/androidx/migrate/artifact-mappings