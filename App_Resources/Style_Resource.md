# Style resource – Kiểu, mẫu

<!-- TOC -->

- [Style resource – Kiểu, mẫu](#style-resource--kiểu-mẫu)
        - [Vị trí lưu trữ:](#vị-trí-lưu-trữ)
        - [Gọi xử dụng:](#gọi-xử-dụng)
        - [Cú pháp:](#cú-pháp)
        - [Ví dụ:](#ví-dụ)
        - [Sử dụng trong View:](#sử-dụng-trong-view)

<!-- /TOC -->

### Vị trí lưu trữ:

<div>**res/values/filename.xml**

### Gọi xử dụng:

*   In XML: **@[package:]style/style_name**

### Cú pháp:

```xml
<resources>
    <style name="style_name"
        parent="@[package:]style/style_to_inherit">
        <item name="[package:]style_property_name">
          style_value
        </item>
    </style>
</resources>
```

**Ghi chú:**

*   **parent:** Style cha, kế thừa lại
*   **name:** Tên Thuộc tính (ví dụ: android:layout_width)
*   **style_value:** Giá trị Thuộc tính (ví dụ: wrap_content)
*   **android:layout_width="wrap_content"** ==> <item  name="android:layout_width">wrap_content</item>
*   Chuyển Style từ View: **Menu Refactor/ Extract/ Style**

### Ví dụ:

```xml
<resources>
    <style name="CustomText" parent="@style/Text">
        <item name="android:textSize">20sp</item>
        <item name="android:textColor">#008</item>
    </style>
</resources>
```

### Sử dụng trong View:

```xml
<EditText
    style="@style/CustomText"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:text="Hello, World!" />
```


---
**Trích**

*   [http://developer.android.com/intl/vi/guide/topics/resources/style-resource.html](http://developer.android.com/intl/vi/guide/topics/resources/style-resource.html)s