# Operators By Category (Toán tử)

## Fast setup

```java
BehaviorSubject<String> test1Subject = BehaviorSubject.create();
BehaviorSubject<String> test2Subject = BehaviorSubject.create();

// Check here

public void test2Click(View view) {     test2Subject.onNext("2222"); }
public void test1Click(View view) {     test1Subject.onNext("1111"); }
```

## Combining Observables

### CombineLastest: first: 1 and 2, next: 1 or 2

```java
/* 1 click + 2 click = print log
    after that: 1 click or 2 click = print log */
Observable.combineLatest(
    test1Subject, test2Subject,
    (item1, item2) -> {
        return item1 + "-" + item2; // observable result
    }).subscribe(result -> {
        Log.e("Nghia", result); // Log: Nghia: 1111-2222
    });
```

### 

---

## Reference

- <http://reactivex.io/documentation/operators.html#combining>
