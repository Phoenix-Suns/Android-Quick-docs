# Compose properties chung

Container:
- Column
- Row

Input:
Output:
Button:
Helper:
Other:

## Button

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

## Lưu dữ liệu Output trả về

```kotlin
// Khi sử dụng remember phải import
import androidx.compose.runtime.setValue
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf

@Composable
fun CustomTextField() {
    // Lưu dữ liệu trả về
    var amountInput by remember { mutableStateOf("") }

    // Gọi trong View
    TextField(
        value = amountInput,
        onValueChange = { amountInput = it },
    )
}
```

## Custom View

```kotlin
// ===== Custom Switch =====
@Composable
fun CustomSwitch(
    roundUp: Boolean,
    onRoundUpChanged: (Boolean) -> Unit,
    modifier: Modifier = Modifier
) {
    Row(
        modifier = modifier
            .fillMaxWidth()
            .wrapContentWidth(Alignment.End),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Text(stringResource(R.string.round_up_tip))
        Switch(
            modifier = modifier
                .fillMaxWidth()
                .wrapContentWidth(Alignment.End),
            checked = roundUp,
            onCheckedChange = onRoundUpChanged,
            colors = SwitchDefaults.colors(
                uncheckedThumbColor = Color.DarkGray
            )
        )
    }
}


// ===== Using =====
@Composable
fun MainView() {
    // Lấy dữ liệu trả về
    var roundUp by remember { mutableStateOf(false) }

    // Gọi trong Trong View
    CustomSwitch(roundUp = roundUp, onRoundUpChanged = { roundUp = it })
}
```
