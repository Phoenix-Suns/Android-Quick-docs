# Các ghi chú trong quá trình làm việc

## Action bar trong Fragment

```java
onCreate(...) {
    ...
    setHasOptionsMenu(true);
}

onCreateView(...) {
    ...
    View rootView = inflater.inflate(R.layout.fragment_layout, container, false);

    mToolbar = rootView.findViewById(R.id.toolbar_Key);
    mToolbar.setTitle(R.string.title_key);
    ((AppCompatActivity)getActivity()).setSupportActionBar(mToolbar);
}

```

## Thanh bar



## Chỉnh lại kích thước ImageView

```<ImageView android:adjustViewBounds="true"/>```


## Lỗi khi đổi language trong file file OBB

https://stackoverflow.com/questions/52731670/android-app-bundle-with-in-app-locale-change/52733674