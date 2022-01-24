# Lỗi phổ biến

## Fragment biến mất sau khi chuyển tab

đổi "fragmentManager" thành "childFragmentManager"

## 

java.io.IOException: canceled due to java.lang.OutOfMemoryError: Failed to allocate a 268086080 byte allocation with 50331648 free bytes and 245MB until OOM, target footprint 329653704, growth limit 536870912

- Giải thích
By calling .setLogLevel(RestAdapter.LogLevel.FULL) you are forcing Retrofit to buffer the entire request body into memory so that it can log. That's what the call to readBodyToBytesIfNecessary in the stack trace is doing.

Enabling logging like this should only be done when debugging.

 - Fix: HttpLoggingInterceptor.Level.BODY -> BASIC