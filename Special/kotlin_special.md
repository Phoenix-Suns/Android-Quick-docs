# Kotlin Special

- [Kotlin Special](#kotlin-special)
  - [Enum](#enum)
  - [Extention Extented](#extention-extented)
  - [Scrope funtions](#scrope-funtions)

## Enum

```kotlin
enum class DateTimeFormat constructor(val value: String) {
    DDMMYYYY_HHMM("dd/MM/yyyy HH:mm"),
    WEEK_MONTH_DAY_YEAR("EEEE, dd MMMM, yyyy"),
    DDMMYYYY("dd/MM/yyyy"),
    HHMM("HH:mm")
}

// Using
AppEnum.DateTimeFormat.HHMM.value // output: "HH:mm"
```

## Extention Extented

```kotlin
fun <T : ImageView> T.loadWithCoil(url: String?) {
    //...
}
```

## Scrope funtions

![](scrope_functions.png)

- <https://kotlinlang.org/docs/scope-functions.html#function-selection>


