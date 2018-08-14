# Permission: Xin Sự cho phép

<!-- TOC -->

- [Permission: Xin Sự cho phép](#permission-xin-sự-cho-phép)
    - [Kiểm tra Permission, lấy chưa cho phép](#kiểm-tra-permission-lấy-chưa-cho-phép)
    - [Yêu cầu cho phép Permission](#yêu-cầu-cho-phép-permission)
        - [Sử dụng](#sử-dụng)
        - [Truy cập Setting của ứng dụng](#truy-cập-setting-của-ứng-dụng)
    - [Nhận Cho phép từ User](#nhận-cho-phép-từ-user)
    - [Thư viện: Dexter](#thư-viện-dexter)

<!-- /TOC -->

## Kiểm tra Permission, lấy chưa cho phép

```java
public static String[] getPermissionNotGranted(Context context, String[] permissions ) {
    ArrayList<String> permissionsToRequest = new ArrayList<String>();
    for (int i=0; i<permissions.length; i++) {
        if (ContextCompat.checkSelfPermission(context, permissions[i]) != PackageManager.PERMISSION_GRANTED) {
            permissionsToRequest.add(permissions[i]);
        }
    }
    String[] permissionsArr = permissionsToRequest.toArray(new String[permissionsToRequest.size()]);
    return permissionsArr;
}
```

## Yêu cầu cho phép Permission

```java
public static String[] requestAllPermission(Activity activity, String[] permissions, int requestCode){
    // --- Request Permission ---
    String[] permissionsToRequest = getPermissionNotGranted(activity, permissions);
    if (permissionsToRequest.length > 0) {
        ActivityCompat.requestPermissions(activity, permissionsToRequest, requestCode);
    }

    return permissionsToRequest;
}
```

### Sử dụng

```java
// Khai báo toàn cục
public static int REQUEST_ALL_PERMISSION = 1;
static String[] permissions = new String[]{
        Manifest.permission.CAMERA,
        Manifest.permission.READ_EXTERNAL_STORAGE,
        Manifest.permission.WRITE_EXTERNAL_STORAGE
};
    
// Gọi trong Hàm
var remainingPermission = PermissionUtil.requestAllPermission(this, permissions, REQUEST_ALL_PERMISSION)
if (remainingPermission.isEmpty()) {
    goMainActivity();
}
```

### Truy cập Setting của ứng dụng

```java
public static Intent createOpenSettingIntent(Context context){
    final Intent i = new Intent();
    i.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
    i.addCategory(Intent.CATEGORY_DEFAULT);
    i.setData(Uri.parse("package:" + context.getPackageName()));
    i.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    i.addFlags(Intent.FLAG_ACTIVITY_NO_HISTORY);
    i.addFlags(Intent.FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS);
    return i;
}

// Sử dụng
startActivity(createOpenSettingIntent(this))
```

## Nhận Cho phép từ User

```java
@Override
public void onRequestPermissionsResult(int requestCode,
        String permissions[], int[] grantResults) {
    switch (requestCode) {
        case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
            // If request is cancelled, the result arrays are empty.
            if (grantResults.length > 0
                && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                // permission was granted, yay! Do the
                // contacts-related task you need to do.
            } else {
                // permission denied, boo! Disable the
                // functionality that depends on this permission.
            }
            return;
        }

        // other 'case' lines to check for other
        // permissions this app might request.
    }
}
```

## Thư viện: Dexter
https://github.com/Karumi/Dexter

```java
final String[] APP_PERMISSIONS = new String[]{
        Manifest.permission.READ_EXTERNAL_STORAGE,
        Manifest.permission.WRITE_EXTERNAL_STORAGE
};

Dexter.withActivity(this)
    .withPermissions(APP_PERMISSIONS)
    .withListener(new MultiplePermissionsListener() {
        @Override
        public void onPermissionsChecked(MultiplePermissionsReport report) {
            // Nếu đã grant vẫn vào 
        }

        @Override
        public void onPermissionRationaleShouldBeShown(List<PermissionRequest> permissions, PermissionToken token) {

        }
})
.check();
```