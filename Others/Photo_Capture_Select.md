# Chụp Ảnh, Chọn Ảnh

## Chụp Ảnh

- Bước 1: Khai báo trong Mainifests

```xml
<application ...>
    <provider
        android:name="android.support.v4.content.FileProvider"
        android:authorities="${applicationId}.fileprovider"
        android:exported="false"
        android:grantUriPermissions="true">
        <meta-data
            android:name="android.support.FILE_PROVIDER_PATHS"
            android:resource="@xml/file_paths" />
    </provider>
```

- 2: Tạo 1 file lưu thông tin save file: **res/xml/file_paths.xml**. Nội dung sau:

```xml
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <external-path
        name="external_files"
        path="." />
</paths>
```

- 3: Code chung:

```java
private Uri mUriImage;  //Lưu thông tin Ảnh
// =============== Có thể tạo trong File Helper ==========
private boolean saveImage() {
    boolean result;
    String imagePath = genImagePath(this);  //AppConfig

    try {
        // Save file
        // Lấy ảnh resize + save

        result = makeFileDirectory(imagePath);  //FileUtil.
        if (result) {

            InputStream input = getContentResolver().openInputStream(mUriImage);
            FileOutputStream output = new FileOutputStream(imagePath);

            result = writeStream(input, output);    //FileUtil
        }
    } catch (Exception ex){
        Log.e(AppConfig.APP_LOG_TAG, ex.getMessage());
        result = false;
    }

    mUriImage = Uri.fromFile(new File(imagePath));
    return result;
}

// Tạo file, với thư mục là tên ứng dụng
public static String genImagePath(Context context) {
    String dirPath = Environment.getExternalStorageDirectory() + "/"
            + context.getApplicationInfo().loadLabel(context.getPackageManager());
    return FileUtil.makeFilePathByTime(dirPath, "avatar", "jpg");
}

// Tạo Thư mục
public static boolean makeFileDirectory(String filePath){
    String folderTargetPath = filePath.substring(0, filePath.lastIndexOf("/")); // getFileDirectory(filePath);
    File folder = new File(folderTargetPath);

    if (!folder.exists() || !folder.isDirectory())
        return folder.mkdirs();

    return true;
}

// Copy file
public static boolean writeStream(InputStream input, OutputStream output){
    try {
        // Chuyển byte từ Input => Output
        byte[] buffer = new byte[1024];
        int length;
        while ((length = input.read(buffer)) > 0) {
            output.write(buffer, 0, length);
        }

        // Close the streams
        output.flush();
        output.close();
        input.close();
        return true;

    } catch (IOException e) {
        e.printStackTrace();
    }
    return false;
}
```

- 4: 1 đống code, gọi hàm takePhoto để chụp ảnh

```java
private static final int REQUEST_TAKE_PHOTO = 101;

// Gọi Hàm này để sử dụng
private void takePhoto() {
    Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
    if (takePictureIntent.resolveActivity(getActivity().getPackageManager()) != null) {
        takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, setImageUri()); // put uri file khi mà mình muốn lưu ảnh sau khi chụp như thế nào  ?

        // Đặt Permission
        takePictureIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION | Intent.FLAG_GRANT_WRITE_URI_PERMISSION);

        startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
    }
}

public Uri setImageUri() {
    File photoFile = createFile();

    if (Build.VERSION.SDK_INT < 21) {
        // Lưu luôn
        photoFile = new File(Environment.getExternalStorageDirectory() + "/DCIM/", "File_Name"+new Date().getTime() + ".png");
        mUriImage = Uri.fromFile(photoFile);
    } else {
        // Lưu Tạm trong bộ nhớ
        Uri photoURI = FileProvider.getUriForFile(this, BuildConfig.APPLICATION_ID+".fileprovider", photoFile);
        mUriImage = photoURI;
    }
    return mUriImage;
}

private File createFile() {
    File filesDir = getExternalFilesDir(Environment.DIRECTORY_PICTURES);
    File file = null;
    try {
        file = File.createTempFile("File_Name"+new Date().getTime(), ".png", filesDir);
    } catch (IOException e) {
        e.printStackTrace();
    }
    return file;
}

@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    // Take photo
    if (requestCode == REQUEST_TAKE_PHOTO && resultCode == RESULT_OK) {
        if (saveImage()) {
            // Update UI Next
            mImageView_FontCard.setImageURI(mUriImage);
        }
    }
}
```

## Chọn Ảnh: Thực hiện bước 1, 2, 3 như trên

```java
private static final int REQUEST_GET_PHOTO = 100;

// Gọi hàm này để sử dụng
public void selectGallery(View view) {
    //Intent lấy ra gallery
    Intent galleryIntent = new Intent(Intent.ACTION_GET_CONTENT);
    galleryIntent.setType("image/*");
    startActivityForResult(galleryIntent, REQUEST_GET_PHOTO);
}

@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    // Get Photo
    if (requestCode == REQUEST_GET_PHOTO && resultCode == RESULT_OK){
        mUriImage = data.getData(); //Lấy ra uri của image
        if (saveImage()) {
            // Update UI Next
            mImageView_FontCard.setImageURI(mUriImage);
        }
    }
}
```