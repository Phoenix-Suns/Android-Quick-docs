# Input Compose Views

- [Input Compose Views](#input-compose-views)
  - [TextField: Nhập text](#textfield-nhập-text)
  - [Switch: công tắt](#switch-công-tắt)


## TextField: Nhập text

```kotlin
import androidx.compose.runtime.setValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.getValue

@Composable
fun CustomTextField() {

    // Lưu giá trị TextField
    var amountInput by remember { mutableStateOf("") }
    // Quản lý Focus Field
    val focusManager = LocalFocusManager.current

    TextField(
        value = amountInput,
        onValueChange = { amountInput = it },
        label = { 
            Text(stringResource(R.string.bill_amount)) 
        },
        modifier = Modifier.fillMaxWidth(),

        // Text Type
        keyboardOptions = KeyboardOptions.Default.copy(
            // Input Number
            keyboardType = KeyboardType.Number,
            // Enter = Next = Qua field tiếp theo
            imeAction = ImeAction.Next
        ),
        keyboardActions = KeyboardActions(
            // First focus
            onNext = { focusManager.moveFocus(FocusDirection.Down) }
        ),
    )
}

// Click Done để Ẩn bàn phím
TextField(
    keyboardOptions = KeyboardOptions.Default.copy(
            keyboardType = KeyboardType.Number,
            imeAction = ImeAction.Done
        ),
    keyboardActions = KeyboardActions(
        // Huỷ Focus, ẩn bàn phím
        onDone = { focusManager.clearFocus() }),
)
```

## Switch: công tắt

```kotlin
import androidx.compose.runtime.setValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.getValue

@Composable
fun CustomTextField() {
    var roundUp by remember { mutableStateOf(false) }

    Switch(
        modifier = modifier
            .fillMaxWidth()
            .wrapContentWidth(Alignment.End),
        checked = roundUp,
        onCheckedChange = { roundUp = it },
        colors = SwitchDefaults.colors(
            uncheckedThumbColor = Color.DarkGray
        )
    )
}
```