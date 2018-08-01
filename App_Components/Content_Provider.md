# Content Providers – Lưu trữ nội dung, dữ liệu

![/Images/content_provider.jpg](/Images/content_provider.jpg)

<!-- TOC -->

- [Content Providers – Lưu trữ nội dung, dữ liệu](#content-providers--lưu-trữ-nội-dung-dữ-liệu)
    - [Content Providers là gì](#content-providers-là-gì)
    - [Content URIs – Cấu trúc dữ liệu](#content-uris--cấu-trúc-dữ-liệu)
    - [Các phương thức truy vấn](#các-phương-thức-truy-vấn)
    - [Cấu trúc Package(Thư mục) đề xuất](#cấu-trúc-packagethư-mục-đề-xuất)
    - [Tạo Content Provider](#tạo-content-provider)
        - [Các bước thực hiện](#các-bước-thực-hiện)
        - [Bước 1: Khởi tạo CSDL (SQLite) truy vấn](#bước-1-khởi-tạo-csdl-sqlite-truy-vấn)
            - [Class Note, binding bảng Note](#class-note-binding-bảng-note)
            - [Class NoteData, Xử lý truy vấn trong bảng Note](#class-notedata-xử-lý-truy-vấn-trong-bảng-note)
            - [Class BaseDataHandle, Khởi tạo CSDL Sqlite](#class-basedatahandle-khởi-tạo-csdl-sqlite)
        - [Bước 2: Tạo Content Provider Class, định nghĩa Content URL để truy xuất](#bước-2-tạo-content-provider-class-định-nghĩa-content-url-để-truy-xuất)
            - [Content Provider Class, xử lý khi nhận truy vấn từ Content Url](#content-provider-class-xử-lý-khi-nhận-truy-vấn-từ-content-url)
        - [Bước 3: Đăng kí trong Mainifesh](#bước-3-đăng-kí-trong-mainifesh)
        - [Bước 4: Kế thừa truy vấn, trong lớp kế thừa Content Provider](#bước-4-kế-thừa-truy-vấn-trong-lớp-kế-thừa-content-provider)
        - [Bước 5: Test, chạy thử kiểm tra](#bước-5-test-chạy-thử-kiểm-tra)
            - [Hiển thị ListView trong App khác](#hiển-thị-listview-trong-app-khác)
            - [Thêm Note từ App khác](#thêm-note-từ-app-khác)
            - [Lấy Note thông qua Id từ App khác](#lấy-note-thông-qua-id-từ-app-khác)

<!-- /TOC -->

## Content Providers là gì

* Truy cập, chỉnh sửa dữ liệu (nội dung) có cấu trúc. Dữ liệu: **Databases, files, network…**
* Tạo khi cần **chia sẽ dữ liệu với ứng dụng khác**.
* Android cung cấp Content Providers cho
  * Audio
  * Video
  * Image
  * Personal Contact Infomation

## Content URIs – Cấu trúc dữ liệu

```xml
<prefix>://<authority>/<data_type>/<id>
```

* Prefix: luôn là **content://**
* ```<authority>```: Chỉ định **tên cụ thể** của Content Provider (VD: contacts, browser,…). Đối với một số Content Provider khác bạn sẽ phải chỉ định tên đầy đủ (VD: com.laptrinhtuduy.statusprovider).
* ```<data_type>```: Chỉ rõ **kiểu dữ liệu** (VD: Để lấy tất cả các liên hệ trong Contacts Content Provider thì kiểu dữ liệu là people và URI sẽ là: content://contacts/people.
* ```<id>``` : Chỉ định rõ **một record** (VD: Nếu bạn muốn lấy địa chỉ liên lạc thứ 5 trong Contacts Content Provider thì URI sẽ là: content://contacts/people/5.

## Các phương thức truy vấn

* **onCreate()**: Phương thức này được gọi khi Provider được bắt đầu.
* **query()**: Truy vấn, lấy dữ liệu. Trả vềCursor.
* **insert()**: chèn một dòng.
* **delete()**: xóa một dòng.
* **update()**: cập nhật một dòng.
* **getType()**: Phương thức trả về kiểu MIME của dữ liệu tại các URI.

## Cấu trúc Package(Thư mục) đề xuất

* data
  * model
    * Note.java
  * AppProvider.java
  * BaseDataHandle.java
  * NoteData.java

## Tạo Content Provider

### Các bước thực hiện

1. Khởi tạo CSDL (SQLite) truy vấn.
    2. Tạo lớp CSDL
    3. Định nghĩa Database Schema
    4. Creating Database
    5. Updating Database
2. Tạo Content Provider Class, định nghĩa Content URL để truy xuất
3. Đăng kí trong Mainifesh
4. Kế thừa truy vấn, trong lớp kế thừa Content Provider
5. Test, chạy thử kiểm tra

### Bước 1: Khởi tạo CSDL (SQLite) truy vấn

#### Class Note, binding bảng Note

```java
/** Hiển thị dòng trong Table Note */
public class Note {
    private long _id;
    private String description;
    // Getter, Setter, Constructor
}

```

#### Class NoteData, Xử lý truy vấn trong bảng Note

```java
/** Chứa hàm truy vấn bảng Note */
public class NoteData {
    // TABLE NAME
    public static final String TABLE_NOTE = "notes";
    // COLUMN NAME
    public static final String COL_ID = "_id";
    public static final String COL_DESCRIPTION = "description";
    // STATEMENT
    public static final String NOTE_CREATE_STATEMENT =
            "CREATE TABLE " + TABLE_NOTE + "(" +
                    COL_ID + " INTEGER  PRIMARY KEY  AUTOINCREMENT, " +
                    COL_DESCRIPTION + " TEXT " +
                    ")";
    /*
    Các hàm truy vấn
     */

	/* Đổi Note to ContentValues */
    private static void noteToContentValues(Note note, ContentValues values) {
		//bindding table common value
        //BaseDataHandle.baseDataObjectToContentValues(note, values); 
        values.put(COL_ID, note.get_id());
        values.put(COL_DESCRIPTION, note.getDescription());
    }

    /** Đổi Cursor to Note */
    private static void cursorToNote(Cursor cursor, Note note) {
        //BaseDataHandle.cursorToBaseDataObject(cursor, note);
        int id = cursor.getInt(cursor.getColumnIndex(COL_ID));
        String description = cursor.getString(cursor.getColumnIndex(COL_DESCRIPTION));
        note.set_id(id);
        note.setDescription(description);
    }

    /** Tạo mới Note, sử dụng 2 hànm ở trên */
    public static long add(Context context, Note note) {
        ContentValues values = new ContentValues();
        noteToContentValues(note, values);

        return BaseDataHandle.insert(context, TABLE_NOTE, values);
    }
}
```

#### Class BaseDataHandle, Khởi tạo CSDL Sqlite

```java
/** Xử lý, khởi tạo SQLite Database */
public class BaseDataHandle extends SQLiteOpenHelper {

    public static final int DATABASE_VERSION = 1;
    public static final String DATABASE_NAME = "test_db";

    public BaseDataHandle(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        try {

            // Tạo bảng NOTE
            db.execSQL(NoteData.NOTE_CREATE_STATEMENT);

        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // Xóa bảng NOTE
        db.execSQL("DROP TABLE IF EXISTS " + NoteData.TABLE_NOTE);

        // Tạo lại
        onCreate(db);
    }
    
    // Xử lý khác
}
```

### Bước 2: Tạo Content Provider Class, định nghĩa Content URL để truy xuất

#### Content Provider Class, xử lý khi nhận truy vấn từ Content Url

```java
/** Xử lý khi nhận Content Url */
public class AppProvider extends ContentProvider {
    //<prefix>://<authority>/<data_type>/<id>

    private static final String AUTHORITY = "com.imark.nghia.nghiacontentprovider.provider";

    private static final String NOTE_BASE_PATH = "notes";
    private static final Uri NOTE_CONTENT_URI = Uri.parse(
        "content://" + AUTHORITY + "/" + NOTE_BASE_PATH
    );

    private static final int CODE_NOTES = 100;
    private static final int CODE_NOTE_ID = 110;
    private static final UriMatcher sUriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
    static {
        // để kiểm tra Loại URI
        // Ví dụ: Nhận "NOTE_BASE_PATH" => loại CODE_NOTES
        sUriMatcher.addURI(AUTHORITY, NOTE_BASE_PATH, CODE_NOTES);
        sUriMatcher.addURI(AUTHORITY, NOTE_BASE_PATH + "/#", CODE_NOTE_ID);
    }

    // Loại trả về
    private static final String TYPE_NOTE_CONTENT_ITEM = ContentResolver.CURSOR_ITEM_BASE_TYPE + "/mt-note";
    private static final String TYPE_NOTE_CONTENT = ContentResolver.CURSOR_DIR_BASE_TYPE + "mt-note";

    //...
}
```

### Bước 3: Đăng kí trong Mainifesh

```xml
<manifest ...>
    <application ...>

        <!--Khai báo Provider-->
        <provider
            android:authorities="com.imark.nghia.nghiacontentprovider.provider"
            android:name=".data.AppProvider"
            android:multiprocess="true" />

    </application>
</manifest>

```

### Bước 4: Kế thừa truy vấn, trong lớp kế thừa Content Provider

```java
/** Xử lý khi nhận Content Url */
public class AppProvider extends ContentProvider {

    //...
    private SQLiteDatabase mDB;
	
    @Override
    public boolean onCreate() {
        // Khởi tạo Database
        mDB = new BaseDataHandle(getContext()).getWritableDatabase();
        return true;
    }

    @Nullable
    @Override
    public Cursor query(@NonNull Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {

        // Dùng QueryBuilder để truy vấn
        SQLiteQueryBuilder queryBuilder = new SQLiteQueryBuilder();
        queryBuilder.setTables(NoteData.TABLE_NOTE);  //add Select table

        // Phân loại Uri
        int uriType = sUriMatcher.match(uri);
        switch (uriType) {
            case CODE_NOTES:
                // not Where
                break;
            case CODE_NOTE_ID:
                queryBuilder.appendWhere(NoteData.COL_ID + "=" + uri.getLastPathSegment());  // add Where
                break;
            default:
                throw new IllegalArgumentException("Unknown URI");
        }

        Cursor cursor = queryBuilder.query(mDB, projection, selection, selectionArgs, null, null, sortOrder);

        // Notify change to Client
        if (getContext() != null)
            cursor.setNotificationUri(getContext().getContentResolver(), uri);

        return cursor;
    }

    @Nullable
    @Override
    public String getType(Uri uri) {

        // Mỗi loại Uri =&gt; loại khác nhau
        int uriType = sUriMatcher.match(uri);
        switch (uriType) {
            case CODE_NOTES:
                return TYPE_NOTE_CONTENT;
            case CODE_NOTE_ID:
                return TYPE_NOTE_CONTENT_ITEM;
            default:
                throw new IllegalArgumentException("Unknown URI");
        }
    }

    @Nullable
    @Override
    public Uri insert(@NonNull Uri uri, ContentValues values) {

        long rowID = mDB.insert(NoteData.TABLE_NOTE, null, values);
        if(rowID &gt; 0){
            // thêm thành công, trả về Uri
            Uri resultUri = ContentUris.withAppendedId(NOTE_CONTENT_URI, rowID);

            // Notify change to Client (Caller)
            if (getContext() != null)
                getContext().getContentResolver().notifyChange(resultUri, null);

            return resultUri;
        }
        throw new SQLException("Failed to add a record into " + uri);
    }

    @Override
    public int delete(@NonNull Uri uri, String selection, String[] selectionArgs) {
        int rowAffected = 0;  // dòng ảnh hưởng

        int uriType = sUriMatcher.match(uri);
        switch (uriType) {
            case CODE_NOTES:
                rowAffected = mDB.delete(NoteData.TABLE_NOTE, selection, selectionArgs);
                break;
            case CODE_NOTE_ID:
                // build where, check selection
                String id = uri.getLastPathSegment();
                String where = NoteData.COL_ID + "=" + id;
                if (!TextUtils.isEmpty(selection)){
                    // Selection not null =&gt; Where ... and
                    where += " AND " + selection;
                }

                rowAffected = mDB.delete(NoteData.TABLE_NOTE, where, selectionArgs);
                break;
            default:
                throw new IllegalArgumentException("Unknown URI");
        }

        // Notify change to Client (Caller)
        if (getContext() != null) {
            getContext().getContentResolver().notifyChange(uri, null);
        }

        return rowAffected;
    }

    @Override
    public int update(@NonNull Uri uri, ContentValues values, String selection, String[] selectionArgs) {
        int rowAffected = 0;  // dòng ảnh hưởng

        int uriType = sUriMatcher.match(uri);
        switch (uriType) {
            case CODE_NOTES:
                rowAffected = mDB.update(NoteData.TABLE_NOTE, values, selection, selectionArgs);
                break;
            case CODE_NOTE_ID:
                // build where, check selection
                String id = uri.getLastPathSegment();
                String where = NoteData.COL_ID + "=" + id;
                if (!TextUtils.isEmpty(selection)){
                    // Selection not null =&gt; Where ... and
                    where += " AND " + selection;
                }

                rowAffected = mDB.update(NoteData.TABLE_NOTE, values, where, selectionArgs);
                break;
            default:
                throw new IllegalArgumentException("Unknown URI");
        }

        // Notify change to Client (Caller)
        if (getContext() != null) {
            getContext().getContentResolver().notifyChange(uri, null);
        }

        return rowAffected;
    }
}
```

![/Images/content-provider-1-225x300.jpg](/Images/content-provider-1-225x300.jpg)
![/Images/content-provider-2-225x300.jpg](/Images/content-provider-2-225x300.jpg)

### Bước 5: Test, chạy thử kiểm tra

#### Hiển thị ListView trong App khác

```java
//ListView mListView_data;
//ArrayAdapter mAdapterData;
//ArrayList mArrayData;
private void mListView_data_init() {
    mArrayData = new ArrayList<>();

    // Triệu hồi AppProvider lấy dữ liệu
    Cursor cursor = getContentResolver().query(
            Uri.parse("content://com.imark.nghia.nghiacontentprovider.provider/notes"),
            null, null, null, null
    );
    // Uri.withAppendedPath(URI, ID);  // thêm Id vào URI. Ví dụ: content://.../.../id
    // Cursor to List
    while (cursor.moveToNext()) {
        String description = cursor.getString(cursor.getColumnIndex("description"));
        mArrayData.add(description);
    }
    cursor.close();

    mAdapterData = new ArrayAdapter&lt;String&gt;(getBaseContext(), android.R.layout.simple_list_item_1, mArrayData);
    mListView_data.setAdapter(mAdapterData);
}
```

#### Thêm Note từ App khác

```java
//EditText mEditText_description;
//Button mButton_save;
View.OnClickListener mButton_save_onClick = new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        ContentValues values = new ContentValues();
        values.put("description", mEditText_description.getText() + "");  // description on Note

        // triệu hồi AppProvider thực hiện qua ContentUrl
        Uri uri = getContentResolver().insert(
                Uri.parse("content://com.imark.nghia.nghiacontentprovider.provider/notes"),
                values);

        // hiển thị kết quả trả về
        Toast.makeText(getBaseContext(), uri.toString(), Toast.LENGTH_LONG).show();
        mListView_data_init();
    }
};
```

#### Lấy Note thông qua Id từ App khác

```java
// thêm Id vào URI. Ví dụ: content://.../.../id
// id lấy là "1"
Uri uri = Uri.withAppendedPath(
        Uri.parse("content://com.imark.nghia.nghiacontentprovider.provider/notes"),
        "1"
);

// Triệu hồi AppProvider lấy dữ liệu
Cursor cursor = getContentResolver().query(
        uri,
        null, null, null, null
);
while (cursor.moveToNext()) {
    String description = cursor.getString(cursor.getColumnIndex("description"));
    mArrayData.add(description);
}
cursor.close();
```

---
**Tham khảo:**
http://developer.android.com/guide/topics/providers/content-providers.html
https://laptrinhtuduy.wordpress.com/2014/04/28/content-providers-trong-android/
http://www.tutorialspoint.com/android/android_content_providers.htm
http://code.tutsplus.com/tutorials/android-fundamentals-working-with-content-providers–mobile-5549