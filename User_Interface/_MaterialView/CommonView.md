# Material View Chuẩn

## MaterialButton

```xml
<com.google.android.material.button.MaterialButton
    app:backgroundTint="#fff"
    app:cornerRadius="8dp"
    app:rippleColor="#3F51B5"
    app:strokeColor="#5fed06"
    app:strokeWidth="2dp" />
```

- Màu: backgroundTint
- Màu khi ấn: rippleColor
- Viền: stroke

## Shape image

- Tạo khung cho Image (Vuông, Tròn, Tam giác)

```xml
<com.google.android.material.imageview.ShapeableImageView
    app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.App.CornerSize50Percent"
    app:strokeWidth="10dp"
    app:strokeColor="@android:color/darker_gray"
/>

<!-- Style -->
<style name="ShapeAppearanceOverlay.App.CornerSize50Percent" parent="">
    <item name="cornerSize">50%</item>
</style>
```

- cornerSize
- cornerFamily (rounded, cut) — Loại góc (bằng, tròn)
(cornerFamilyTopLeft, cornerFamilyTopRight, cornerFamilyBottomRight, cornerFamilyBottomLeft)
- cornerSize — corner size to be used for all four corners (cornerSizeTopLeft, cornerSizeTopRight, cornerSizeBottomRight, cornerSizeBottomLeft)

- <https://howtodoandroid.com/shapeableimageview-material-components-android/>


