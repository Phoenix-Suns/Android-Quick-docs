# Flow: xuất ra list đồng thời (bất đồng bộ)

- [Flow: xuất ra list đồng thời (bất đồng bộ)](#flow-xuất-ra-list-đồng-thời-bất-đồng-bộ)
  - [Cú pháp](#cú-pháp)
  - [Sequence: xuất ra list tuần tự (đồng bộ)](#sequence-xuất-ra-list-tuần-tự-đồng-bộ)
  - [Hàm xử lý](#hàm-xử-lý)
    - [Lấy 2 phần tử: .take(2)](#lấy-2-phần-tử-take2)
    - [Biến đổi giá trị ra: .map](#biến-đổi-giá-trị-ra-map)
    - [Biến đổi giá trị ra: .fatMap](#biến-đổi-giá-trị-ra-fatmap)
      - [Biến đổi giá trị ra, chạy hết flow mỗi lần: .flatMapConcat](#biến-đổi-giá-trị-ra-chạy-hết-flow-mỗi-lần-flatmapconcat)
      - [Biến đổi giá trị ra: .flatMapMerge](#biến-đổi-giá-trị-ra-flatmapmerge)
      - [Biến đổi giá trị ra, lấy mới nhất: .flatMapLatest](#biến-đổi-giá-trị-ra-lấy-mới-nhất-flatmaplatest)
    - [Biến đổi giá trị ra, thêm bớt số lượng: .transform](#biến-đổi-giá-trị-ra-thêm-bớt-số-lượng-transform)
    - [Lọc lại giá trị ra: .filter](#lọc-lại-giá-trị-ra-filter)
    - [Thực hiện trước khi emit: .onEach](#thực-hiện-trước-khi-emit-oneach)
    - [Cộng dồn giá trị: .reduce](#cộng-dồn-giá-trị-reduce)
    - [Cộng dồn giá trị, thêm giá trị: .fold](#cộng-dồn-giá-trị-thêm-giá-trị-fold)
    - [Cộng 2 flow (đợi 2 giá trị cùng ra): zip](#cộng-2-flow-đợi-2-giá-trị-cùng-ra-zip)
    - [Cộng 2 flow: combine](#cộng-2-flow-combine)
  - [Reference](#reference)

## Cú pháp

```java
// Xuất ra số đồng thời
numberList().collect { println(it) }    // chỉ chạy khi gọi collect

// Tạo flow
fun numberList(): Flow<Int> = flow {
    for (i in 1..3) {
        delay(1000)
        // có thể dùng Suppend funtions trong flow
        emit(i) // Send value
    }
}

// OR
val data = flowOf(1,"abc", 3.4, "def")

// OR
listOf(1, "abc", 3.4, "def").asFlow()
```

## Sequence: xuất ra list tuần tự (đồng bộ)

```java
// Xuất ra số tuần tự (1s xuất 1, 1s sau xuất 2)
numberList().forEach { value -> println(value) }

fun numberList(): Sequence<Int> = sequence {
    for (i in 1..3) {
        Thread.sleep(1000)
        yield(i)    // Send Value
    }
}
```

## Hàm xử lý

### Lấy 2 phần tử: .take(2)

### Biến đổi giá trị ra: .map

```java
(1..3).asFlow().map { it * it }
```

### Biến đổi giá trị ra: .fatMap

#### Biến đổi giá trị ra, chạy hết flow mỗi lần: .flatMapConcat

```java
(1..3).asFlow()
    .flatMapConcat {
        // Đợi flow này xong, mới chạy phần tử tiếp theo
        flow {
            emit("first-$it")
            delay(500)
            emit("second-$it")
        }
    }
/* System.out: first-1 second-1 first-2 second-2 */
```

#### Biến đổi giá trị ra: .flatMapMerge

```java
(1..3).asFlow()
    .flatMapMerge {
        // collect nhận emit bất kì đến, không cần đợi flow này xong
        flow {
            emit("first-$it")
            delay(500)
            emit("second-$it")
        }
    }
/* System.out: first-1 first-2 first-3 second-1 second-2 sencond-3 */
```

#### Biến đổi giá trị ra, lấy mới nhất: .flatMapLatest

### Biến đổi giá trị ra, thêm bớt số lượng: .transform

```java
(1..9).asFlow() // a flow of requests
    .transform { value ->
        if (value % 2 == 0) {
            // Emit only even values, but twice
            emit(value * value)
            emit(value * value * value)
        } // Do nothing if odd
    }
    .collect { response -> println(response) }
// System.out: 4 8 16 64 36 216 64 512
```

### Lọc lại giá trị ra: .filter

```java
(1..5).asFlow().filter { it % 2 == 0 } // Lọc lại số chẵn
```

### Thực hiện trước khi emit: .onEach

```java
(1..3).asFlow()
    .onEach { 
        // Delay 3s trước khi xuất
        delay(3000) 
    }.collect { value ->
        println(value)
    }
```

### Cộng dồn giá trị: .reduce

```java
val sum = (1..3).asFlow()
    .reduce { a, b -> a + b } // sum them
println(sum)    // 1+2+3 = 6
```

### Cộng dồn giá trị, thêm giá trị: .fold

```java
val sum = (1..3).asFlow()
    .fold(initial = 10) { 
        a, b -> a + b 
    }
println(sum)    // 10 + 1 + 2 + 3 = 16  
```

### Cộng 2 flow (đợi 2 giá trị cùng ra): zip

```java
// flow1[0] đợi flow2[0] cùng emit
flow1.zip(flow2) { a, b -> ... }
/* System.out:
1 -> one
2 -> two
3 -> three
*/
```

### Cộng 2 flow: combine

```java
// Chỉ cần flow1[0] flow2[0] có giá trị
flow1.combine(flow2) { a, b -> ... }
/* System.out:
1 -> one
2 -> one
2 -> two
3 -> two
3 -> three
*/
```

## Reference

- <https://viblo.asia/p/cung-hoc-kotlin-coroutine-phan-10-flow-part-3-of-3-aWj53G4o56m>