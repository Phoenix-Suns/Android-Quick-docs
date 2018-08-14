# Glide - Thư viện load ảnh Online

https://github.com/bumptech/glide

## Download Image

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