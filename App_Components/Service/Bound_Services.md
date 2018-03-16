# Bound Service – lấy kết quả liên tục

**Bound Service là gì?**
* Component ràng buộc Service để lấy kết quả liên tục
* Component chạy Service: **bindService()**
* Service truy cập hàm: **onBind()**

### Bound Service Template - Code Mẫu:
```java
/**
 * Bind service dùng Ibinder đồng bộ
 * Client &amp; service chạy cùng ứng dụng, cùng tiến trình
 * Example: run music on background
 */
public class LocalService extends Service {

    Random mRandom = new Random();
    private IBinder mBinder = new LocalBinder();

    public LocalService() {
    }

    @Override
    public IBinder onBind(Intent intent) {
        return mBinder;
    }

    /**
     * Tính tổng 3 số, 1 số ngẫu nhiên (trong Service)
     * @param number1
     * @param number2
     * @return
     */
    public int getTotal(int number1, int number2) {
        return number1 + number2 + mRandom.nextInt();
    }

    /**
     * Lớp dùng cho client Binder.
     * Service chạy cùng tiến trình với clients =&gt; không cần dùng IPC
     * perform interprocess communication (IPC): Thực hiện giao tiếp trong tiến trình
     */
    public class LocalBinder extends Binder {
        public LocalService getService() {
            // trả về khởi tạo của LocalService, để Content nhận lưu lại
            return LocalService.this;
        }
    }
}
```

### Gọi trong Component khác (ví dụ: Activity):
```java
public class MainActivity extends Activity {

    EditText mEditValue1, mEditValue2;
    Button mButtonAdd;

    boolean mIsServiceBound;
    LocalService mLocalService;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
    }

    /**
     * Định nghĩa phản hồi cho Service binding,
     * chuyển đến hàm bindService() của Service
     */
    private ServiceConnection mLocalService_Connection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            // bound(ràng buộc) to LocalService
            // chuyển IBinder =&gt; lấy khởi tạo LocalService
            LocalService.LocalBinder binder = (LocalService.LocalBinder) service;
            mLocalService = binder.getService();

            mIsServiceBound = true;
            Toast.makeText(getBaseContext(), "Local Service connected", Toast.LENGTH_SHORT).show();
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
            mIsServiceBound = false;
            Toast.makeText(getBaseContext(), "Local Service disconnected", Toast.LENGTH_SHORT).show();
        }
    };

    @Override
    protected void onStart() {
        super.onStart();

        // bound LocalService
        Intent intent = new Intent(this, LocalService.class);
        bindService(intent, mLocalService_Connection, Activity.BIND_AUTO_CREATE);
        mIsServiceBound = true;
    }

    @Override
    protected void onStop() {
        super.onStop();

        // unBound LocalService
        unbindService(mLocalService_Connection);
    }

    /**
     * Hàm chính: Tính total dự trên service
     */
    private void showTotalNumber() {
        if (mIsServiceBound) {
            // gọi phương thức trong localService
            // có thể bị treo máy. đề nghị chạy trong thread để không giảm hiệu xuất
            int number1 = Integer.parseInt(mEditValue1.getText().toString());
            int number2 = Integer.parseInt(mEditValue2.getText().toString());
            int total = mLocalService.getTotal(number1, number2);

            Toast.makeText(this, "Tổng là: " + total, Toast.LENGTH_LONG).show();
        }
    }
}
```

## Sử dụng Messager để tương tác
* Giao tiếp tiến trình điều kiển (Tương tác ngược lại Activity)
* Không cần sử dụng AIDL (Android Interface Definition Language)

### Cách sử dụng:
* Dịch vụ triển khai **Handler** để nhận lệnh gọi lại cho mỗi lệnh gọi từ một máy khách.
* **Handler** được sử dụng để tạo một đối tượng **Messenger** (là một tham chiếu tới **Handler**).
* **Messenger** tạo một **IBinder** mà dịch vụ trả về máy khách từ **onBind()**.
* Máy khách sử dụng **IBinder** để khởi tạo **Messenger** (tham chiếu tới **Handler** của dịch vụ), mà máy khách sử dụng để gửi các đối tượng **Message** tới dịch vụ.
* Dịch vụ nhận được từng **Message** trong **Handler** của mình—cụ thể là theo phương pháp **handleMessage()**.

### Template - Code mẫu:
```java
/**
 * Created by Nghia-PC on 6/26/2015.
 * Service chuyển chữ thành Hoa, trả về cho Activity
 */
public class MessageService extends Service {
    // lệnh đến service để hiển thị tin nhắn
    public static final int MSG_SAY_HELLO = 1;
    public static final int MSG_TO_UPPER_CASE = 2;
    public static final int MSG_TO_UPPER_CASE_RESPONSE = 3;
    public static final String KEY_UPPER_DATA = "UPPER DATA";

    // client gởi messages đến IncomingHandler
    final Messenger mMessenger = new Messenger(new IncomingHandler());

    /**
     * Khi ràng buộc service,
     * trả về Messager để gởi Message đến Service
     * @param intent
     * @return
     */
    @Override
    public IBinder onBind(Intent intent) {
        Toast.makeText(getApplicationContext(), "binding", Toast.LENGTH_SHORT).show();
        return mMessenger.getBinder();
    }

    /**
     * Handler tin nhắn tới từ Client
     */
    public class IncomingHandler extends Handler {
        @Override
        public void handleMessage(Message msg) {
            try {
                switch (msg.what) {
                    case MSG_SAY_HELLO:
                        // ---- Hiện hello ----
                        Toast.makeText(getApplicationContext(), "hello!", Toast.LENGTH_SHORT).show();
                        break;
                    case MSG_TO_UPPER_CASE:
                        // ---- Chuyển chữ hoa ----
                        // Lấy data gởi đến
                        String data = msg.getData().getString("data");

                        // init response message
                        Message message = Message.obtain(null, MSG_TO_UPPER_CASE_RESPONSE);
                        Bundle bundle = new Bundle();
                        bundle.putString(KEY_UPPER_DATA, data.toUpperCase());
                        message.setData(bundle);

                        // gởi phản hồi về activity
                        msg.replyTo.send(message);

                    default:
                        super.handleMessage(msg);
                        break;
                }

            } catch (RemoteException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### Activity sử dụng:
```java
public class MainActivity extends Activity {

    /** Messenger giao tiếp Service */
    Messenger mMessenger = null;
    /** Flag đánh dấu service chạy */
    boolean isServiceBound;
    /**
     * Lớp tương tác giao diện chính của Service
     */
    private ServiceConnection mConnection = new ServiceConnection() {
        public void onServiceConnected(ComponentName className, IBinder service) {
            // Hàm sẽ gọi khi kết nối Service khởi tạo, Trả về đối tượng tương tác với Service.
            // Giao tiếp = Messenger =&gt; Chuyển IBinder thành Messenger
            mMessenger = new Messenger(service);
            isServiceBound = true;
        }

        public void onServiceDisconnected(ComponentName className) {
            // gọi khi bị mất kết nối, đường truyền không mong đợi
            // :)) đùa à
            mMessenger = null;
            isServiceBound = false;
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
    }

    @Override
    protected void onStart() {
        super.onStart();
        // kết nối Service
        bindService(new Intent(this, MessageService.class), mConnection, Context.BIND_AUTO_CREATE);
    }

    @Override
    protected void onStop() {
        super.onStop();
        // Hủy kết nối Service
        if (isServiceBound) {
            unbindService(mConnection);
            isServiceBound = false;
        }
    }

    public void sayHello(View v) {
        if (!isServiceBound) return;

        try {
            // Tạo và gởi Message to Service, sử dụng "what"
            Message msg = Message.obtain(null, MessageService.MSG_SAY_HELLO, 0, 0);
            mMessenger.send(msg);

        } catch (RemoteException e) {
            e.printStackTrace();
        }
    }

    /**
     * lấy chữ trong Editext, gởi service, nhận phản hồi
     */
    private void Button2_onClick(View v) {
        if (isServiceBound) {
            try {
                String text = mEditText.getText().toString();

                // Khởi tạo tin nhắn: msg.what = ConvertService.TO_UPPER_CASE
                Message message = Message.obtain(null, MessageService.MSG_TO_UPPER_CASE);

                // đưa dữ liệu vào messege
                Bundle b = new Bundle();
                b.putString("data", text);
                message.setData(b);

                // Nhận phản hồi từ Service bằng lớp IncomingHandler()
                message.replyTo = new Messenger(new ResponseHandler());

                // gởi message đến Service
                mMessenger.send(message);

            } catch (Exception ex){
                ex.printStackTrace();
            }
        } else {
            Toast.makeText(MainActivity.this, "Service is disconneted!", Toast.LENGTH_LONG).show();
        }
    }

    // Lớp nhận phải hồi từ Service
    class ResponseHandler extends Handler {

        @Override
        public void handleMessage(Message msg) {
            int respCode = msg.what;

            switch (respCode) {
                case MessageService.MSG_TO_UPPER_CASE_RESPONSE: {
                    // Nhận dữ liệu từ Service
                    String value = msg.getData().getString(MessageService.KEY_UPPER_DATA);
                    Toast.makeText(getBaseContext(), value, Toast.LENGTH_LONG).show();
                }
            }
        }
    }
}
```

---
**Tham khảo:**
http://developer.android.com/intl/vi/guide/components/bound-services.html