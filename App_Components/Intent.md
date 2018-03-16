# Intent – Ý định – liên kết các Component lại
**Intent là gì?**
1.	Là 1 tin nhắn yêu cầu hành động Component khác
2.	Có 2 loại:
    1.	Explicit Intent: tường minh, gọi trực tiếp tên ứng dụng
    2.	Implicit Intent: ẩn ý, gọi chung chung, ứng dụng nào chạy thì chạy

## Explicit Intent - tin nhắn cụ thể
Ví dụ: Gọi mở Activity khác, truyền giá trị (NAME_TEXT_VALUE, NAME_NUMBER_VALUE) vào:

```java
/* Gởi Explicite Intent
* put giá trị thông qua bundle
*/
private void sendBundle() {
	Bundle bundle = new Bundle();
	bundle.putString(ReceiverActivity.NAME_TEXT_VALUE, "giá trị chữ");
	bundle.putInt(ReceiverActivity.NAME_NUMBER_VALUE, 3333);

	Intent intent = new Intent(this, ReceiverActivity.class);
	intent.putExtras(bundle);
	startActivity(intent);
}

```

**ReciverActivity nhận giá trị Intent**
```java
/**
* Nhận Intent được gởi
*/
private void receiveIntent() {

    Intent intent = getIntent();
    Bundle bundle = intent.getExtras();
    String value1 = bundle.getString(MainActivity.NAME_TEXT_VALUE);
    int value2 = bundle.getInt(MainActivity.NAME_NUMBER_VALUE, 0);
}

```

## Implicit Intent - tin nhắn chung
```java
/**
* gởi implicit message
* gởi message đến ứng dụng nào sử dụng trong máy
*/
private void sendImplicit_Text() {
    // tạo tin nhắn chữ
    Intent sendIntent = new Intent();
    sendIntent.setAction(Intent.ACTION_SEND);
    sendIntent.putExtra(Intent.EXTRA_TEXT, "nội dung giá trị abc");
    sendIntent.setType("text/plain"); // "text/plain" MIME type

    // Verify that the intent will resolve to an activity
    if (sendIntent.resolveActivity(getPackageManager()) != null) {
        startActivity(sendIntent);  // gởi chữ đến ứng dụng nào sử dụng
    }
}

```

**Khai báo Activity nhận giá trị (Toàn bộ android)**
```xml
<!-- Khai báo Activity trong Manifresh: nhận intent từ app component đưa về -->
<activity ...="">
	<intent-filter>
		<action android:name="android.intent.action.SEND"><!-- hành động -->
			<category android:name="android.intent.category.DEFAULT"><!-- loại -->
				<data android:mimetype="text/plain"><!-- Kiểu dữ liệu -->
				</data>
			</category>
		</action>
	</intent-filter>
</activity>

```

**Lấy giá trị Intent được gởi đến Component(Activity)**
```java
Intent intent = getIntent();

String type = intent.getType();
String action = intent.getAction();
String extraText = intent.getStringExtra(Intent.EXTRA_TEXT);

```

# Intent Parcelable – Gởi Object cho App Component khác
•	Gởi 1 Object (Class) qua Intent
Có thể theo cách “PeopleModel implements Serializable”

## Các bước thực hiện
### 1.	Tạo Lớp chuyển:
```java
public class PeopleModel implements Parcelable {
	public enum Type implements Serializable {
        GOOD_MAN,
        BAD_MAN
    }
	
    private long id;
    private String name;
    private Type type;

    // TODO constructor, getter, setter

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        // Lưu các biến vào Parcel, khi đọc thì sẽ được đọc theo thứ tự khi ghi.
        dest.writeValue(id);
        dest.writeValue(name);
        dest.writeValue(type);
    }

    private PeopleModel (Parcel in){
        // Ghi thứ tự nào, đọc thứ tự đó
        this.id = (long) in.readValue(String.class.getClassLoader());
        this.name = (String) in.readValue(String.class.getClassLoader());
        this.type = (Type) in.readValue(String.class.getClassLoader());
    }

    //Để đọc 1 Parcelable cần tham số ClassLoader, ta có thể lấy bằng hàm getClassLoader()
    public static final Parcelable.Creator CREATOR = new Parcelable.Creator() {
        public PeopleModel createFromParcel(Parcel source) {
            return new PeopleModel(source);
        }

        public PeopleModel[] newArray(int size) {
            return new PeopleModel[size];
        }
    };
}

```
### 2.	Hàm gởi:
```java
private void sendPeople() {
	PeopleModel peopleModel = new PeopleModel(1, "dđm msdf", PeopleModel.Type.BAD_MAN);

	Intent intent = new Intent(this, ReceiveActivity.class);
	Bundle bundle = new Bundle();
	bundle.putParcelable("key", peopleModel);
	intent.putExtras(bundle);

	startActivity(intent);
}

```
### 3.	Hàm nhận:
```java
private void getPeople_fromIntent() {
	Bundle bundle = getIntent().getExtras();
	PeopleModel peopleModel = bundle.getParcelable("key");

	Toast.makeText(this, peopleModel.toString(), Toast.LENGTH_SHORT).show();
}

```

# Common Intents

1. [Alarm Clock](https://developer.android.com/guide/components/intents-common.html?hl=vi#Clock)
  1. [Create an alarm](https://developer.android.com/guide/components/intents-common.html?hl=vi#CreateAlarm)
  2. [Create a timer](https://developer.android.com/guide/components/intents-common.html?hl=vi#CreateTimer)
  3. [Show all alarms](https://developer.android.com/guide/components/intents-common.html?hl=vi#ShowAlarms)
2. [Calendar](https://developer.android.com/guide/components/intents-common.html?hl=vi#Calendar)
  1. [Add a calendar event](https://developer.android.com/guide/components/intents-common.html?hl=vi#AddEvent)
3. [Camera](https://developer.android.com/guide/components/intents-common.html?hl=vi#Camera)
  1. [Capture a picture or video and return it](https://developer.android.com/guide/components/intents-common.html?hl=vi#ImageCapture)
  2. [Start a camera app in still image mode](https://developer.android.com/guide/components/intents-common.html?hl=vi#CameraStill)
  3. [Start a camera app in video mode](https://developer.android.com/guide/components/intents-common.html?hl=vi#CameraVideo)
4. [Contacts/People App](https://developer.android.com/guide/components/intents-common.html?hl=vi#Contacts)
  1. [Select a contact](https://developer.android.com/guide/components/intents-common.html?hl=vi#PickContact)
  2. [Select specific contact data](https://developer.android.com/guide/components/intents-common.html?hl=vi#PickContactDat)
  3. [View a contact](https://developer.android.com/guide/components/intents-common.html?hl=vi#ViewContact)
  4. [Edit an existing contact](https://developer.android.com/guide/components/intents-common.html?hl=vi#EditContact)
  5. [Insert a contact](https://developer.android.com/guide/components/intents-common.html?hl=vi#InsertContact)
5. [Email](https://developer.android.com/guide/components/intents-common.html?hl=vi#Email)
  1. [Compose an email with optional attachments](https://developer.android.com/guide/components/intents-common.html?hl=vi#ComposeEmail)
6. [File Storage](https://developer.android.com/guide/components/intents-common.html?hl=vi#Storage)
  1. [Retrieve a specific type of file](https://developer.android.com/guide/components/intents-common.html?hl=vi#GetFile)
  2. [Open a specific type of file](https://developer.android.com/guide/components/intents-common.html?hl=vi#OpenFile)
7. [Local Actions](https://developer.android.com/guide/components/intents-common.html?hl=vi#Local)
  1. [Call a car](https://developer.android.com/guide/components/intents-common.html?hl=vi#CallCar)
8. [Maps](https://developer.android.com/guide/components/intents-common.html?hl=vi#Maps)
  1. [Show a location on a map](https://developer.android.com/guide/components/intents-common.html?hl=vi#ViewMap)
9. [Music or Video](https://developer.android.com/guide/components/intents-common.html?hl=vi#Music)
  1. [Play a media file](https://developer.android.com/guide/components/intents-common.html?hl=vi#PlayMedia)
  2. [Play music based on a search query](https://developer.android.com/guide/components/intents-common.html?hl=vi#PlaySearch)
10. [New Note](https://developer.android.com/guide/components/intents-common.html?hl=vi#NewNote)
  1. [Create a note](https://developer.android.com/guide/components/intents-common.html?hl=vi#CreateNote)
11. [Phone](https://developer.android.com/guide/components/intents-common.html?hl=vi#Phone)
  1. [Initiate a phone call](https://developer.android.com/guide/components/intents-common.html?hl=vi#DialPhone)
12. [Search](https://developer.android.com/guide/components/intents-common.html?hl=vi#Search)
  1. [Search in a specific app](https://developer.android.com/guide/components/intents-common.html?hl=vi#SearchOnApp)
  2. [Perform a web search](https://developer.android.com/guide/components/intents-common.html?hl=vi#SearchWeb)
13. [Settings](https://developer.android.com/guide/components/intents-common.html?hl=vi#Settings)
  1. [Open a specific section of Settings](https://developer.android.com/guide/components/intents-common.html?hl=vi#OpenSettings)
14. [Text Messaging](https://developer.android.com/guide/components/intents-common.html?hl=vi#Messaging)
  1. [Compose an SMS/MMS message with attachment](https://developer.android.com/guide/components/intents-common.html?hl=vi#SendMessage)
15. [Web Browser](https://developer.android.com/guide/components/intents-common.html?hl=vi#Browser)
  1. [Load a web URL](https://developer.android.com/guide/components/intents-common.html?hl=vi#ViewUrl)
16. [Verify Intents with the Android Debug Bridge](https://developer.android.com/guide/components/intents-common.html?hl=vi#AdbIntents)

## **See also**

1. [Intents and Intent Filters](https://developer.android.com/guide/components/intents-filters.html?hl=vi)

---
**Trích:**
•	http://developer.android.com/intl/vi/guide/components/intents-filters.html
•	http://developer.android.com/intl/vi/reference/android/os/Parcelable.html
