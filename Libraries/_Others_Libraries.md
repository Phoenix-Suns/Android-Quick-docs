# Others Android Libraries

<!-- TOC -->

- [Others Android Libraries](#others-android-libraries)
  - [Butter Knife - Binding View Library](#butter-knife---binding-view-library)
    - [Example](#example)
  - [Dexter - Ask Permission Library](#dexter---ask-permission-library)
    - [Example](#example-1)
  - [Glide - Thư viện load ảnh Online](#glide---th%c6%b0-vi%e1%bb%87n-load-%e1%ba%a3nh-online)
    - [Download Image](#download-image)
  - [EventBus - Chuyển sự kiện qua lại giữa các Android Component](#eventbus---chuy%e1%bb%83n-s%e1%bb%b1-ki%e1%bb%87n-qua-l%e1%ba%a1i-gi%e1%bb%afa-c%c3%a1c-android-component)

<!-- /TOC -->

## Butter Knife - Binding View Library

Dùng để bind view, thay cho findViewById

http://jakewharton.github.io/butterknife/

### Example

```java
//----- For Activity ----
class ExampleActivity extends Activity {
  @BindView(R.id.title) TextView title;
  @BindView(R.id.subtitle) TextView subtitle;
  @BindView(R.id.footer) TextView footer;

  @Override public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.simple_activity);
    ButterKnife.bind(this);
    // TODO Use fields...
  }
}

// ----- For Fragment -----
public class OutletListFragment extends Fragment {
    @BindView(R.id.recyclerview) RecyclerView mRecyclerView_Outlet;
    @BindView(R.id.swipeRefreshLayout) SwipeRefreshLayout mSwipeRefreshLayout;
    
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        mRootView = inflater.inflate(R.layout.layout_fragment, container, false);
        ButterKnife.bind(this, mRootView);
        // TODO Use fields...
        return mRootView;
    }

// ------ for Recycler View Holder ------
public class ViewHolder extends RecyclerView.ViewHolder {
    public final View view;
    @BindView(R.id.textView_Serial) TextView textView_Serial;
    @BindView(R.id.textView_Name) TextView textView_Name;
    @BindView(R.id.textView_Address) TextView textView_Address;
    public OutletModel item;

    public ViewHolder(View view) {
        super(view);
        this.view = view;
        ButterKnife.bind(this, view);
    }
}
```

## Dexter - Ask Permission Library

https://github.com/Karumi/Dexter

### Example

```java
Dexter.withActivity(this)
.withPermissions(AppConfig.APP_PERMISSIONS)
.withListener(new MultiplePermissionsListener() {
    @Override
    public void onPermissionsChecked(MultiplePermissionsReport report) {
        //TODO: do after request, asked still go here
    }

    @Override
    public void onPermissionRationaleShouldBeShown(List<PermissionRequest> permissions, PermissionToken token) {

    }
})
.check();
```

## Glide - Thư viện load ảnh Online

https://github.com/bumptech/glide
https://github.com/codepath/android_guides/wiki/Displaying-Images-with-the-Glide-Library

### Download Image

```java
Glide.with(getApplicationContext())
    .asBitmap()
    .load(WSConfig.WS_MEDIA_DOMAIN + newPic.getImageUrl())
    .into(new SimpleTarget<Bitmap>() {
        @Override
        public void onResourceReady(@NonNull Bitmap resource, @Nullable Transition<? super Bitmap> transition) {
            // save resource Image
        }
    });
```

## EventBus - Chuyển sự kiện qua lại giữa các Android Component

https://github.com/greenrobot/EventBus

- Step 1: Define events:

```java
public static class MessageEvent { /* Additional fields if needed */ }
```

- Step 2: Prepare subscribers: Declare and annotate your subscribing method, optionally specify a thread mode:

```java
@Subscribe(threadMode = ThreadMode.MAIN)  
public void onMessageEvent(MessageEvent event) {/* Do something */};
```

- Step 3: Register and unregister your subscriber. For example on Android, activities and fragments should usually register according to their life cycle:

```java
@Override
public void onStart() {
    super.onStart();
    EventBus.getDefault().register(this);
}

@Override
public void onStop() {
    super.onStop();
    EventBus.getDefault().unregister(this);
}
```

- Step 4: Post events:

```java
EventBus.getDefault().post(new MessageEvent());
```

---
