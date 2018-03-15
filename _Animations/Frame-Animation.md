# Frame Animation – Hiệu Ứng theo khung hình
Nhiều hình chạy

### Vị trí file: res/drawable/filename.xml
### Lớp sử dụng: AnimationDrawable

### Gọi sử dụng:
* In Java: R.drawable.filename
* In XML: @[package:]drawable.filename

## Cú pháp:
```xml
<animation-list android:oneshot="[false/true]"
 xmlns:android="http://schemas.android.com/apk/res/android">
 <item android:drawable="@[package:]drawable/drawable_resource_name"
 android:duration="integer"/>
</animation-list>
```
**Thuộc tính:**
* android:oneshot: chạy 1 lần
        * true – chạy 1 lần
        * false – chạy hình nhiều lần
* android:duration – thời gian của hình

## Gọi trong code:
**file xml – res/anim/rocket.xml:**
```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
 android:oneshot="false">
 <item
    android:drawable="@drawable/rocket_thrust1"
    android:duration="200" />
 <item
    android:drawable="@drawable/rocket_thrust2"
    android:duration="200" />
 <item
    android:drawable="@drawable/rocket_thrust3"
    android:duration="200" />
</animation-list>
```

**Sử dụng trong onCreate Activity**
```java
ImageView rocketImage = (ImageView) findViewById(R.id.rocket_image);
rocketImage.setBackgroundResource(R.drawable.rocket_thrust);

rocketAnimation = (AnimationDrawable) rocketImage.getBackground();
rocketAnimation.start();
```

---
**Trích:**
http://developer.android.com/guide/topics/resources/animation-resource.html

