# MENU

Các tác vụ, chức năng hiển thị theo ngữ cảnh.

<!-- TOC -->

- [MENU](#menu)
    - [Tạo file menu](#tạo-file-menu)
        - [Tạo bằng file: menu/menu_main.xml](#tạo-bằng-file-menumenu_mainxml)
    - [Popup Menu, Tạo Menu hiện dưới Button1](#popup-menu-tạo-menu-hiện-dưới-button1)
        - [Hiển thị Popupmenu](#hiển-thị-popupmenu)
        - [Đổi nền PopupMenu](#đổi-nền-popupmenu)
    - [OptionMenu, Tạo MainMenu cho Activity](#optionmenu-tạo-mainmenu-cho-activity)
        - [Tạo MainMenu bằng code](#tạo-mainmenu-bằng-code)
    - [Menu hiện bên dưới Activity: chỉnh trong manifesh.xml](#menu-hiện-bên-dưới-activity-chỉnh-trong-manifeshxml)
    - [ContentMenu, tạo Menu khi nhấn giữ Button2](#contentmenu-tạo-menu-khi-nhấn-giữ-button2)

<!-- /TOC -->

## Tạo file menu

### Tạo bằng file: menu/menu_main.xml

```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:tools="http://schemas.android.com/tools"
      tools:context="com.imark.nghia.nghiamenu.MainActivity">
    <item android:id="@+id/action_settings"
        android:title="@string/action_settings"
        android:orderInCategory="100"
        android:showAsAction="ifRoom"
		android:icon="@android:drawable/sym_action_call"/>
	<item android:title="Add 1" />
	<item android:title="Add 2" />
		
    <!-- menu group -->
    <group android:id="@+id/group_menu_delete">
        <item android:id="@+id/action_delete_1"
              android:title="Delete 1" />
        <item android:id="@+id/action_delete_2"
              android:title="Delete 2" />
    </group>
	
    <!-- "file" submenu -->
    <item android:id="@+id/file"
          android:title="Menu phụ..." >
        <menu>
            <item android:id="@+id/action_create"
                  android:title="Tạo mới" />
            <item android:id="@+id/action_open"
                  android:title="Mở" />
        </menu>
    </item>
</menu>
```

**Thuộc tính:**
•	android:id="@+id/action_settings"
•	android:title="@string/action_settings"
•	android:icon="@android:drawable/sym_action_call"
•	android:orderInCategory="100": thứ tự Item trong Menu.
•	android:showAsAction= ["ifRoom" | "never" | "withText" | "always" | "collapseActionView"]: cách thức hiển thị: nếu có chỗ, ko hiện, với title, luôn hiện.
•	https://developer.android.com/guide/topics/resources/menu-resource.html

## Popup Menu, Tạo Menu hiện dưới Button1

![popup_menu](https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/menu_basic_2.jpg)

### Hiển thị Popupmenu

```java
button1.setOnClickListener(new View.OnClickListener() {
	@Override
    public void onClick(View v) {
        //----- Show Popup menu-----
        PopupMenu popup = new PopupMenu(getBaseContext(), v);
        MenuInflater inflater = popup.getMenuInflater();
				inflater.inflate(R.menu.menu_main, popup.getMenu());
        popup.show();
    }
});
```

### Đổi nền PopupMenu

Tạo style: value/styles.xml
```xml
<style name="AppTheme">
    <item name="android:itemBackground">@android:color/darker_gray</item>	
</style>
```

Áp dụng style này trong AndroidManifesh.xml

```xml
<manifest …> … <application … android:theme="@style/AppTheme" > …
```

## OptionMenu, Tạo MainMenu cho Activity

![popup_menu](https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/menu_basic_3.jpg)

```java
/**Menu của Activity */
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    // Hiển thị Menu,  Inflate the menu; this adds items to the action bar if it is present.
    getMenuInflater().inflate(R.menu.menu_main, menu);
    return true;
}

/** Chọn Item trong Activity Menu */
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    // Kiểm tra ID và Id Item.
	//Dùng switch
	/*switch (item.getItemId()) {
            case 111:
                // chọn menu 1
                Toast.makeText(getApplicationContext(), "Nghia", Toast.LENGTH_SHORT).show();
                return true;
            default:
                return super.onOptionsItemSelected(item);
     }*/
	
    int id = item.getItemId();
    if (id == R.id.action_settings) {
		//chọn menu settings
        Toast.makeText(this, "Action Menu", Toast.LENGTH_SHORT).show();
        return true;
    }

    return super.onOptionsItemSelected(item);
}
```

### Tạo MainMenu bằng code

```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
	// Tạo Items
    menu.add(0, 111, 0, "Menu 1");
    menu.add(0, 112, 1, "Menu 2");

    // Tạo menu con
    SubMenu subMenu3 = menu.addSubMenu(0, 113, 2, "Menu 3");
    subMenu3.add(0, 1131, 0, "Sub 3 1");
    subMenu3.add(0, 1132, 2, "Sub 3 2");
    subMenu3.add(0, 1133, 3, "Sub 3 3");
    return true;
}
```

## Menu hiện bên dưới Activity: chỉnh trong manifesh.xml

```xml
<activity ... >
    <!-- To support below API Level 14 -->
    <meta-data android:name="android.support.UI_OPTIONS"
           android:value="splitActionBarWhenNarrow" />
</activity>
```

## ContentMenu, tạo Menu khi nhấn giữ Button2

![popup_menu](https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/menu_basic_1.jpg)

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    //...
    Button button2 = (Button) findViewById(R.id.button);
    registerForContextMenu(button2); // long click hiện menu
}

/** Menu ngữ cảnh */
@Override
public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
    super.onCreateContextMenu(menu, v, menuInfo);
    MenuInflater inflater = getMenuInflater();
		inflater.inflate(R.menu.menu_main, menu);
}

/** Chọn trong Menu Ngữ cảnh */
@Override
public boolean onContextItemSelected(MenuItem item) {
    AdapterView.AdapterContextMenuInfo info = (AdapterView.AdapterContextMenuInfo) item.getMenuInfo();
    switch (item.getItemId()) {
        case R.id.action_add:
			//Chọn Item Add
            Toast.makeText(this, "Action Add", Toast.LENGTH_SHORT).show();
            return true;
        case R.id.action_delete:
            Toast.makeText(this, "Action Delete", Toast.LENGTH_SHORT).show();
            return true;
        default:
            return super.onContextItemSelected(item);
    }
}
```
