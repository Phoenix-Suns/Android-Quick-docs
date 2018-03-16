# String Resource - Tài nguyên chữ, kí tự, văn bản sử dụng

<!-- TOC -->

- [String Resource - Tài nguyên chữ, kí tự, văn bản sử dụng](#string-resource---tài-nguyên-chữ-kí-tự-văn-bản-sử-dụng)
        - [Vị trí file:](#vị-trí-file)
        - [Lớp:](#lớp)
        - [Sử dụng:](#sử-dụng)
        - [Cú pháp:](#cú-pháp)
        - [Kèm tham số](#kèm-tham-số)
        - [String array](#string-array)
        - [Quantity Strings (Plurals) - String dạng số lượng:](#quantity-strings-plurals---string-dạng-số-lượng)
        - [HTML markup - định dạng html](#html-markup---định-dạng-html)
        - [Styling with Spannables - Kiểu mở rộng](#styling-with-spannables---kiểu-mở-rộng)

<!-- /TOC -->

### Vị trí file:

**res/values/filename.xml**

### Lớp:
[http://developer.android.com/reference/java/lang/String.html](http://developer.android.com/reference/java/lang/String.html)

### Sử dụng:

*   In Java: **R.string.string_name**
*   In XML**: @string/string_name**

### Cú pháp:

```java
text_string

// java:
String string = getString(R.string.hello);
```

### Kèm tham số

```java
Hello, %1$s! You have %2$d new messages.
This is a \"good string\".

// java
String text = String.format(res.getString(R.string.welcome_messages), username, mailCount);
```

### String array

```xml
<resources>
 <string-array name="string_array_name">
  <item>text_string</item>
  <item>text_string</item>
  <item>text_string</item>
  ...
 </string-array>
</resources>

// java:
String[] planets = getStringArray(R.array.planets_array);
```

### Quantity Strings (Plurals) - String dạng số lượng:

```xml
<resources>
 <plurals name="plural_name">
  <item quantity=["zero" | "one" | "two" | "few" | "many" | "other"]>
   text_string
  </item>
 </plurals>
</resources>

<!--Ví dụ-->

<resources>
 <plurals name="numberOfSongsAvailable">
  <item quantity="one">Znaleziono %d piosenkę.</item>
  <item quantity="few">Znaleziono %d piosenki.</item>
  <item quantity="other">Znaleziono %d piosenek.</item>
 </plurals>
</resources>

// java
int count = getNumberOfsongsAvailable();
Resources res = getResources();
String songsFound = res.getQuantityString(R.plurals.numberOfSongsAvailable, count, count);
```

### HTML markup - định dạng html

*   b: in đậm
*   i: in nghiêng
*   u: gạch chân
```xml
<string name="welcome_messages">Welcome to <b>Android</b>!</string>

// java
Resources res = getResources();
String text = String.format(res.getString(R.string.welcome_messages), username, mailCount);
CharSequence styledText = Html.fromHtml(text);
```

### Styling with Spannables - Kiểu mở rộng

*   Chắc sẽ làm sau

---
Tham khảo:

* [http://developer.android.com/intl/vi/guide/topics/resources/string-resource.html#StylingWithSpannables](http://developer.android.com/intl/vi/guide/topics/resources/string-resource.html#StylingWithSpannables)

</div>