# APPLICATION
Lưu trữ trạng thái cục bộ của ứng dụng.

<!-- TOC -->

- [APPLICATION](#application)
    - [Tạo File: AppApplication.java](#tạo-file-appapplicationjava)
    - [Khai báo trong AndroidManifest.xml](#khai-báo-trong-androidmanifestxml)
    - [Component(Activity) khác gọi để sử dụng](#componentactivity-khác-gọi-để-sử-dụng)

<!-- /TOC -->

## Tạo File: AppApplication.java
```java
public class AppApplication extends Application {
 private String globalValue;
 public String getGlobalValue() {
 return globalValue;
 }
 public void setGlobalValue(String globalValue) {
 this.globalValue = globalValue;
 }
@Override
    public void onCreate() {
        super.onCreate();

        //Tạo font mặc định để thêm vào AppTheme
        FontUtil.setDefaultFont(this, "SERIF", "neutra.ttf");
    }
}
```

## Khai báo trong AndroidManifest.xml
```xml
<manifest package="com.apress.helloworld">
 <application android:name=".AppApplication" ... >
   ...
 </application>
</manifest>
```

## Component(Activity) khác gọi để sử dụng
```java
MyApplication myApplication = (MyApplication) getApplication();
myApplication.setGlobalValue("1234");
```

