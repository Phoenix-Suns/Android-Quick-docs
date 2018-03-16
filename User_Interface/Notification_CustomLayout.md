# NOTIFICATION Custom Layout XML

Tạo giao diện notification qua XML layout file.

<!-- TOC -->

- [NOTIFICATION Custom Layout XML](#notification-custom-layout-xml)
    - [Bước 1: Tạo giao diện Notification: layout/custom_notification.xml](#bước-1-tạo-giao-diện-notification-layoutcustom_notificationxml)
        - [Tạo giao diện notification khi mở rộng: layout/custom_notification_expanded.xml (Tạo sao cũng được ))](#tạo-giao-diện-notification-khi-mở-rộng-layoutcustom_notification_expandedxml-tạo-sao-cũng-được-)
    - [Bước 2: hiện Notification trong Activity](#bước-2-hiện-notification-trong-activity)

<!-- /TOC -->

 ![image 1](/Images/notification_custom_1.jpg)

## Bước 1: Tạo giao diện Notification: layout/custom_notification.xml

```xml
...
<ImageView android:id="@+id/imageView" .../>
<TextView android:id="@+id/textView" .../>
<Button android:id="@+id/button_pause"
	android:text="Pause" .../>
<Button android:id="@+id/button_next"
	android:text="Next" .../>
```

### Tạo giao diện notification khi mở rộng: layout/custom_notification_expanded.xml (Tạo sao cũng được ))

## Bước 2: hiện Notification trong Activity

```java
/** Show Custom XML Notification  */
private void showNotification() {
	//---- Build Notification interface ----
    Notification.Builder builder = new Notification.Builder(this);
    builder.setSmallIcon(R.drawable.ic_stat_custom);
    builder.setAutoCancel(true); //Đóng khi chạm

    //---Notification Click => Use Intent => Open Activity---
    Intent i = new Intent(this, MainActivity.class);
    i.setFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
    PendingIntent pi = PendingIntent.getActivity(this, 0, i, PendingIntent.FLAG_UPDATE_CURRENT);
    builder.setContentIntent(pi);

	//---Set XML Layout for Notification
	Notification notification = builder.build();
	RemoteViews contentView1 = new RemoteViews(getPackageName(),
		R.layout.custom_notification); //Nomal Interface
	RemoteViews contentView2 = new RemoteViews(getPackageName(), 
		R.layout.custom_notification_expanded); //Expanded Interface
	notification.contentView = contentView1; // replace with builder.setCustomContentView
	notification.bigContentView = contentView2;
		
	//--- Setup View inside XML Layout ---
	contentView1.setTextViewText(R.id.textView, "Try your best!");
	contentView1.setOnClickPendingIntent(R.id.button_next, pi); //Set Intent for button_next
		
    // Show Notification
    NotificationManager manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
    manager.notify(0, notification);
}
```