# Activity – Màn hình hiển thị, tương tác (giống 1 cửa sổ)

<!-- TOC -->

- [Activity – Màn hình hiển thị, tương tác (giống 1 cửa sổ)](#activity--màn-hình-hiển-thị-tương-tác-giống-1-cửa-sổ)
        - [Khai báo tồn tại trong Mainifesh:](#khai-báo-tồn-tại-trong-mainifesh)
        - [Vòng đời](#vòng-đời)
            - [Ghi nhớ:](#ghi-nhớ)
        - [Khởi động Activity trong Component(Activity khác):](#khởi-động-activity-trong-componentactivity-khác)
        - [Mở Activity Lấy kết quả trả về:](#mở-activity-lấy-kết-quả-trả-về)
        - [Mở Activity Dạng Dialog (cửa sổ):](#mở-activity-dạng-dialog-cửa-sổ)

<!-- /TOC -->

### Khai báo tồn tại trong Mainifesh:
```xml
<manifest ...="">
	<application ...="">
		<activity android:name=".ExampleActivity">
			<!-- hiển thị đầu tiên -->
			<intent-filter>
				<action android:name="android.intent.action.MAIN">
					<category android:name="android.intent.category.LAUNCHER"/>
				</action>
			</intent-filter>
		</activity>
	</application>
	...
</manifest>

```
### Vòng đời
![https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/activity_lifecycle.png](https://github.com/luunghiatran/Android-Quick-docs/blob/master/Images/activity_lifecycle.png)


#### Ghi nhớ: 
**•	Create <> Destroy -> Start <> Stop -> Resume <> Pause**
•	Thu hồi tài nguyên (xóa hoàn toàn): Pause -> Stop -> Destroy
•	Xuống chạy nền (bị Activity khác giành): Pause -> Stop
•	Bị dialog giành (vẫn còn hiển thị): Pause

### Khởi động Activity trong Component(Activity khác):
```java
Intent intent = new Intent(this, SignInActivity.class);
// đặt thông tin Cần chuyển vào Intent
startActivity(intent);

```
### Mở Activity Lấy kết quả trả về:
```java
private int REQUEST_CODE_PICK_CONTACT = 200;
private static final int REQUEST_CODE_PICK_TEXT = 201;

//onCreate() {... pickContact(); }

/**
 * Mở activity lấy contact
 */
private void pickContact() {
	// Create an intent to "pick" a contact, as defined by the content provider URI
	Intent intent = new Intent(Intent.ACTION_PICK, ContactsContract.Contacts.CONTENT_URI);
	startActivityForResult(intent, REQUEST_CODE_PICK_CONTACT);
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
	// If the request went well (OK) and the request was REQUEST_CODE_PICK_CONTACT
	if (requestCode == REQUEST_CODE_PICK_CONTACT) {
		if (resultCode == Activity.RESULT_OK) {
			// Perform a query to the contact's content provider for the contact's name
			Cursor cursor = getContentResolver().query(data.getData(),
					new String[]{ContactsContract.Contacts.DISPLAY_NAME}, null, null, null);
			if (cursor.moveToFirst()) {
				// True if the cursor is not empty
				int columnIndex = cursor.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME);
				String name = cursor.getString(columnIndex);
				// Do something with the selected contact's name...
				Toast.makeText(this, name, Toast.LENGTH_LONG).show();
			}
		}
	}
	// lấy result từ activity của mình
	if (requestCode == REQUEST_CODE_PICK_TEXT){
		if (resultCode == Activity.RESULT_OK){
			String value = data.getStringExtra(GetTextActivity.EXTRA_VALUE);

			Toast.makeText(this, value, Toast.LENGTH_LONG).show();
		}
	}
}

```
### Mở Activity Dạng Dialog (cửa sổ):
```xml
<manifest ...="">
	<application ...="">
		<activity android:theme="@android:style/Theme.Dialog"/>
	</application>
	...
</manifest>

```
