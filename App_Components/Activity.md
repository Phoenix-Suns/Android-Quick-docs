# Activity – Màn hình hiển thị, tương tác (giống 1 cửa sổ)

<!-- TOC -->

- [Activity – Màn hình hiển thị, tương tác (giống 1 cửa sổ)](#activity-%e2%80%93-m%c3%a0n-h%c3%acnh-hi%e1%bb%83n-th%e1%bb%8b-t%c6%b0%c6%a1ng-t%c3%a1c-gi%e1%bb%91ng-1-c%e1%bb%ada-s%e1%bb%95)
		- [Khai báo tồn tại trong Mainifesh:](#khai-b%c3%a1o-t%e1%bb%93n-t%e1%ba%a1i-trong-mainifesh)
		- [Vòng đời](#v%c3%b2ng-%c4%91%e1%bb%9di)
			- [Ghi nhớ:](#ghi-nh%e1%bb%9b)
		- [Khởi động Activity trong Component(Activity khác):](#kh%e1%bb%9fi-%c4%91%e1%bb%99ng-activity-trong-componentactivity-kh%c3%a1c)
			- [1 số LauchMode (chế độ mở Activity) trong Manifesh](#1-s%e1%bb%91-lauchmode-ch%e1%ba%bf-%c4%91%e1%bb%99-m%e1%bb%9f-activity-trong-manifesh)
		- [Mở Activity Lấy kết quả trả về:](#m%e1%bb%9f-activity-l%e1%ba%a5y-k%e1%ba%bft-qu%e1%ba%a3-tr%e1%ba%a3-v%e1%bb%81)
		- [Mở Activity Dạng Dialog (cửa sổ):](#m%e1%bb%9f-activity-d%e1%ba%a1ng-dialog-c%e1%bb%ada-s%e1%bb%95)

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
![/Images/activity_lifecycle.png](/Images/activity_lifecycle.png)


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

#### 1 số LauchMode (chế độ mở Activity) trong Manifesh

- Standard: nhiều Activity tồn tại (khởi tạo nhiều lần)
- SingleTop: nhiều Activity tồn tại (khởi tạo nhiều lần). Nếu Activity đã khởi tạo ở trên cùng, chỉ gọi hàm onNewIntent. Nếu không trên cùng thì khởi tạo lại.
- SingleTask: Chỉ 1 Activity tồn tại. Nếu Activity đã khởi tạo, destroy các Activity sau nó.
- singleInstance: Chỉ 1 Activity tồn tại, Activity chạy Task khác. Nếu Activity đã khởi tạo, sẽ nhảy lên trên.

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
