# Intent Service – Tự dừng sau khi trả về

<!-- TOC -->

- [Intent Service – Tự dừng sau khi trả về](#intent-service-%e2%80%93-t%e1%bb%b1-d%e1%bb%abng-sau-khi-tr%e1%ba%a3-v%e1%bb%81)
    - [Intent Service là gì?](#intent-service-l%c3%a0-g%c3%ac)
  - [Template - Code mẫu:](#template---code-m%e1%ba%abu)
  - [Activity gọi:](#activity-g%e1%bb%8di)
  - [Tham khảo](#tham-kh%e1%ba%a3o)

<!-- /TOC -->

### Intent Service là gì?
Tự dừng lại sau khi trả về kết quả, hay hàm **“onHandleIntent”** xử lý xong

## Template - Code mẫu:
```java
/**
 * Service tự kết thúc khi xử lý xong
 */
public class DownloadService extends IntentService {

    private int result = Activity.RESULT_CANCELED;
    public static final String ACTION_DOWNLOAD = "com.vogella.android.service.receiver";
    public static final String URL = "urlpath";
    public static final String FILENAME = "filename";
    public static final String FILEPATH = "filepath";
    public static final String RESULT = "result";

    public DownloadService(){
        super("DownloadServices");
    }

    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        // lấy intent gởi đến
        String urlPath = intent.getStringExtra(URL);
        String fileName = intent.getStringExtra(FILENAME);

        // xóa file nếu tồn tại
        File file = new File(Environment.getExternalStorageDirectory(), fileName);
        if (file.exists()){
            file.delete();
        }

        // ghi từng dòng đến khi kết thúc
        InputStream inputStream = null;
        FileOutputStream outputStream = null;
        try {

            // open url connection
            java.net.URL url = new URL(urlPath);
            inputStream = url.openConnection().getInputStream();
            // read request
            InputStreamReader reader = new InputStreamReader(inputStream);
            outputStream = new FileOutputStream(file.getPath());
            // input read =&gt; output write
            int next = -1;
            while ((next = reader.read()) != -1){
                outputStream.write(next);
            }

            inputStream.close();
            outputStream.close();

            // successfully finished
            result = Activity.RESULT_OK;

        } catch (Exception ex){
            ex.printStackTrace();
        }
        // gởi kết quả về, receiver nhận
        publishResults(file.getAbsolutePath(), result);
    }

    /**
     * Send Broadcast Receiver trong Service
     */
    private void publishResults(String absolutePath, int result) {
        Intent intent = new Intent(ACTION_DOWNLOAD);
        intent.putExtra(FILEPATH, absolutePath);
        intent.putExtra(RESULT, result);
        sendBroadcast(intent);
    }
}
```

## Activity gọi:
```java
public class MainActivity extends Activity {

    private BroadcastReceiver receiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            // nhận Result từ DownloadService
            Bundle bundle = intent.getExtras();
            if (bundle != null) {
                String downloadPath = bundle.getString(DownloadService.FILEPATH);
                int resultCode = bundle.getInt(DownloadService.RESULT);
                if (resultCode == RESULT_OK){
                    // download success
                    String message = "Download done: " + downloadPath;
                    Toast.makeText(getBaseContext(), message, Toast.LENGTH_SHORT).show();
                    txtStatus.setText(message);
                } else {
                    // download failed
                    Toast.makeText(getBaseContext(), "Download failed", Toast.LENGTH_SHORT).show();
                    txtStatus.setText("Download failed");
                }
            }
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
    }

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

 /**
 * Hàm chính: Nhấp Download
 **/
    private void btnDownload_onClick(View v) {
        String url = txtURL.getText().toString();

        // Thêm thông tin, đường dẫn cho DownloadService
        Intent intent = new Intent(this, DownloadService.class);
        intent.putExtra(DownloadService.FILENAME, "index.html");
        intent.putExtra(DownloadService.URL, url);

        startService(intent); // Chạy DownloadService, chạy 1 lần, chạy ngầm
        txtStatus.setText("Service Download start");
    }
}
```

---

## Tham khảo

- <http://developer.android.com/guide/components/aidl.html>
- <http://o7planning.org/web/fe/default/vi/document/1162405/huong-dan-lap-trinh-android-service>
