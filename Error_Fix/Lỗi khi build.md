## Error Text transformDexArchiveWithExternalLibsDexMergerForDebug

Error:Execution failed for task ':app:transformDexArchiveWithExternalLibsDexMergerForDebug'.
> java.lang.RuntimeException: java.lang.RuntimeException: com.android.builder.dexing.DexArchiveMergerException: Unable to merge dex

## Lý do lỗi

Nhiều project import cùng thư viện

## How to fix

```gradle
android {
    defaultConfig {
            multiDexEnabled true
        }
        /*Gộp nhiều 1 thư viện trong nhiều project*/
        configurations {
            all*.exclude group: 'com.android.support', module: 'support-v4'
            all*.exclude group: 'com.android.support', module: 'support-annotations'
        }
}
```