# Button

- [Button](#button)
  - [Simple Button](#simple-button)


## Simple Button

```kotlin
@Composable
fun CustomButtom() {
    // Nhận button trả về
    var result by remember { mutableStateOf(1) }

    // Gọi trong View
    Button(onClick = {
        // gởi ra ngoài
        result = (1..6).random()
    }) {
        Text(stringResource(R.string.roll))
    }
}
```