# Compose properties chung

Container:
- Column
- Row

Input:
Output:
Button:
Helper:
Other:

## Lưu dữ liệu Output trả về

```kotlin
import androidx.compose.runtime.setValue
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf


// Lưu dữ liệu trả về
var amountInput by remember { mutableStateOf("") }

// Gọi trong View
TextField(
    value = amountInput,
    onValueChange = { amountInput = it },
)
```

## Custom View

```kotlin
// ===== Custom Switch =====
@Composable
fun RoundTheTipRow(
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
// Trong View
RoundTheTipRow(roundUp = roundUp, onRoundUpChanged = { roundUp = it })

// Lấy dữ liệu trả về
var roundUp by remember { mutableStateOf(false) }
```
