# Broadcast Receiver (receiver)

<!-- TOC -->

- [Broadcast Receiver (receiver)](#broadcast-receiver-receiver)
        - [Receiver là gì](#receiver-là-gì)
    - [1 - Tạo thông qua Manifesh:](#1---tạo thông-qua-manifesh)
    - [2 - Tạo thông qua biến, trong Component](#2---tạo thông-qua-biến-trong-component)
        - [Khai báo:](#khai-báo)
        - [Đăng kí nhận, xử lý Receiver:](#đăng-kí-nhận-xử-lý-receiver)
        - [Component(Activity) Hủy đăng kí nhận, dùng khi thu hồi bộ nhớ, hay dùng trong OnDestroy:](#componentactivity-hủy-đăng-kí-nhận-dùng-khi-thu-hồi-bộ-nhớ-hay-dùng-trong-ondestroy)
    - [Component Gởi thông tin đến Receiver:](#component-gởi-thông-tin-đến-receiver)

<!-- /TOC -->

### Receiver là gì

* **Nhận sự kiện** phát đi từ:
  * Các ứng dụng (tự tạo tên sự kiện trong app)
  * Hệ thống (vd: nhận mạng, mở bluetooth, ...)
* Lưu ý:
  * Tránh xử lý quá lâu
  * Tránh xử lý bất đồng bộ, luồng (cũng lâu à =D)
* Có 2 cách khởi tạo:
  * Đăng kí lớp qua Manifesh
  * Đăng kí biến trực tiếp trên Component (vd: activity)

## 1 - Tạo thông qua Manifesh:

Đăng kí nhận sự kiện trong Manifesh, khi bắt được sự kiện sẽ chuyển đến lớp Receiver xử lý

```xml
<receiver android:name=".receivers.UploadAllDataReceiver">
    <intent-filter>
        <!--đăng kí sự kiện, khi sự kiện xảy ra sẽ chuyển đến lớp UploadAllDataReceiver-->
	<!--từ ứng dụng-->
        <action android:name="com.imark.nghia.idsspcore.services.action.RECEIVER_UPLOAD_FINISH" />
	<!--từ hệ thống-->
	<action android:name="android.net.wifi.WIFI_STATE_CHANGED" />
    </intent-filter>
</receiver>```

```
Tạo 1 lớp xử lý khi nhận được sự kiện:

```java
public class UploadAllDataReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
		//***********
		// Xử lý khi nhận được
		//***********
    }
}

```

## 2 - Tạo thông qua biến, trong Component

### Khai báo:

```java
// Khai báo biến toàn cục
BroadcastReceiver mReceiverUploadFinish;
```

### Đăng kí nhận, xử lý Receiver:

```java

// khởi tạo nhận kết quả
mReceiverUploadFinish = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
        //***********
	// Xử lý khi nhận được intent
	//***********
    }
};
// đăng kí nhận receiver (thay cho khai báo trong Manifesh)
mContext.registerReceiver(mReceiverUploadFinish, new IntentFilter(UploadAllDataService.ACTION_UPLOAD_FINISH));

```

### Component(Activity) Hủy đăng kí nhận, dùng khi thu hồi bộ nhớ, hay dùng trong OnDestroy:

```java

// hủy đăng kí nhận receiver
if (mReceiverUploadFinish != null) 
	mContext.unregisterReceiver(mReceiverUploadFinish);

```

## Component Gởi thông tin đến Receiver:

```java
public static final String ACTION_UPLOAD_FINISH =  "com.imark.nghia.idsspcore.services.action.RECEIVER_UPLOAD_FINISH";
Intent intent = new Intent(ACTION_UPLOAD_FINISH);
intent.putExtra("định_danh", "giá_trị_gởi");  // gởi giá trị đến Receiver
sendBroadcast(intent);

```

---
**Tham khảo:**

*   [http://codetheory.in/android-broadcast-receivers/](http://codetheory.in/android-broadcast-receivers/)
*   [http://www.academia.edu/5792453/B%C3%A0i_5_Broadcast_Receiver_tr%C3%AAn_Android](http://www.academia.edu/5792453/B%C3%A0i_5_Broadcast_Receiver_tr%C3%AAn_Android)