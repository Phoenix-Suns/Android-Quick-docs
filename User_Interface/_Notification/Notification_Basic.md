# Notification – Thông báo trên Status Bar

- [Notification – Thông báo trên Status Bar](#notification--thông-báo-trên-status-bar)
	- [Simple Example](#simple-example)
	- [Sử dụng: NotificationCompat, không cần chia phiên bản android](#sử-dụng-notificationcompat-không-cần-chia-phiên-bản-android)
	- [Service giao tiếp Notification](#service-giao-tiếp-notification)
	- [Notification Channel (android O)](#notification-channel-android-o)

## Simple Example

![async task 1](/Images/notification_basic_1.jpg)

 ```java
 public static final String EXTRA_KEY_NUMBER = "EXTRA KEY NUMBER";
public static final int REQUEST_CODE_INTENT = 1;
private static final int MY_NOTIFY_ID = 1; //Mã quản lý Notification

/** Hiển thị Notification */
private void showNotification() {
	//Quản lý Notification
    final NotificationManager manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
    
    Notification.Builder mNotifiBuilder = new Notification.Builder(this);
	//--- Tạo Nội dung Notification: Phải có Ảnh + Chữ ---
    mNotifiBuilder.setContentTitle("Day la tieng noi")
    	.setContentText("Nội dung text bên trong tiêu đề")
    	.setSmallIcon(android.R.drawable.ic_lock_idle_low_battery);

    //--- Tạo Intent gọi khi Click Notification ---
    Intent intent = new Intent(this, Detail1Activity.class);
    intent.putExtra(EXTRA_KEY_NUMBER, 12121991); //Gởi 1 số đến Activity hiện khi Notification click<

    if (Build.VERSION.SDK_INT < Build.VERSION_CODES.JELLY_BEAN) {
        // api < 16
        // Intent( ý định) Pending (chờ xử lý): Intent chạy suốt Ứng dụng
        PendingIntent resultPendingIntent = PendingIntent
			.getActivity(this, REQUEST_CODE_INTENT, intent, PendingIntent.FLAG_UPDATE_CURRENT);
        mNotifiBuilder.setContentIntent(resultPendingIntent);

		//--- Hiển thị Notification ---
        manager.notify(MY_NOTIFY_ID, mNotifiBuilder.getNotification());  // hiển thị, cập nhật Notification
    } else {
        // api >= 16
        // ---Tạo hàng chờ Activity trở về---
        TaskStackBuilder stackBuilder = TaskStackBuilder.create(this);
        stackBuilder.addParentStack(Detail1Activity.class);  // thêm vào ngăn chứa    
        stackBuilder.addNextIntent(intent);   // thêm Intent trên cùng ngăn chứa, Thêm được nhiều Intent
        PendingIntent resultPendingIntent = stackBuilder
			.getPendingIntent(0, PendingIntent.FLAG_UPDATE_CURRENT);  //Tạo PendingIntent từ StackBuilder
        // Đặt PendingIntent vào NotifiBuilder
        mNotifiBuilder.setContentIntent(resultPendingIntent);

		//--- Hiển thị Notification ----
        manager.notify(MY_NOTIFY_ID, mNotifiBuilder.build());  // hiển thị, cập nhật Notification
    }
}
```

## Sử dụng: NotificationCompat, không cần chia phiên bản android

Phải thêm thư viện: android.support.v4+

```java
void runNotifyCompat(View v){
	
	final NotificationManager manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
	createNotificationChannel(manager);

	//---Cài đặt giao diện ---
	NotificationCompat.Builder notifiBuilder = new NotificationCompat.Builder(getBaseContext(), "NOTIFICATION_CHANNEL_ID")
		.setContentTitle("You alway Smile") //phai co
		.setContentText("This is the voice of HCM city!")
		.setSmallIcon(R.mipmap.ic_abc) //phai co, 24x24
		.setLargeIcon(BitmapFactory.decodeResource(getResources(), R.mipmap.ic_abc))
		.setAutoCancel(true) //Ẩn khi User click
		.setOngoing(true) //Không cho User hủy
		.setPriority(Notification.PRIORITY_MAX) //Độ ưu tiên cao nhất
		.setProgress(100, 25, false) //Hiện Progress với 25%
		//.setProgress(0, 0, false) //Ẩn Progress
		.setColor(0x008000);
		
	//--- Thêm Intent khi click chuyển đến Activity ---
    Intent intent = new Intent(this, Detail1Activity.class);
    intent.putExtra(EXTRA_KEY_NUMBER, 12121991);
	//Tạo hàng chờ Activity trở về
	TaskStackBuilder stackBuilder = TaskStackBuilder.create(this);
	stackBuilder.addParentStack(Detail1Activity.class);  // thêm vào ngăn chứa
	stackBuilder.addNextIntent(intent);  // thêm Intent trên cùng ngăn chứa, Thêm được nhiều Intent
	// Gets a PendingIntent containing the entire back stack
	PendingIntent resultPendingIntent = stackBuilder
		.getPendingIntent(0, PendingIntent.FLAG_UPDATE_CURRENT);
	// ấn vào gọi Intent chuyển đến Activity
	notifiBuilder.setContentIntent(resultPendingIntent);
	
	//--- Hiển thị Notification ---
	manager.notify(MY_NOTIFY_ID, notifiBuilder.build());

// lấy Dữ liệu từ Intent của Notification từ Activity khác
    //int value = getIntent().getIntExtra(MainActivity.EXTRA_KEY_NUMBER, 0);
}

//--- Close Notification ---
public void cancelNotifiCompat(View v){
	final NotificationManager manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
	manager.cancel(MY_NOTIFY_ID);
}

//--- Chạy Service Hiện Notification ---
public void runServiceNotification(View v) {
	Intent i = new Intent(getBaseContext(), ProgressService.class);
	startService(i);
}

private void createNotificationChannel(NotificationManager manager) {
	if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O) {
		NotificationChannel channel = new NotificationChannel(
				"NOTIFICATION_CHANNEL_ID",
				"YOUR_CHANNEL_NAME",
				NotificationManager.IMPORTANCE_DEFAULT);
		channel.setDescription("YOUR_NOTIFICATION_CHANNEL_DESCRIPTION");
		manager.createNotificationChannel(channel);
	}
}
```

## Service giao tiếp Notification

```java
public class ProgressService extends Service
{
	private static final int PROGRESS_NOTIFY_ID = 2;

	public boolean running = false; //kiểm tra đã chạy chưa
    private NotificationCompat.Builder mNotifiBuilder;

	@Override
	public void onCreate()
	{
		super.onCreate();
		mNotifiBuilder = new NotificationCompat.Builder(getApplicationContext());
	}

	@Override
	public int onStartCommand(Intent intent, int flags, int startId) {
		super.onStartCommand(intent, flags, startId);
		
		startServiceWithNotification();
			
		return START_STICKY; //Tự hồi sinh
	}
	
	void startServiceWithNotification() {
		//Check Running
		if (running) return;
		running = true;
		
		final int minValue = 0;
		final int maxValue = 1000;

		//--- Hiện giao diện khi Running ---
		mNotifiBuilder.setSmallIcon(android.R.drawable.btn_default)
			.setContentTitle("Running...")
			.setOngoing(true); // ko cho user huy
		mNotifiBuilder.build().flags = Notification.FLAG_NO_CLEAR;     // NO_CLEAR makes the notification stay when the user performs a "delete all" command
		startForeground(PROGRESS_NOTIFY_ID, mNotifiBuilder.build()); //Chay notifi duoi nen

		final NotificationManager manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
        // Hiển thị process
        new Thread(new Runnable() {
				@Override
				public void run() {
					try {
						//---Chay & Hien Progress dang chay ---
						for (int i = minValue; i <= maxValue; i += 100) {
							mNotifiBuilder.setProgress(maxValue, i, false);
							manager.notify(PROGRESS_NOTIFY_ID, mNotifiBuilder.build());
							Thread.sleep(2 * 1000); // dừng lại 2s
						}
						//--- Hien thi giao dien Finish ---
						stopForeground(true); //Hủy Notification, để đặt lại lệnh setOngoing
						mNotifiBuilder.setContentTitle("Finish")
							.setProgress(0,0,false) //Hide progress
							.setOngoing(false); //Cho user hủy
						manager.notify(PROGRESS_NOTIFY_ID, mNotifiBuilder.build()); //Hiện lại Notifi, user được hủy
					} catch (Exception ex) {
						ex.printStackTrace();
					}
				}
			}).start();
	} 

	@Override
	public IBinder onBind(Intent p1)
	{
		return null;
	}

	@Override
	public void onDestroy()
	{
		//khi ung dung bi close, crash
		running = false;
		Toast.makeText(getApplicationContext(), 
					   "Stop Running! by Sys", Toast.LENGTH_SHORT).show();
		super.onDestroy();
	}
	
	//--- Dừng Service ---
	public void stopService() {
		stopForeground(true);
		stopSelf();
		running = false;
		Toast.makeText(getApplicationContext(), 
			"Stop Running! by user", Toast.LENGTH_SHORT).show();
	}
}
```

## Notification Channel (android O)

```java
// Simple Version
// @RequiresApi(Build.VERSION_CODES.O)
private fun bindingForegroundNotification() {
	if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
		val notificationManager = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
		val name = resources.getString(R.string.app_name)
		val importance = NotificationManager.IMPORTANCE_LOW
		NotificationChannel(CHANNEL_ID, name, importance).apply {
			enableLights(false)
			enableVibration(false)
			notificationManager.createNotificationChannel(this)
		}
	}
	notification = NotificationCompat.Builder(applicationContext, CHANNEL_ID)
			.setContentTitle(resources.getString(R.string.app_name))
			.setContentText("")
			.setSmallIcon(R.drawable.ic_headset_small)
	startForeground(NOTIFICATION_ID, notification?.build())
}
```

```java
val intent = Intent(this, MainActivity::class.java)
	intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP)
	val pendingIntent = PendingIntent.getActivity(this, 0, intent, PendingIntent.FLAG_ONE_SHOT or PendingIntent.FLAG_IMMUTABLE)

	val channelId = getString(R.string.app_name)
	val defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION)

	val notificationBuilder = NotificationCompat.Builder(this, channelId)
			.setSmallIcon(R.drawable.ic_launcher_background)
			.setLargeIcon(BitmapFactory.decodeResource(resources, R.drawable.ic_launcher_background))
			.setContentTitle(getString(R.string.project_id))
			.setContentText(messageBody)
			.setAutoCancel(true)
			.setSound(defaultSoundUri)
			.setContentIntent(pendingIntent)
			.setDefaults(Notification.DEFAULT_ALL)
			.setPriority(NotificationManager.IMPORTANCE_HIGH)
			.addAction(NotificationCompat.Action(
				android.R.drawable.sym_call_missed,
				"Cancel",
				PendingIntent.getActivity(this, 0, intent, PendingIntent.FLAG_CANCEL_CURRENT)))
			.addAction(NotificationCompat.Action(
				android.R.drawable.sym_call_outgoing,
				"OK",
				PendingIntent.getActivity(this, 0, intent, PendingIntent.FLAG_CANCEL_CURRENT)))

	val notificationManager = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager

	// Since android Oreo notification channel is needed.
	if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
		val channel = NotificationChannel(
			channelId,
			"Channel human readable title",
			NotificationManager.IMPORTANCE_DEFAULT)

		notificationManager.createNotificationChannel(channel)
	}

	notificationManager.notify(0, notificationBuilder.build())
```

---
