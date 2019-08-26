# RxJava - Xử lý Thread

https://github.com/ReactiveX/RxJava

## Convert Completable to Observable

```java
Completable.fromAction(() ->
    db.pictureDAO().insert(arrPictures)
).andThen(Observable.just("OK"));   
``` 

## Thread from Action, Void, Function

```java
Completable.fromAction(() ->
    // Void
)
```