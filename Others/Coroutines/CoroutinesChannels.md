# Channel

- Lưu danh sách Couroutines (kiểu FI-FO)
- **Hot Streams**: send item vào: suspend item. receive item: resume item

## Ví dụ

```java
// Hiện mỗi giá trị, sau mỗi giây
fun main() = runBlocking {
    val channel = Channel<Int>()
    val job = launch {
        for (x in 1..5) {
            channel.send(x * x)
        }
    }
    // print 5 giá trị, trước khi nhận cho delay 1s
    delay(1000) // delay 1s
    println(channel.receive()) // nhận giá trị thứ 1
    delay(1000) // delay 1s
    println(channel.receive()) // nhận giá trị thứ 2
    delay(1000) // delay 1s
    println(channel.receive()) // nhận giá trị thứ 3
    delay(1000) // delay 1s
    println(channel.receive()) // nhận giá trị thứ 4
    delay(1000) // delay 1s
    println(channel.receive()) // nhận giá trị thứ 5
    println("Done! Channel is empty?: ${channel.isEmpty} / Coroutine is completed?: ${job.isCompleted} / Coroutine is active?: ${job.isActive}")
}
```

## Hàm điều khiển

- Tạo channel: Channel<Int>()
- Thêm giá trị: .send(value)
- Xuất giá trị (remove luôn): .receive()
- Đóng, ko cho send, receive: .close()
- Kiểm tra Channel còn item: .isEmpty


- Hoàn thành Coroutines: .isCompleted
- Coroutines còn sống: .isActive

