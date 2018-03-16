# Drawable Resource - tài nguyên ảnh

<!-- TOC -->

- [Drawable Resource - tài nguyên ảnh](#drawable-resource---tài-nguyên-ảnh)
        - [Định nghĩa:](#định-nghĩa)
        - [Vị trí file: **res/drawable/filename.png**](#vị-trí-file-resdrawablefilenamepng)
        - [Gọi sử dụng:](#gọi-sử-dụng)
        - [Phân loại](#phân-loại)
            - [Bitmap File](#bitmap-file)
        - [Nine-Patch File](#nine-patch file)
        - [Layer List](#layer-list)
        - [State List](#state-list)
        - [Level List](#level-list)
        - [Transition Drawable](#transition-drawable)
        - [Inset Drawable](#inset-drawable)
        - [Clip Drawable](#clip-drawable)
        - [Scale Drawable](#scale-drawable)
        - [Shape Drawable](#shape-drawable)

<!-- /TOC -->

### Định nghĩa:

Là ngồn ảnh dùng trực tiếp trong Android.

### Vị trí file: **res/drawable/filename.png**

### Gọi sử dụng:

*   In Java: `R.drawable.filename`
    *   ví dụ:

```java
// Chuyển thành ảnh để sử dụng
Bitmap icon = BitmapFactory.decodeResource(getResources(),
R.drawable.ic_add_48dp);
BitmapDrawable bd = new BitmapDrawable(getResources(), icon);
// Chèn vào Image
imageView_WorkStatus.setImageDrawable(ContextCompat.getDrawable(mContext,
R.drawable.ic_add_48dp));
```

*   In XML: `@[_package_:]drawable/_filename_`
    *   Ví dụ: 
    ```android:src="@drawable/ic_outlet_32dp"```

### Phân loại

#### Bitmap File

*   Chứa file ảnh thuần (png, jpg, gif)
*   Hàm liên quan: [BitmapDrawable](http://developer.android.com/reference/android/graphics/drawable/BitmapDrawable.html)

### Nine-Patch File 

[![](http://developer.android.com/images/ninepatch_raw.png)](http://developer.android.com/images/ninepatch_raw.png)

*   Ảnh co giãn theo nội dung
*   File name: <span style="background-color: yellow;">filename.9.png
*   Hàm liên quan: [NinePatchDrawable](http://developer.android.com/reference/android/graphics/drawable/NinePatchDrawable.html)
*   Stretchable: Vùng giãn nở.
*   PaddingBox: Vùng chứa dữ liệu (padding có thể thay đổi = code)
*   9 patch XML:

```xml
<nine-patch
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:src="@[package:]drawable/drawable_resource"
    android:dither=["true" | "false"] />

```

### Layer List 

[![](http://developer.android.com/images/resources/layers.png)](http://developer.android.com/images/resources/layers.png)

*   Chèn Nhiều hình ảnh, thành 1 ảnh
*   Hàm liên quan: [LayerDrawable](http://developer.android.com/reference/android/graphics/drawable/LayerDrawable.html)
*   Cú pháp:

```xml
<layer-list
    xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:drawable="@[package:]drawable/drawable_resource"
        android:id="@[+][package:]id/resource_name"
        android:top="dimension"
        android:right="dimension"
        android:bottom="dimension"
        android:left="dimension" />
</layer-list>
```

### State List

[![](http://developer.android.com/design/media/touch_feedback_states.png)](http://developer.android.com/design/media/touch_feedback_states.png)

*   Ảnh theo trạng thái (hover, press)
*   Hàm liên quan:  `[StateListDrawable](http://developer.android.com/reference/android/graphics/drawable/StateListDrawable.html)`
*   Cú pháp:

```xml
<selector xmlns:android="http://schemas.android.com/apk/res/android"
     android:constantSize=["true" | "false"]
     android:dither=["true" | "false"]
     android:variablePadding=["true" | "false"] >
     <item
        android:drawable="@[package:]drawable/drawable_resource"
        android:state_pressed=["true" | "false"]
        android:state_focused=["true" | "false"]
        android:state_hovered=["true" | "false"]
        android:state_selected=["true" | "false"]
        android:state_checkable=["true" | "false"]
        android:state_checked=["true" | "false"]
        android:state_enabled=["true" | "false"]
        android:state_activated=["true" | "false"]
        android:state_window_focused=["true" | "false"] />
    </selector>
```

### Level List

*   Ảnh theo số (ví dụ: set số 1 => ảnh 1, số 2 => ảnh 2)
*   Hàm liên quan: `[LevelListDrawable](http://developer.android.com/reference/android/graphics/drawable/LevelListDrawable.html)`
*   Cú pháp:

```xml
<level-list xmlns:android="http://schemas.android.com/apk/res/android" >
        <item
        android:drawable="@drawable/status_off"
        android:maxLevel="0" />
        <item
        android:drawable="@drawable/status_on"
        android:maxLevel="1" />
    </level-list>
```

*   Sử dụng trong Java: đặt background View, đổi Level bằng hàm **setLevel()** hay **setImageLevel()**

### Transition Drawable

*   Đổi 2 ảnh qua lại (kiểu cross-fade mờ dần)
*   Hàm: `[TransitionDrawable](http://developer.android.com/reference/android/graphics/drawable/TransitionDrawable.html)`
*   Cú pháp:

```xml
<transition
    xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:drawable="@[package:]drawable/drawable_resource"
        android:id="@[+][package:]id/resource_name"
        android:top="dimension"
        android:right="dimension"
        android:bottom="dimension"
        android:left="dimension" />
</transition>
```

*   Trong đó: android:topInteger - The top offset in pixels.
*   Áp dụng:
    *   XML:

```xml
<transition xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/on" />
    <item android:drawable="@drawable/off" />
    </transition>

    <ImageButton
        android:id="@+id/button"
        android:layout_height="wrap_content"
        android:layout_width="wrap_content"
        android:src="@drawable/transition" />
```

*   Java: `TransitionDrawable drawable = (TransitionDrawable) button.getDrawable(); drawable.startTransition(500);`

### Inset Drawable

*   Định nghĩa ảnh ở trong ảnh. Dùng khi dùng ảnh nền mà ảnh thật nhỏ hơn ảnh nền (làm ảnh to ra thêm tí :D).
*   Cú pháp:

```xml
<inset
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/drawable_resource"
    android:insetTop="dimension"
    android:insetRight="dimension"
    android:insetBottom="dimension"
    android:insetLeft="dimension" />
```

### Clip Drawable

[![](http://developer.android.com/images/resources/clip.png)](http://developer.android.com/images/resources/clip.png)

*   Định nghĩa ảnh, là 1 phần của ảnh khác (bị cắt ra)
*   Hàm: [ClipDrawable](http://developer.android.com/reference/android/graphics/drawable/ClipDrawable.html)
*   Cú pháp:

```xml
<clip
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/drawable_resource"
    android:clipOrientation=["horizontal" | "vertical"]
    android:gravity=["top" | "bottom" | "left" | "right" | "center_vertical" |
    "fill_vertical" | "center_horizontal" | "fill_horizontal" |
    "center" | "fill" | "clip_vertical" | "clip_horizontal"] />
```

*   Java sử dụng:

```java
// Level từ 0 -> 10.000 (biến mất hình)
ClipDrawable drawable = (ClipDrawable) imageview.getDrawable();
drawable.setLevel(drawable.getLevel() + 1000);
```

### Scale Drawable

*   Định nghĩa ảnh, đổi size ảnh khác dựa vào giá trị level.
*   Hàm: `[ScaleDrawable](http://developer.android.com/reference/android/graphics/drawable/ScaleDrawable.html)`
*   Cú pháp:

```xml
<scale
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/drawable_resource"
    android:scaleGravity=["top" | "bottom" | "left" | "right" | "center_vertical" |
    "fill_vertical" | "center_horizontal" | "fill_horizontal" |
    "center" | "fill" | "clip_vertical" | "clip_horizontal"]
    android:scaleHeight="percentage"
    android:scaleWidth="percentage" />
```

*   Thành phần: x

### Shape Drawable

*   Tạo ảnh qua các đường = xml, màu, gradients.
*   Hàm: `[ShapeDrawable](http://developer.android.com/reference/android/graphics/drawable/ShapeDrawable.html)`
*   Cú pháp:

```xml
<shape
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape=["rectangle" | "oval" | "line" | "ring"] >
    <corners
        android:radius="integer"
        android:topLeftRadius="integer"
        android:topRightRadius="integer"
        android:bottomLeftRadius="integer"
        android:bottomRightRadius="integer" />
    <gradient
        android:angle="integer"
        android:centerX="integer"
        android:centerY="integer"
        android:centerColor="integer"
        android:endColor="color"
        android:gradientRadius="integer"
        android:startColor="color"
        android:type=["linear" | "radial" | "sweep"]
        android:useLevel=["true" | "false"] />
    <padding
        android:left="integer"
        android:top="integer"
        android:right="integer"
        android:bottom="integer" />
    <size
        android:width="integer"
        android:height="integer" />
    <solid
        android:color="color" />
    <stroke
        android:width="integer"
        android:color="color"
        android:dashWidth="integer"
        android:dashGap="integer" />
</shape>
    
```

*   Thành phần: x

---
**Tham khảo:** [http://developer.android.com/guide/topics/resources/drawable-resource.html#Transition](http://developer.android.com/guide/topics/resources/drawable-resource.html#Transition)