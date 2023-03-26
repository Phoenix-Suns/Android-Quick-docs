# Layout Views

- [Layout Views](#layout-views)
  - [Column: Sắp dọc](#column-sắp-dọc)
  - [Row: Sắp ngang](#row-sắp-ngang)


## Column: Sắp dọc

```kotlin
Column(
    modifier = Modifier
        .fillMaxSize()
        .wrapContentSize(Alignment.Center),
    horizontalAlignment = Alignment.CenterHorizontally
) { 
    // Child Views
}
```

## Row: Sắp ngang

```kotlin
Row(
    modifier = Modifier
        .fillMaxSize()
        .wrapContentSize(Alignment.Center),
    horizontalAlignment = Alignment.CenterHorizontally
) { 
    // Child Views
}
```