# Floating Message, Floating Activity, Bubble Chat

## Sample

```kotlin
const val NOTIFICATION_CHANNEL_ID = "NOTIFICATION_CHANNEL_ID"

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        //https://developer.android.com/guide/topics/ui/conversations#api-notifications
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.P) {
            createNotificationChannel()
        }

        findViewById<Button>(R.id.buttonShowBubble).setOnClickListener {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.P) {
                // Enable in app detail notification
                // https://proandroiddev.com/deep-dive-into-android-q-bubbles-7d0e794f0a18
                // https://viblo.asia/p/android-11-bubble-api-is-finally-here-eW65G17YZDO
                showBubble()
            }
        }
    }

    @RequiresApi(Build.VERSION_CODES.P)
    private fun showBubble() {
        // Create bubble intent
        val target = Intent(getContext(), BubbleActivity::class.java)
        val bubbleIntent = PendingIntent.getActivity(
            getContext(),
            0,
            target,
            PendingIntent.FLAG_UPDATE_CURRENT
        )

        // Create bubble metadata
        val bubbleData = Notification.BubbleMetadata.Builder(
            bubbleIntent,
            Icon.createWithResource(this, R.drawable.ic_baseline_account_circle_24)
        )
            .setDesiredHeight(600)
            .setAutoExpandBubble(true)
            .setSuppressNotification(true)
            .build()

        // Create notification
        val chatPartner = Person.Builder()
            .setName("Chat partner")
            .setImportant(true)
            .build()

        val builder = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            Notification.Builder(this, NOTIFICATION_CHANNEL_ID)
        } else {
            Notification.Builder(this)
        }

        val intent = Intent(this, BubbleActivity::class.java)
        val requestCode = 100
        val flags = 0
        val contentIntent = PendingIntent.getActivity(this, requestCode, intent, flags)
        builder.setContentIntent(contentIntent)
            .setSmallIcon(R.drawable.ic_launcher_foreground)
            .setBubbleMetadata(bubbleData)
            .addPerson(chatPartner)
            .setCategory(Notification.CATEGORY_MESSAGE)
            .setShortcutId("1000")
            .setLocusId(LocusId("1000"))
            .style = Notification.MessagingStyle(chatPartner)
            .setGroupConversation(false)

        val notificationManager =
            getContext().getSystemService(NOTIFICATION_SERVICE) as NotificationManager
        // notificationId is a unique int for each notification that you must define
        notificationManager.notify(1000, builder.build())
    }

    @RequiresApi(Build.VERSION_CODES.P)
    private fun createNotificationChannel() {
        //conversation notification

        // Create the NotificationChannel, but only on API 26+ because
        // the NotificationChannel class is new and not in the support library
        val name: CharSequence = "notification name"
        val description = "notification description"

        // The importance must be IMPORTANCE_HIGH to show Bubbles.
        val importance = NotificationManager.IMPORTANCE_HIGH
        val channel = NotificationChannel(NOTIFICATION_CHANNEL_ID, name, importance)
        channel.description = description

        // Register the channel with the system; you can't change the importance
        // or other notification behaviors after this
        val notificationManager: NotificationManager = getContext().getSystemService(
            NotificationManager::class.java
        )
        notificationManager.createNotificationChannel(channel)

        try {
            updateShortcuts()
        } catch (e: IOException) {
            e.printStackTrace()
        }
    }

    private fun getContext(): Context {
        return this
    }

    @RequiresApi(Build.VERSION_CODES.P)
    @Throws(IOException::class)
    private fun updateShortcuts() {
        // Create a dynamic shortcut for each of the contacts.
        // The same shortcut ID will be used when we show a bubble notification.
        val set: HashSet<String> = HashSet()
        set.add("com.example.android.bubbles.category.TEXT_SHARE_TARGET")
        val shortcutInfo = ShortcutInfo.Builder(getContext(), "1000")
            .setLocusId(LocusId("1000"))
            .setActivity(ComponentName(getContext(), MainActivity::class.java))
            .setShortLabel("contact name")
            .setIcon(Icon.createWithResource(this, R.drawable.ic_baseline_account_circle_24))
            .setLongLived(true)
            .setCategories(set)
            .setIntent(
                Intent(getContext(), BubbleActivity::class.java)
                    .setAction(Intent.ACTION_VIEW)
                    .setData(
                        Uri.parse(
                            "https://android.example.com/chat/\${contact.id}"
                        )
                    )
            )
            .setPerson(
                Person.Builder()
                    .setName("contact name")
                    .setIcon(
                        Icon.createWithResource(this, R.drawable.ic_baseline_account_circle_24)
                    )
                    .build()
            )
            .build()

        val shortcuts: MutableList<ShortcutInfo> = ArrayList()
        shortcuts.add(shortcutInfo)
        (getContext().getSystemService(Context.SHORTCUT_SERVICE) as ShortcutManager).addDynamicShortcuts(
            shortcuts
        )
    }
}
```

## Preference

- <https://proandroiddev.com/deep-dive-into-android-q-bubbles-7d0e794f0a18>
- <https://viblo.asia/p/android-11-bubble-api-is-finally-here-eW65G17YZDO>
- <https://developer.android.com/guide/topics/ui/conversations#api-notifications>
