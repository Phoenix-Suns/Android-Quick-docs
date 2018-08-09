# Menu Resource - Menu chức năng

<!-- TOC -->

- [Menu Resource - Menu chức năng](#menu-resource---menu-chức-năng)
        - [Giải thích:](#giải-thích)
        - [Vị trí file:](#vị-trí-file)
        - [Lớp sử dụng:](#lớp-sử-dụng)
        - [Sử dụng:](#sử-dụng)
        - [Dùng trong Activity](#dùng-trong-activity)

<!-- /TOC -->

### Giải thích:

Menu trong android (Context menu, content menu, main menu, drawer menu)

### Vị trí file:

**res/menu/filename.xml**

### Lớp sử dụng:

[Menu](http://developer.android.com/reference/android/view/Menu.html)

### Sử dụng:

*   In Java: **R.menu.filename**
*   In XML: **@[package:]menu.filename**

Cú pháp:

```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
 <item android:id="@[+][package:]id/resource_name"
  android:title="string"
  android:titleCondensed="string"
  android:icon="@[package:]drawable/drawable_resource_name"
  android:onClick="method name"
  android:showAsAction=["ifRoom" | "never" | "withText" | "always" | "collapseActionView"]
  android:actionLayout="@[package:]layout/layout_resource_name" 
  android:actionViewClass="class name"
  android:actionProviderClass="class name"
  android:alphabeticShortcut="string"
  android:numericShortcut="string"
  android:checkable=["true" | "false"]
  android:visible=["true" | "false"]
  android:enabled=["true" | "false"]
  android:menuCategory=["container" | "system" | "secondary" | "alternative"]
  android:orderInCategory="integer" />

 <group android:id="@[+][package:]id/resource name"
  android:checkableBehavior=["none" | "all" | "single"]
  android:visible=["true" | "false"]
  android:enabled=["true" | "false"]
  android:menuCategory=["container" | "system" | "secondary" | "alternative"]
  android:orderInCategory="integer" >
  <item />
 </group>

 <item >
  <menu>
   <item />
  </menu>
 </item>
</menu>
```

**Thành phần:**

*   titleCondensed (cô đặc):  Tiêu đề rút gọn
*   showAsAction : cách hiển thị:
    *   ifRoom: hiện nếu còn chỗ
    *   withText: icon kèm title
    *   never: không hiện
    *   always: luôn hiện
    *   collapseActionView: hiện theo: android:actionLayout | android:actionViewClass
*   actionLayout: layout Resources
*   actionViewClass: lớp đổi View cho button. Ví dụ:

```java
// Khởi tạo Menu Chính trong Activity
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
    	getMenuInflater().inflate(R.menu.menu_main, menu);

    	MenuItem searchItem = menu.findItem(R.id.action_search);
    	SearchView searchView = (SearchView) MenuItemCompat.getActionView(searchItem);

    	// Configure the search info and add any event listeners...
    	// Chỉnh sửa Menu Properties

    	return super.onCreateOptionsMenu(menu);
    }
```

*   actionProviderClass: lớp hành động cho Button: Ví dụ: [ShareActionProvider](http://developer.android.com/reference/android/widget/ShareActionProvider.html)
*   alphabeticShortcut | numericShortcut : phím tắt
*   menuCategory
*   orderInCategory: thứ tự trên menu

### Dùng trong Activity

```java
// Khởi tạo Menu
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.activity_main, menu);
    return super.onCreateOptionsMenu(menu);
}

// Select on Menu
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.action_download: 
            // Action
        break;
    }

    return super.onOptionsItemSelected(item);
}
```

---
Tham khảo:
*   [http://developer.android.com/intl/vi/guide/topics/resources/menu-resource.html](http://developer.android.com/intl/vi/guide/topics/resources/menu-resource.html)

</div>