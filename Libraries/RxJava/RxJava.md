# RxJava Trick

## Basic

### Hello word

```java
Flowable.just("Hello world").subscribe(System.out::println);
```

### Subscription time (Show data)

```java
// Subscribe
Disposable subscribe = Observable.just(1,2,3).subscribe(new Consumer<Integer>() {
    @Override
    public void accept(Integer integer) throws Throwable {
        Log.e("Test", "" + integer);
    }
});
```

### Assembly time (doing data)

```java
// Flowable
Flowable<Integer> flow = Flowable.range(1, 5)   // 1, 2, 3, 4, 5
        .map(v -> v * v)   // 1, 4, 9, 16, 25
        .filter(v -> v % 3 == 0); // 9
flow.subscribe(value -> {
    Log.e("Test", "" + value);
});
```

### Runtime (Doing + Show data)

```java
Observable.create(emitter -> {
    // Send Error
    emitter.onError(new IllegalStateException("This's a ERROR!"));
}).subscribe(
        x -> {
            // Export value
            System.out.println(x);
        },
        throwable -> {
            // Export error
            Log.e("Test Error", throwable.getMessage()); // Logcat: E/Test Error: This's a ERROR!
            throwable.printStackTrace();
        }
);
```

## Create Subscriber

```java
private Observable<List<ScheduleSession>> getLocalMonthSessions() {
    return Observable.create(subscriber -> {
        List<ScheduleSession> localSessionList = new ArrayList<>();
        //...
        subscriber.onNext(localSessionList);
        subscriber.onCompleted();
    });
}
```

```java
PublishSubject<String> subject = PublishSubject.create();
subject.hasObservers();     // Receive
subject.onNext("Test");    // Send
```

```java
// Send empty
Observable.empty();
```

## Send to Error

```java
Observable.create(emitter -> {
    // Send Error
    emitter.onError(new IllegalStateException("This's a ERROR!"));
    // Or
    return Observable.error(new Throwable("This's a ERROR!"));
});
```

## Rx Type

- Flowable
- Observable
- Single
- Maybe
- Completable

---

## Reference

- <https://github.com/ReactiveX/RxJava>
- <http://reactivex.io/documentation/observable.html>
- <https://viblo.asia/p/cung-tim-hieu-ve-rxjava-phan-1-QpmleBmk5rd>
