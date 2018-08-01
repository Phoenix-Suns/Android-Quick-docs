# TOAST CUSTOM LAYOUT

 ![async task 1](/Images/toast_custom_layout.jpg)

 **Lưu ý: ko sử dụng Input Control cho Toast Layout được, không tương tác được.**

 ## Tạo file layout cho Toast: layout/toast_layout.xml

 ```xml
 <LinearLayout android:id="@+id/toast_layout_root"
    xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="horizontal"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent"
              android:padding="8dp"
              android:background="#DAAA" >

    <ImageView android:src="@mipmap/ic_launcher"
               android:layout_width="wrap_content"
               android:layout_height="wrap_content"
               android:layout_marginRight="8dp" />

    <TextView android:id="@+id/text"
              android:textAppearance="?android:textAppearanceLarge"
              android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:textColor="#FFF" />
</LinearLayout>
```

## Hiển thị Toast trong Activity(onCreate)

```java
public void showToast(View view) {
        CharSequence text_message = "Thử Nghiệm Tin nhắn 123";
      
        Toast toast = Toast.makeText(getBaseContext(), text_message, Toast.LENGTH_SHORT);
        // Vị trí TOP | LEFT (Trên cùng, tay trái)
        toast.setGravity(Gravity.TOP|Gravity.LEFT, 0, 0);

        //----- Set Toast Layout -----
        LayoutInflater inflater = getLayoutInflater();
        View layout = inflater.inflate(R.layout.toast_layout, (ViewGroup) findViewById(R.id.toast_layout_root));
        TextView textView = (TextView) layout.findViewById(R.id.text);
        textView.setText(text_message);

        toast.setView(layout);

        toast.show();
    }
}
```