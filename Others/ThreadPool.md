## ThreadPool (java)

Quản lý, điều tiết nhiều Thread

```java
// ===== Khởi Future, Lưu Thread =====
ExecutorService executorService = Executors.newFixedThreadPool(5);
List<Future> listFuture = new ArrayList<Future>(); 
        
// ===== Thêm Thread vào Future =====
for (int i = 1; i <= 10; i++) {
    MyRunnable myRunnable = new MyRunnable("Runnable " + i);
    // Bước này chúng ta dùng submit() thay vì execute()
    Future future = executorService.submit(myRunnable);
    listFuture.add(future); // Từng Future sẽ quản lý một Runnable
}
    
for (Future future : listFuture) {
    try {
        // Khi Thread nào kết thúc, get() của Future tương ứng sẽ trả về null
        System.out.println(future.get());
    } catch (ExecutionException | InterruptedException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
}
        
// ===== Huỷ tất cả Theard =====
executorService.shutdown();
```

---

## Reference

- https://www.baeldung.com/kotlin/create-thread-pool

