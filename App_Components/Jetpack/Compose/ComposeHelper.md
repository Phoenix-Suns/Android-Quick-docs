# Compose Helper Views: các lớp hỗ trợ

## Spacer: Tạo khoảng trống

```kotlin
Spacer(modifier = Modifier.height(16.dp))
```

## Modifier: Padding, Size, Shape

```kotlin
Image(
	modifier = Modifier
		.padding(8.dp)		// Padding
		.size(40.dp)    	// Image Size
		.clip(CircleShape)  // Clip Image Circle
)
```