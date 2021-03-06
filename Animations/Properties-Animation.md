# View Animation – Hiệu ứng cơ bản – Chuyển hoạt

![./Images/animation-1.png](/Images/animation-1.png)

<!-- TOC -->

- [View Animation – Hiệu ứng cơ bản – Chuyển hoạt](#view-animation-%e2%80%93-hi%e1%bb%87u-%e1%bb%a9ng-c%c6%a1-b%e1%ba%a3n-%e2%80%93-chuy%e1%bb%83n-ho%e1%ba%a1t)
    - [Vị trí File XML:](#v%e1%bb%8b-tr%c3%ad-file-xml)
    - [Resource reference (tên gọi):](#resource-reference-t%c3%aan-g%e1%bb%8di)
  - [Thuộc tính chung](#thu%e1%bb%99c-t%c3%adnh-chung)
    - [Alpha Animation (Độ trong suốt)](#alpha-animation-%c4%90%e1%bb%99-trong-su%e1%bb%91t)
    - [Rotate Animation (Độ xoay)](#rotate-animation-%c4%90%e1%bb%99-xoay)
    - [Scale Animation (Độ co dãn)](#scale-animation-%c4%90%e1%bb%99-co-d%c3%a3n)
    - [Translate Animation (Độ đi chuyển)](#translate-animation-%c4%90%e1%bb%99-%c4%91i-chuy%e1%bb%83n)
    - [Set/Batch Animation (nhiều hiệu ứng)](#setbatch-animation-nhi%e1%bb%81u-hi%e1%bb%87u-%e1%bb%a9ng)
    - [Gọi trong Code: onCreate Activity](#g%e1%bb%8di-trong-code-oncreate-activity)

<!-- /TOC -->

### Vị trí File XML:
res/anim/filename.xml

### Resource reference (tên gọi):
* In Java: R.anim.filename 
* In XML: @[package:]anim/filename

![/Images/animation-2.png](/Images/animation-2.png)

## Thuộc tính chung

- android:fillAfter=”true” – Giữ trạng thái sau khi chạy (trong ```<set>```)
- android:fillBefore=”true” – Giữ trạng thái trước khi chạy
- android:duration – Thời gian chạy
- android:startOffset – Thời gian chờ trước khi chạy
- android:repeatMode – Chế độ lặp
- android:repeatCount – Lần lặp
- infinite – lặp vô hạn
- android:interpolator – (nội suy) kiểu chạy
  - @android:anim/accelerate_decelerate_interpolator – Tăng tốc, giảm tốc
  - @android:anim/accelerate_interpolator – Tăng tốc
  - @android:anim/anticipate_interpolator – (Biết trước) quay lên vị trí đầu, chạy tiếp
  - @android:anim/anticipate_overshoot_interpolator – (Biết trước_Vượt qua) quay lên vị trí đầu, vượt qua vị trí cuối
  - @android:anim/bounce_interpolator – (nẩy lên) nẩy lên ở vị trí cuối
  - @android:anim/cycle_interpolator – (Vòng tròn) chạy qua lại đầu, cuối
  - @android:anim/decelerate_interpolator – (Giảm tốc) Chậm dần
  - @android:anim/linear_interpolator – (Thẳng) đều đều
  - @android:anim/overshoot_interpolator – (Vượt qua) vượt qua vị trí cuối
  - @android:anim/fade_in
  - @android:anim/fade_out
  - @android:anim/slide_in_left – Chạy từ trái qua
  - @android:anim/slide_out_right – Chạy từ phải rồi mất

### Alpha Animation (Độ trong suốt)

```xml
<set ... android:fillAfter="true" >
    <alpha
        android:duration="1000"
        android:fromAlpha="0.0"
        android:interpolator="@android:anim/accelerate_interpolator"
        android:toAlpha="1.0" />
</set>
```

### Rotate Animation (Độ xoay)

```xml
    <rotate
        android:duration="3000"
        android:fromDegrees="0"
        android:pivotX="50%"
        android:pivotY="50%"
        android:toDegrees="360"/>
```

### Scale Animation (Độ co dãn)

```xml
    <scale
         android:duration="3000"
         android:fromXScale="1"
         android:fromYScale="1"
         android:pivotX="50%"
         android:pivotY="50%"
         android:toXScale="2"
         android:toyScale="2" />
```

### Translate Animation (Độ đi chuyển)

```xml
    <translate
        android:duration="3000"
        android:fromXDelta="-50%"
        android:fromYDelta="-50%"
        android:toXDelta="0%"
        android:toYDelta="0%" />
```

### Set/Batch Animation (nhiều hiệu ứng)

**Chạy đồng thời:**

```xml
    <set xmlns:android="http://schemas.android.com/apk/res/android"
     android:interpolator="@android:anim/slide_out_right">
        <alpha
            android:duration="3000"
            android:fromalpha="0"
            android:toalpha="1" />
        <rotate
            android:duration="3000"
            android:fromdegrees="0"
            android:pivotx="50%"
            android:pivoty="50%"
            android:todegrees="360" />
        <scale
            android:duration="3000"
            android:fromxscale="1"
            android:fromyscale="1"
            android:pivotx="50%"
            android:pivoty="50%"
            android:toxscale="2"
            android:toyscale="2" />
        <translate
            android:duration="3000"
            android:fromxdelta="-50%"
            android:fromydelta="-50%"
            android:toxdelta="0%"
            android:toydelta="0%" />
    </set>
```

**Chạy hàng đợi:** ```<set ... android:startOffset=”thời gian”/>```

### Gọi trong Code: onCreate Activity

```java
// Load hiệu ứng
    final Animation amin = AnimationUtils.loadAnimation(getApplicationContext(), R.anim.fade_out);

    // Sự kiện
    amin.setAnimationListener(new Animation.AnimationListener() {
        @Override  public void onAnimationStart(Animation animation) {
        }
        @Override  public void onAnimationEnd(Animation animation) {
        }
        @Override  public void onAnimationRepeat(Animation animation) {
        }
    });

    // Chạy hiệu ứng
    txtMessage.startAnimation(amin);

    // Hủy hiệu ứng
    txtMessage.clearAnimation();
```

---
**Trích dẫn:**

- <http://www.androidhive.info/2013/06/android-working-with-xml-animations>
- <http://developer.android.com/guide/topics/resources/animation-resource.html>
- <https://developer.android.com/guide/topics/graphics/prop-animation>
