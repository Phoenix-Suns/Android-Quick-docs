# Other Resource - tài nguyên khác

<!-- TOC -->

- [Other Resource - tài nguyên khác](#other-resource---tài-nguyên-khác)
        - [Vị trí lưu trữ:](#vị-trí-lưu-trữ)
        - [Bool: nguồn đúng sai - logical](#bool-nguồn-đúng-sai---logical)
        - [Color - màu sắc](#color---màu-sắc)
        - [Dimension - Kích cỡ, kích thước](#dimension---kích-cỡ-kích-thước)
        - [ID - Định danh](#id---định-danh)
        - [Integer - số](#integer---số)
        - [Integer Array - mảng số](#integer-array---mảng-số)
        - [Typed Array - Mảng của nhiều Resource](#typed-array---mảng-của-nhiều-resource)

<!-- /TOC -->

### Vị trí lưu trữ:

**res/values/filename.xml**

### Bool: nguồn đúng sai - logical

**Ví dụ:** XML file saved at res/values-small/bools.xml:

```xml
<resources>
    <bool name="screen_small">true</bool>
    <bool name="adjust_view_bounds">true</bool>
</resources>
```

Gọi trong java:

```java
Resources res = getResources();
boolean screenIsSmall = res.getBoolean(R.bool.screen_small);
```

Sử dụng trong XML layout view:

```xml
<imageview
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:adjustviewbounds="@bool/adjust_view_bounds"
    android:src="@drawable/logo"/>
```

### Color - màu sắc

**Định dạng:**

*   #RGB
*   #ARGB
*   #RRGGBB
*   #AARRGGBB

**Ví dụ:** XML file saved at res/values/colors.xml:

```xml
<resources>
    <color name="opaque_red">#f00</color>
    <color name="translucent_red">#80ff0000</color>
</resources>
```

Sử dụng trong Java:

```java
Resources res = getResources();
int color = res.getColor(R.color.opaque_red);
```

Sử dụng trong XML layout View:

```xml
<textview
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	android:text="Hello"
	android:textcolor="@color/translucent_red" />
```

### Dimension - Kích cỡ, kích thước

**Đơn vị:**

*   dp: Density-independent Pixels - Kích thước phụ thuộc thiết bị - hay sử dụng
*   sp: Scale-independent Pixels - giống dp - dùng cho font chữ
*   pt: point - 1/72 of an inch based on the physical size of the screen.
*   px: pixel
*   mm: milimet
*   in: inch

**Ví dụ:** XML file saved at res/values/dimens.xml:

```xml
<resources>
    <dimen name="textview_height">25dp</dimen>
    <dimen name="textview_width">150dp</dimen>
    <dimen name="ball_radius">30dp</dimen>
    <dimen name="font_size">16sp</dimen>
</resources>
```

Sử dụng trong Java:

```java
Resources res = getResources();
float fontSize = res.getDimension(R.dimen.font_size);
```

Sử dụng trong XML Layout View

```xml
<TextView
    android:layout_width="@dimen/textview_width"
    android:layout_height="@dimen/textview_height"
    android:textsize="@dimen/font_size" />
```

### ID - Định danh

Lưu định danh để sử dụng 
**Ví dụ:** XML file saved at res/values/ids.xml:

```xml
<resources>
    <item name="button_ok" type="id">
        <item name="dialog_exit" type="id">
        </item>
    </item>
</resources>
```

Then, this layout snippet uses the "button_ok" ID for a Button widget:

```xml
<button android:id="@id/button_ok"/>
```

### Integer - số

**Ví dụ:** XML file saved at res/values/integers.xml:

```xml
<resources>
    <integer name="max_speed">75</integer>
    <integer name="min_speed">5</integer>
</resources>
```

Sử dụng trong Java:

```java
Resources res = getResources();
int maxSpeed = res.getInteger(R.integer.max_speed);
```

### Integer Array - mảng số

**Ví dụ:** XML file saved at res/values/integers.xml:

```xml
<resources>
    <integer-array name="bits">
        <item>4</item>
        <item>8</item>
        <item>16</item>
        <item>32</item>
    </integer-array>
</resources>
```

Sử dụng trong Java

```java
Resources res = getResources();
int[] bits = res.getIntArray(R.array.bits);
```

### Typed Array - Mảng của nhiều Resource

**ví dụ:** XML file saved at res/values/arrays.xml:

```xml
<resources>
    <array name="icons">
        <item>@drawable/home</item>
        <item>@drawable/settings</item>
        <item>@drawable/logout</item>
    </array>
    <array name="colors">
        <item>#FFFF0000</item>
        <item>#FF00FF00</item>
        <item>#FF0000FF</item>
    </array>
</resources>
```

Gọi trong Java

```java
Resources res = getResources();
TypedArray icons = res.obtainTypedArray(R.array.icons);
Drawable drawable = icons.getDrawable(0);

TypedArray colors = res.obtainTypedArray(R.array.colors);
int color = colors.getColor(0,0);
```

---
**Trích**

*   [http://developer.android.com/intl/vi/guide/topics/resources/more-resources.html](http://developer.android.com/intl/vi/guide/topics/resources/more-resources.html)