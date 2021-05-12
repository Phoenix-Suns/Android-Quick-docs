# RxJava RxAndroid - Kiểm Soát Thread, đa luồng nhanh gọn

- [RxJava RxAndroid - Kiểm Soát Thread, đa luồng nhanh gọn](#rxjava-rxandroid---ki%e1%bb%83m-so%c3%a1t-thread-%c4%91a-lu%e1%bb%93ng-nhanh-g%e1%bb%8dn)
  - [Tạo Lớp Quản lý Description của Rx](#t%e1%ba%a1o-l%e1%bb%9bp-qu%e1%ba%a3n-l%c3%bd-description-c%e1%bb%a7a-rx)
  - [Simple RxJava & Retrofit](#simple-rxjava--retrofit)
  - [2 Request đồng thời](#2-request-%c4%91%e1%bb%93ng-th%e1%bb%9di)
    - [Zip](#zip)
    - [Merge](#merge)
  - [2 Request Tuần tự, xong rồi tiếp](#2-request-tu%e1%ba%a7n-t%e1%bb%b1-xong-r%e1%bb%93i-ti%e1%ba%bfp)
    - [flatMap](#flatmap)
  - [Tham Khảo](#tham-kh%e1%ba%a3o)

## Tạo Lớp Quản lý Description của Rx

```java
CompositeDisposable mCompositeDisposable = new CompositeDisposable();   //Quản lý Subscription

@Override
    protected void onDestroy() {
        mCompositeDisposable.clear();    // Using clear will clear all, but can accept new disposable
        //mCompositeDisposable.dispose(); // Using dispose will clear all and set isDisposed = true, so it will not accept any new disposable

        super.onDestroy();
    }
```

## Simple RxJava & Retrofit

Cài đặt retrofit

```java
// Tạo Interface
@GET("users/{username}")
Observable<UserDetailModel> getGitHubUserDetail(@Path("username") String userName);

// === Using ====
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("https://api.github.com/")
        .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
        .addConverterFactory(GsonConverterFactory.create())
        .build();
GithubWS githubService = retrofit.create(GithubWS.class);

//===========One retrofit================
Disposable dis = githubService.getGitHubUserDetail("ahmedrizwan")
        .subscribeOn(Schedulers.newThread())        // “work” on io thread
        .observeOn(AndroidSchedulers.mainThread())  // “listen” on UIThread
        .map(response -> "nghia:" + response.getName() + " \nurl:" + response.getUrl()) //get data return, convert it to String
        .subscribe(finalResponse -> Log.e("nghia: ", finalResponse));

mCompositeDisposable.add(dis);
```

## 2 Request đồng thời

### Zip

```java
private void retrofit_2API_RunSameTime(GithubWS githubService) {
    Observable<UserDetailModel> api_1 = githubService.getGitHubUserDetail("ahmedrizwan").subscribeOn(Schedulers.newThread())            .observeOn(AndroidSchedulers.mainThread());
    Observable<UserDetailModel> api_2 = githubService.getGitHubUserDetail("luunghiatran").subscribeOn(Schedulers.newThread()).observeOn(AndroidSchedulers.mainThread());

    //==========2 Retrofit: Xử lý đồng thời ===============
    //https://gist.github.com/Macrow/99e2be7208dd42d76c0be8556dc785b0
    Observable.zip( api_1, api_2,
            // Tạo Object mới
            (userDetail1, userDetail2) -> userDetail1.getAvatarUrl() + userDetail2.getAvatarUrl()   //Can make an object here
    ).subscribe(new Observer<String>() {
        @Override
        public void onSubscribe(Disposable d) {
            Log.e(TAG_LOG, d.toString());
        }

        @Override
        public void onNext(String s) {
            Log.e(TAG_LOG, s);      // Return Value
        }

        @Override
        public void onError(Throwable e) {
            Log.e(TAG_LOG, e.getMessage());
        }

        @Override
        public void onComplete() {
            Log.e(TAG_LOG, "complete");
        }
    });
}
```

### Merge

```java
//https://viblo.asia/p/zip-va-merge-trong-rxjava-924lJM80ZPM
// ở đây trong hàm onNext() chúng ta thấy RxJava trả về "từng phần tử"
private void mergeList() {
    Observable.merge(createListObservable()).subscribe(new Subscriber<Object>() {
        @Override
        public void onCompleted() {

        }

        @Override
        public void onError(Throwable e) {

        }

        @Override
        public void onNext(Object o) {
            txtMergeList.setText(txtMergeList.getText() + " " +  o);
        }
    });
}

// kotlin
// Make List Disposable
val listDisposable = ArrayList<Observable<BaseRespond<EmptyResponse>>>()
for (i in 0 until listNotification.size) {
    val disposable = RetrofitManager.service.deleteNotification(listNotification[i].id)
    listDisposable.add(disposable)
}

val merge = Observable.merge(listDisposable)
        .subscribeOn(Schedulers.newThread()).observeOn(AndroidSchedulers.mainThread())
        .subscribe({ result ->
            // onNext
            if(result.code == UrlHelper.CODE_SUCCESS) {
                // add success
            } else {
                // add error
            }

        }, { error ->

        }, {
            // Complete
        })

addSubscription(merge)
```

## 2 Request Tuần tự, xong rồi tiếp

### flatMap

```java
private void retrofit_2API_Sequentially(GithubWS githubService) {
    //================== 2 Retrofit: Tuần tự ====================
    Disposable dis = githubService.getAllUser().subscribeOn(Schedulers.newThread()).observeOn(AndroidSchedulers.mainThread())
        .flatMap(response -> {  // Trả về ListUser

            // Call 1, DoSomething, Call 2
            Log.e(TAG_LOG, "Nhận 1: " + response.size());
            return githubService.getGitHubUserDetail("ahmedrizwan").subscribeOn(Schedulers.newThread()).observeOn(AndroidSchedulers.mainThread());
        }).flatMap(response2 -> {   // Trả về UserDetail của "ahmedrizwan"

            // Call 2, DoSomething, Call 3
            Log.e(TAG_LOG, "Nhận 2: " + response2.getAvatarUrl());
            return githubService.getGitHubUserDetail("luunghiatran").subscribeOn(Schedulers.newThread()).observeOn(AndroidSchedulers.mainThread());
        })
        .subscribe(response3 -> { // trả về UserDetail của "luunghiatran"
            // Call 3, Finish
            Log.e(TAG_LOG, "Nhận 3: " + response3.getAvatarUrl());
        },
        error -> {
            Log.e(TAG_LOG, "Cancel loading");
            Log.e(TAG_LOG, "Error: " + error.getMessage());
        });

    mCompositeDisposable.add(dis);
}
```

---

## Tham Khảo

- <http://reactivex.io/documentation/operators.html#creating>
