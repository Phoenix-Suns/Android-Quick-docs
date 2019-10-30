# Services – Xử lý tiến trình chạy nền

<!-- TOC -->

- [Services – Xử lý tiến trình chạy nền](#services-%e2%80%93-x%e1%bb%ad-l%c3%bd-ti%e1%ba%bfn-tr%c3%acnh-ch%e1%ba%a1y-n%e1%bb%81n)
  - [Service là gì](#service-l%c3%a0-g%c3%ac)
  - [Khai báo trong Mainifresh](#khai-b%c3%a1o-trong-mainifresh)
  - [Vòng đời](#v%c3%b2ng-%c4%91%e1%bb%9di)
  - [Service template - Code Mẫu](#service-template---code-m%e1%ba%abu)
    - [Khởi động Service](#kh%e1%bb%9fi-%c4%91%e1%bb%99ng-service)
    - [Dừng Service](#d%e1%bb%abng-service)
  - [Nhận kết quả Service trả về qua Receiver](#nh%e1%ba%adn-k%e1%ba%bft-qu%e1%ba%a3-service-tr%e1%ba%a3-v%e1%bb%81-qua-receiver)
    - [Trực tiếp trong Service](#tr%e1%bb%b1c-ti%e1%ba%bfp-trong-service)
    - [Gián tiếp qua Receiver](#gi%c3%a1n-ti%e1%ba%bfp-qua-receiver)

<!-- /TOC -->

## Service là gì

- Là thành phần chạy ngầm, không hiển thị giao diện.
- Có thể chạy vô thời hạn, khi thành phần bắt đầu bị hủy (Activity)
- Không chạy Thread, phải tự tạo

## Khai báo trong Mainifresh

```xml
<manifest ... >
  ...
  <application ... >
      <service android:name=".ExampleService" />
      ...
  </application>
</manifest>

```

## Vòng đời

![/Images/service_lifecycle.png](/Images/service_lifecycle.png)

1. Component (Activity) bắt đầu Service: startService()
    1. Service sẽ truy cập: **onStartCommand()**
    2. Dừng Service trong chính nó: **stopSelf()**, **stopService()**
2. Component (Activity) bắt đầu Service, Component muốn Bind (gắn kết) với Service: **bindService()**
    1. Service sẽ truy cập: **onBind()**
    2. Component giao tiếp Service = interface trả về **IBinder**

## Service template - Code Mẫu

```java
public class MusicService extends Service {

    public static final String EXTRA_RAW_MUSIC_ID = "com.imark.nghia.nghiaservice_1_start.extra.RAW_MUSIC_ID";
    MediaPlayer player;

    /**
     * Khởi tạo Service
     */
    @Override
    public void onCreate() {
        super.onCreate();

        // khởi tạo Player
        player = MediaPlayer.create(this, R.raw.defaut_music);
        player.setLooping(true);
    }

    /**
     * The service is starting, hành động sau khi Component gọi "startService()"
     */
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {

        // Lấy Intent trong Service
        int musicId = intent.getIntExtra(EXTRA_RAW_MUSIC_ID, 0);
        if (musicId != 0) {
            // có nhạc
            player = MediaPlayer.create(this, musicId);
        }
        // run player
        player.start();

        stopForeground(false)   // Destroy app will detroy Service

        return START_STICKY;
    }

    /**
     * A client is binding to the service with
     * bindService()
     * Sử dụng để ứng dụng khác gọi Service
     */
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

    @Override
    public void onDestroy() {
        // stop music
        player.stop();
    }

    /**
     * Khởi đông Service mặc định trong hàm "onStartCommand"
     */
    public static void startPlayDefaultMusic(Context context) {
        Intent iMusicService = new Intent(context, MusicService.class);
        context.startService(iMusicService);
    }

    /**
     * khởi đông Service put bài hát vào
     */
    public static void startPlayMusic(Context context, int musicRawId) {
        Intent iMusicService = new Intent(context, MusicService.class);
        iMusicService.putExtra(MusicService.EXTRA_RAW_MUSIC_ID, musicRawId);
        context.startService(iMusicService);
    }

    /**
     * Dừng Service -> hàm "onDestroy" trong Service
     */
    public static void stopPlayMusic(Context context) {
        Intent iMusicService = new Intent(context, MusicService.class);
        context.stopService(iMusicService);
    }
}

```

**Ghi chú code:**
onStartCommand Return:

1. START_NOT_STICKY:
    1. Tắt Service – Hệ thống (Android OS) không cần tạo lại. Mất kết quả.
    2. Sử dụng: Khởi động lại **Unfinish Jobs cùng Aplication.**
2. START_STICKY:
    1. Tắt Service – đủ tài nguyên – Hệ thống tự tạo lại = null Intent.
    2. Sử dụng khi: **Phát Media, Không cần lệnh chạy, chạy vô thời hạn, chạy để sử lý**.
3. START_REDELIVER_INTENT
    1. Tắt Service – hệ thống tự tạo lại = Intent cuối cùng.
    2. Sử dụng khi: Service cần **khởi động lặp tức**, như tải 1 tập tin (tải lại sau khi chết =D).

### Khởi động Service

```java
// khởi động Service -> đến hàm "onStartCommand" trong Service
Intent iMusicService = new Intent(this, MusicService.class);
startService(iMusicService);

// Dừng Service -> đến hàm "onDestroy" trong Service
Intent iMusicService = new Intent(this, MusicService.class);
stopService(iMusicService);

```

### Dừng Service

- Tự dừng:
  - stopSelf(),
  - stopSelf(int) : Dừng với Id của Component yêu cầu
- Component khác dùng lệnh dừng:
  - stopService()

## Nhận kết quả Service trả về qua Receiver

### Trực tiếp trong Service

**Toast, Notification.**

### Gián tiếp qua Receiver

**Service gởi Broadcast Receiver:**

```java
/**
 * Send Broadcast Receiver trong Service
 */
private void publishResults(int result) {
 Intent intent = new Intent(ACTION_DOWNLOAD);
 intent.putExtra(RESULT, result);
 sendBroadcast(intent);
}

```
**Component (Activity) khai báo Broadcoast nhận kết quả từ Service:
**
```java
/**
 * Tạo BR nhận kết quả từ DownloadService
 */
private BroadcastReceiver receiver = new BroadcastReceiver() {
 @Override
 public void onReceive(Context context, Intent intent) {
  // nhận Result từ DownloadService
  Bundle bundle = intent.getExtras();
  if (bundle != null) {
   int result = bundle.getInt(DownloadService.RESULT);  // kết quả
  }
 }
};

@Override
protected void onResume() {
 super.onResume();
 // đăng kí receiver
 registerReceiver(receiver, new IntentFilter(DownloadService.ACTION_DOWNLOAD));
}

@Override
protected void onPause() {
 super.onPause();
 // hủy đăng kí receiver
 unregisterReceiver(receiver);
}

```

---
Tham khảo: 
http://developer.android.com/guide/components/aidl.html
http://o7planning.org/web/fe/default/vi/document/1162405/huong-dan-lap-trinh-android-service