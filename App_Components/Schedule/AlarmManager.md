# Alarm Manager

Lên lịch, hẹn giờ để thực hiện tác vụ. Kết nối với Alarm Service của hệ thống

```java
// ===== Gọi Alarm sau 10s =====
// Get AlarmManager instance
val alarmManager = getSystemService(Context.ALARM_SERVICE) as AlarmManager
// Intent part
val intent = Intent(this, AlarmReceiver::class.java)
intent.action = "FOO_ACTION"
intent.putExtra("KEY_FOO_STRING", "Medium AlarmManager Demo")
val pendingIntent = PendingIntent.getBroadcast(this, 0, intent, 0)

//PendingIntent.getBroadcast() — Applicable to AlarmManager
//PendingIntent.getActivity()
//PendingIntent.getActivities()
//PendingIntent.getService()

// FLAG
//FLAG_UPDATE_CURRENT
//FLAG_CANCEL_CURRENT
//FLAG_IMMUTABLE

// Alarm time
val ALARM_DELAY_IN_SECOND = 10
val alarmTimeAtUTC = System.currentTimeMillis() + ALARM_DELAY_IN_SECOND * 1_000L
// Set with system Alarm Service
// Other possible functions: setExact() / setRepeating() / setWindow(), etc
alarmManager.setExactAndAllowWhileIdle(AlarmManager.RTC_WAKEUP, alarmTimeAtUTC, pendingIntent)

//set() – lên lịch thời gian, nhưng cho phép Android linh động thời điểm kích hoạt
//setExact()- yêu cầu Android kích hoạt chính xác thời điểm
// Tránh không chạy khi Phone ngủ
//setAndAllowWhileIdle(...)
//setExactAndAllowWhileIdle(...)



// ===== Nhận data trả về =====
alarmManager.setExact(AlarmManager.RTC_WAKEUP, alarmTime, tagStr, object : AlarmManager.OnAlarmListener {
  override fun onAlarm() {
    Toast.makeText(this, "AlarmManager.OnAlarmListener", Toast.LENGTH_LONG).show()
  }
}, null)


// ===== Khởi tạo BroadcastReceiver, nhận data trả về =====
class AlarmReceiver : BroadcastReceiver() {
  override fun onReceive(context: Context, intent: Intent) {
    // Is triggered when alarm goes off, i.e. receiving a system broadcast
    if (intent.action == "FOO_ACTION") {
      val fooString = intent.getStringExtra("KEY_FOO_STRING")
      Toast.makeText(context, fooString, Toast.LENGTH_LONG).show()
    }
  }
}

// Khai báo trong manifest
<manifest>
  <application>
    <receiver
        android:name=".AlarmReceiver"
        android:enabled="true"
        android:exported="true">
    </receiver>
  </application>
</manifest>


// ===== Huỷ Alarm =====
val onAlarmListener = object : AlarmManager.OnAlarmListener {
  override fun onAlarm() {
     toast("Alarm goes off.")
  }
}
alarmManager.cancel(onAlarmListener)


// Huỷ bằng request code
val requestCode = 0
val alarmManager;

fun getAlarmIntent(): Intent { 
  val intent = Intent(this, AlarmReceiver::class.java)
  intent.action = ALARM_RECEIVER_ACTION
  return intent
}
fun createAlarm() {
  val sender = PendingIntent.getBroadcast(fooActivityContext, requestCode, getAlarmIntent(), 0)
  alarmManager = getSystemService(Context.ALARM_SERVICE) as AlarmManager
  alarmManager.setExactAndAllowWhileIdle(AlarmManager.RTC_WAKEUP, alarmTimeAtUTC, pendingIntent)
}
fun cancelAlarm(){
   val sender = PendingIntent.getBroadcast(fooActivityContext, requestCode, getAlarmIntent(), 0)
   alarmManager.cancel(sender)
}
```

---

## Reference

- https://vntalking.com/alarm-manager-trong-android.html

