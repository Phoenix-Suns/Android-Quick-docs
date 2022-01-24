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

- Tạo khung cho Image (Vuông, Bo Tròn Circle, Tam giác)

```xml
<com.google.android.material.imageview.ShapeableImageView
    app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.App.CornerSize50Percent"
    app:strokeWidth="10dp"
    android:padding="10dp"
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

### Button

```xml
<style name="MyButton" parent="Widget.MaterialComponents.Button.OutlinedButton">
    <item name="shapeAppearanceOverlay">@style/MyShapeAppearance</item>
</style>
<style name="MyShapeAppearance">
    <item name="cornerFamily">rounded</item>
    <item name="cornerFamilyTopRight">cut</item>
    <item name="cornerFamilyBottomRight">cut</item>
    <item name="cornerSizeTopLeft">32dp</item>
    <item name="cornerSizeBottomLeft">32dp</item>
</style>
```

### Dời View 1 đoạn

```xml
android:translationY="8dp"
```

### Đổi hình cursor khi gõ chữ

```xml
android:textCursorDrawable="@drawable/ic_name"
```

## Tham khảo

- <https://howtodoandroid.com/shapeableimageview-material-components-android/>
- <https://stackoverflow.com/questions/42684717/rounded-corners-on-material-button>
