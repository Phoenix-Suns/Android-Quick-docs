# Room - SQLite Database Handle

- [Room - SQLite Database Handle](#room---sqlite-database-handle)
  - [Step by Step Setup](#step-by-step-setup)
    - [1. Update gradle File](#1-update-gradle-file)
    - [2. Khởi tạo App Database:](#2-kh%e1%bb%9fi-t%e1%ba%a1o-app-database)
    - [3. Khởi tạo Table Note](#3-kh%e1%bb%9fi-t%e1%ba%a1o-table-note)
    - [4. Tạo Note DAO (Data Access Object)](#4-t%e1%ba%a1o-note-dao-data-access-object)
    - [5. Sử dụng Hàm Trên UI](#5-s%e1%bb%ad-d%e1%bb%a5ng-h%c3%a0m-tr%c3%aan-ui)
      - [Chạy thông qua Task](#ch%e1%ba%a1y-th%c3%b4ng-qua-task)
      - [Test Trên UI](#test-tr%c3%aan-ui)

## Step by Step Setup

### 1. Update gradle File

```json
====== build.gradle (Module: app) ========
dependencies {
    //...
    // Room components
    implementation "android.arch.persistence.room:runtime:$rootProject.roomVersion"
    annotationProcessor "android.arch.persistence.room:compiler:$rootProject.roomVersion"
    androidTestImplementation "android.arch.persistence.room:testing:$rootProject.roomVersion"

    // Lifecycle components
    implementation "android.arch.lifecycle:extensions:$rootProject.archLifecycleVersion"
    annotationProcessor "android.arch.lifecycle:compiler:$rootProject.archLifecycleVersion"
}

======== build.gradle (Project: ProjectName) =========
ext {
   roomVersion = '1.1.1'
   archLifecycleVersion = '1.1.1'
}
```

### 2. Khởi tạo App Database:

**Data/AppDatabase.java**

```java
@Database(version = DATABASE_VERSION, entities = {
        Note.class  //Khai báo Class Entity
})
public abstract class AppDatabase extends RoomDatabase {
    public static final int DATABASE_VERSION = 2;
    public static final String DATABASE_NAME = "note.db";

    private static AppDB sAppDB;
    public abstract NoteDAO noteDAO();  // Khai báo Class DAO

    public static AppDB getInstance(Context context) {
        if (sAppDB == null) {
            // Tránh xung đột Thread, Thread này xử lý xong, Thread khác mới xử lý
            synchronized (AppDB.class) {
                if (sAppDB == null) {
                    sAppDB = Room.databaseBuilder(context, AppDB.class, DATABASE_NAME)
                            .fallbackToDestructiveMigration()   // rebuild instead of Migrating
                            .allowMainThreadQueries()   // Cho phép chạy trên Thread UI, just for testing
                            //.addCallback(sRoomDatabaseCallback) // Gen Fake Data
                            .build();
                }
            }
        }
        return sAppDB;
    }
}
```

### 3. Khởi tạo Table Note

```java
@Entity(tableName = "note_table")
//@Entity(primaryKeys = ["id"]) // Multiple Id
public class Note {

    @PrimaryKey(autoGenerate = true)
    private int id; // Auto Generate Id

    //@PrimaryKey
    @NonNull
    @ColumnInfo(name = "text")
    private String text;

    public Note(@NonNull String text) {
        this.text = text;
    }
    // ... Setter, Getter
}
```

### 4. Tạo Note DAO (Data Access Object)

```java
@Dao
public interface NoteDAO {
    @Insert
    List<Long> insert(Note... notes);

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    long insertOrUpdate(Note note);

    @Delete
    void delete(Note note);

    @Query("DELETE FROM note_table")
    void deleteAll();

    @Update
    int update(Note... notes);

    @Query("SELECT * from note_table ORDER BY text ASC")
    /*List<Note> getAll();*/
    LiveData<List<Note>> getAll();

    @Query("SELECT * FROM `note_table` WHERE id=:id")
    fun getById(id: String): List<Note>
}
```

### 5. Sử dụng Hàm Trên UI

#### Chạy thông qua Task

```java
private static class insertAsyncTask extends AsyncTask<Note, Void, List<Long>> {

    private NoteDAO mNoteDAO;

    insertAsyncTask(NoteDAO dao) {
        mNoteDAO = dao;
    }

    @Override
    protected List<Long> doInBackground(final Note... params) {
        return mNoteDAO.insert(params[0]);
    }

    @Override
    protected void onPostExecute(List<Long> longs) {
        super.onPostExecute(longs);
        // ... Return
    }
}

//===== Run: new insertAsyncTask(mNoteDAO).execute(note); ==========
```

#### Test Trên UI

```java
AppDatabase appDB = AppDatabase.getInstance(application);
NoteDAO noteDAO = appDB.noteDAO();

noteDAO.insert(new Note(...));
```



---
**Reference:**
- https://codelabs.developers.google.com/codelabs/android-room-with-a-view/index.html?index=..%2F..%2Findex#0
- https://google-developer-training.gitbooks.io/android-developer-advanced-course-practicals/unit-6-working-with-architecture-components/lesson-14-room,-livedata,-viewmodel/14-1-a-room-livedata-viewmodel/14-1-a-room-livedata-viewmodel.html
- https://developer.android.com/training/data-storage/room/defining-data