# Retrofit 2: Kết nối WebService, Rest API

http://square.github.io/retrofit/

<!-- TOC -->

- [Retrofit 2: Kết nối WebService, Rest API](#retrofit-2-kết-nối-webservice-rest-api)
    - [Cài đặt](#cài-đặt)
    - [Simple Example - Ví dụ cơ bản](#simple-example---ví-dụ-cơ-bản)
        - [Step 1: Tạo Object dựa vào Json trả về. Truy cập: http://www.jsonschema2pojo.org/](#step-1-tạo-object-dựa-vào-json-trả-về-truy-cập-httpwwwjsonschema2pojoorg)
        - [Step 2: Tạo Interface lấy data](#step-2-tạo-interface-lấy-data)
        - [Step 3: Thực thi](#step-3-thực-thi)
    - [1 Số phương Thức thông dụng](#1-số-phương-thức-thông-dụng)
        - [GET](#get)
        - [POST JSON BODY](#post-json-body)
        - [POST FORM DATA](#post-form-data)
        - [POST FORM IMAGE](#post-form-image)
        - [Download Image](#download-image)
    - [Gợi ý Sắp Xếp File](#gợi-ý-sắp-xếp-file)

<!-- /TOC -->


## Cài đặt

thêm dependencies vào project App: **app/build.gradle**

```json
dependencies {
    ...
    implementation 'com.google.code.gson:gson:2.8.2'

    implementation 'io.reactivex.rxjava2:rxandroid:2.0.2'
    implementation 'io.reactivex.rxjava2:rxjava:2.1.13'

    api 'com.squareup.retrofit2:retrofit:2.4.0'
    api 'com.squareup.retrofit2:converter-gson:2.4.0'
    api 'com.squareup.retrofit2:adapter-rxjava2:2.4.0'
}
```

## Simple Example - Ví dụ cơ bản

### Step 1: Tạo Object dựa vào Json trả về. Truy cập: http://www.jsonschema2pojo.org/

1. Source type: Json
2. Annotation style: Gson
3. Click Preview
4. Tạo Class: **UserDetailModel.java**

![Json to Java](/Images/retrofit_2_json.png)

### Step 2: Tạo Interface lấy data

```java
public interface GithubWS {
    @GET("users/{username}")
    Call<UserDetailModel> getGitHubUserDetail1(@Path("username") String userName);
}
```

### Step 3: Thực thi

```java
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("https://api.github.com/")
        .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
        .addConverterFactory(GsonConverterFactory.create())
        .build();
GithubWS githubService = retrofit.create(GithubWS.class);

Call<UserDetailModel> call = githubService.getGitHubUserDetail1("luunghiatran");
call.enqueue(new Callback<UserDetailModel>() {
    @Override
    public void onResponse(Call<UserDetailModel> call, Response<UserDetailModel> response) {
        if (response.body() != null) {
            // Success
            UserDetailModel value = response.body();
        }else {
            // Error
            String error = response.raw().message();
        }
    }

    @Override
    public void onFailure(Call<UserDetailModel> call, Throwable t) {
        // Error
        String error = t.getMessage();
    }
});
```

## 1 Số phương Thức thông dụng

### GET

```java
public interface GithubWS {
    @GET("users/{username}")
    Call<UserDetailModel> getGitHubUserDetail1(@Path("username") String userName);
}

// Using
githubService.getGitHubUserDetail1("luunghiatran");
```

### POST JSON BODY

```java
@POST(WSConfig.ENDPOINT_SEND_USER_INFO)
Call<WSResultBase> sendUserInfo(@Body HashMap<String, Object> body);

// === Using ===
/*post value: {
    "name": "The Jester",
        "phone": "0123456789",
        "email": "logan@adquestasia.com"
}*/

HashMap<String, Object> body = new HashMap<>();
body.put("name", name);
body.put("phone", phone);
body.put("email", email);

return service.sendUserInfo(body);
```

### POST FORM DATA

```java
@FormUrlEncoded
@POST(WSConfig.ENDPOINT_CHANGE_PASSWORD)
Call<SimpleWSResult> ChangePassword(
        @Field("user_id") RequestBody userId,
        @Field("new_pass") RequestBody oldPass,
        @Field("old_pass") RequestBody newPass);

// === Using === 
RequestBody rbUserId = RequestBody.create(MediaType.parse("text/plain"), userId+"");
RequestBody rbOldPass = RequestBody.create(MediaType.parse("text/plain"), oldPass);
RequestBody rbNewPass = RequestBody.create(MediaType.parse("text/plain"), newPass);

return service.ChangePassword(rbUserId, rbOldPass, rbNewPass);
```

### POST FORM IMAGE

```java
@Multipart
@POST(WSConfig.ENDPOINT_CHANGE_AVATAR)
Call<SimpleWSResult> ChangeAvatar(
        @Part("user_id") RequestBody userId,
        @Part MultipartBody.Part file);

// === Using ===
RequestBody rbUserId = RequestBody.create(MediaType.parse("text/plain"), userId+"");

// Kiến tạo file
File fAvatar = new File(mUriImage.getPath());
RequestBody rbAvatar = RequestBody.create(MediaType.parse("image/*"), fAvatar);
MultipartBody.Part mpAvatar = MultipartBody.Part.createFormData("image", fAvatar.getName(), rbAvatar );

return service.ChangeAvatar(rbUserId, mpAvatar);
```

### Download Image

```java
private void downloadImage() {
    OkHttpClient client = new OkHttpClient();

    Request request = new Request.Builder()
            .url("http://wwwns.akamai.com/media_resources/globe_emea.png")
            .build();

    client.newCall(request).enqueue(new okhttp3.Callback() {
        @Override
        public void onFailure(okhttp3.Call call, IOException e) {
            String error = "Error " + e.getMessage();
            Toast.makeText(MainActivity.this, error, Toast.LENGTH_LONG).show();
        }

        @Override
        public void onResponse(okhttp3.Call call, okhttp3.Response response) throws IOException {
            String filePath = Environment.getExternalStorageDirectory().getPath() + "/file_name.png";

            InputStream inputStream = response.body().byteStream(); // Read the data from the stream
            OutputStream outputStream = new FileOutputStream(filePath);
            writeStream(inputStream, outputStream); //FileUtil

            MainActivity.this.runOnUiThread(() -> Toast.makeText(MainActivity.this, "Saved", Toast.LENGTH_LONG).show());
        }
    });
}

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


## Gợi ý Sắp Xếp File

- network
- - facebook
- - googlemap
- - webservice
- - - OutletWS
- - - WSConfig   // Lưu giữ cài đặt, Static Field: WS_DOMAIN, ENDPOINT_GET_OUTLET
- - - WSBase     // Tạo mẫu Retrofit
- - wsresponse
- - - WSResult   // Các Model tạo tự động

**OutletWS**

```java
public class OutletWS extends WSBase {
    public interface IService {
        @GET(WSConfig.ENDPOINT_ALL_OUTLET)
        Call<OutletListWSResult> getOutletList();
    }

    public Call<OutletListWSResult> getOutletList() {
        IService service = createService(IService.class);

        return service.getOutletList();
    }
}
```

**WSConfig**

```java
public class WSConfig {
    public static int STATUS_SUCCESS = 1;
    public static int STATUS_ERROR = 0;

    public static final String WS_DOMAIN = "http://domain.com/api/";
    public static final String WS_DOMAIN_MEDIA = "http://domain.com/api/image/";

    // End of URL Path: ws_domain + endpoint
    public static final String ENDPOINT_ALL_OUTLET = "location/location_data";
}
```

**WSBase**

```java
public class BaseWS {
    private static Retrofit retrofit = null;

    public static Retrofit buildRetrofit() {
        if (retrofit==null) {
            retrofit = new Retrofit.Builder()
                    .baseUrl(WSConfig.WS_DOMAIN)
                    .addConverterFactory(GsonConverterFactory.create())
                    .build();
        }
        return retrofit;
    }
}
```