# QR Code Handle

## ZXing

``` json
implementation 'me.dm7.barcodescanner:zxing:1.9.13'
```

### Read from file

```java
@OnClick(R.id.btPickFromGallery)
void onPickFromGalleryClick(View view) {
    Intent pickIntent = new Intent(Intent.ACTION_PICK);
    pickIntent.setDataAndType(android.provider.MediaStore.Images.Media.EXTERNAL_CONTENT_URI, "image/*");
    startActivityForResult(pickIntent, REQUEST_PICK_IMAGE);
}

@Override
public void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == REQUEST_PICK_IMAGE) {
        if(data == null || data.getData()==null) {
            Log.e("TAG", "The uri is null, probably the user cancelled the image selection process using the back button.");
            return;
        }
        Uri uri = data.getData();
        try {
            InputStream inputStream = getContext().getContentResolver().openInputStream(uri);
            Bitmap bitmap = BitmapFactory.decodeStream(inputStream);

            // crop image
            if (bitmap.getWidth() > MAX_RESOLUTION_IMAGE) {
                float ratio = (float) bitmap.getHeight() / bitmap.getWidth();
                bitmap = Bitmap.createScaledBitmap(bitmap, MAX_RESOLUTION_IMAGE, (int) (MAX_RESOLUTION_IMAGE * ratio), false);
            }
            ivReview.setImageBitmap(bitmap);

            int[] intArray = new int[bitmap.getWidth()*bitmap.getHeight()];
            //copy pixel data from the Bitmap into the 'intArray' array
            bitmap.getPixels(intArray, 0, bitmap.getWidth(), 0, 0, bitmap.getWidth(), bitmap.getHeight());
            LuminanceSource source = new RGBLuminanceSource(bitmap.getWidth(), bitmap.getHeight(), intArray);
            BinaryBitmap binaryBitmap = new BinaryBitmap(new HybridBinarizer(source));

            try {
                Reader reader = new MultiFormatReader();

                // Try decode harder
                Map<DecodeHintType, Object> tmpHintsMap = new EnumMap<>(DecodeHintType.class);
                tmpHintsMap.put(DecodeHintType.TRY_HARDER, Boolean.TRUE);
                tmpHintsMap.put(DecodeHintType.POSSIBLE_FORMATS, EnumSet.allOf(BarcodeFormat.class));
                tmpHintsMap.put(DecodeHintType.PURE_BARCODE, Boolean.FALSE);
                Result result = reader.decode(binaryBitmap, tmpHintsMap);

                handleResult(result);
            } catch (Exception ex) {
                Log.e(TAG, "" + ex.getMessage());
                AppDialog.showScanQRFailedMessage(this, null);
            }
        }
        catch (FileNotFoundException e) {
            Log.e(TAG, "can not open file" + uri.toString(), e);
            AppDialog.showScanQRFailedMessage(this, null);
        }
    }
}
```

## Google Vision

```json
implementation "com.google.android.gms:play-services-vision:20.1.3"
```

### Read from file

```java
Frame frame = new Frame.Builder().setBitmap(bitmap).build();
BarcodeDetector barcodeDetector = new BarcodeDetector.Builder(requireContext()).build();
SparseArray<Barcode> sparseArray = barcodeDetector.detect(frame);
if(sparseArray.size() > 0){
    for (int i = 0; i < sparseArray.size(); i++){
        Log.e(TAG, "Value: " + sparseArray.valueAt(i).rawValue + "----" + sparseArray.valueAt(i).displayValue);
        Toast.makeText(requireContext(), sparseArray.valueAt(i).rawValue, Toast.LENGTH_SHORT).show();
    }
}else {
    Log.e(TAG,"SparseArray null or empty");
}
```
