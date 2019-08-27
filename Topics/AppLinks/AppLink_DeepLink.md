# Deep Link: Open app when Open Link

- [Deep Link: Open app when Open Link](#deep-link-open-app-when-open-link)
  - [Simple Start](#simple-start)
    - [Step 1: Create Intent in Manifesh file](#step-1-create-intent-in-manifesh-file)
    - [Step 2: Catch link on Activity](#step-2-catch-link-on-activity)
  - [Reference](#reference)

## Simple Start

### Step 1: Create Intent in Manifesh file

```xml
<manifest ...>
    <application ...>
        <activity android:name=".ui.others.ReceiveURLActivity">
            <intent-filter android:label="@string/app_name">
                <action android:name="android.intent.action.VIEW" />

                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />

                <data
                    android:scheme="http"
                    android:host="beta.oneway.vn"
                    android:pathPrefix="/tin-tuc" />

                <data
                    android:scheme="http"
                    android:host="beta.oneway.vn"
                    android:pathPrefix="/video" />

            </intent-filter>
        </activity>
    </application>
</manifest>
```

### Step 2: Catch link on Activity

```java
class ReceiveURLActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // get UID from LINK
        val appLinkAction = intent.action
        val appLinkData: Uri? = intent.data

        if (appLinkAction == Intent.ACTION_VIEW) {

            /*if (appLinkData?.pathSegments == null || appLinkData.pathSegments?.size == 0) {
                // Go Home
                startActivity(Intent(this, SplashActivity::class.java))
            }*/

            if (appLinkData != null) {

                var uidStr : String? = null
                appLinkData.lastPathSegment?.also { lastPath ->
                    //lastPath = khong-chiu-nghi-ngoi-47902.html
                    uidStr = lastPath.substring(
                            lastPath.lastIndexOf('-') + 1,
                            lastPath.lastIndexOf('.')
                    )
                }

                when (appLinkData.pathSegments[0]) {
                    "tin-tuc" -> {
                        uidStr?.let {
                            val news = News().apply { this.uid = it.toInt() }
                            // Start Activity
                        }
                    }
                    "video" -> {
                        uidStr?.let {
                            val video = Videos().apply { this.uid = it.toInt() }
                            // Start Activity
                        }
                    }
                }
            }
        }

        finish()
    }
}
```

---

## Reference

- <https://developer.android.com/training/app-links/deep-linking.html>
- <https://developer.android.com/studio/write/app-link-indexing>
  