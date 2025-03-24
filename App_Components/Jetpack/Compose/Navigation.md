# Compose Navigation

- [Compose Navigation](#compose-navigation)
  - [Setup](#setup)
  - [Đi đến View](#đi-đến-view)
  - [Cài đặt Định hướng](#cài-đặt-định-hướng)
  - [Gởi dữ liệu qua View](#gởi-dữ-liệu-qua-view)
  - [Gởi dữ liệu qua NavHostController](#gởi-dữ-liệu-qua-navhostcontroller)
  - [Nhận dữ liệu trả về](#nhận-dữ-liệu-trả-về)
  - [Dùng Nav toàn Ứng dụng (Cách 1)](#dùng-nav-toàn-ứng-dụng-cách-1)
  - [Reference](#reference)


## Setup

```json
dependencies {
    //...
    implementation "androidx.navigation:navigation-compose:2.4.2"
    implementation "androidx.compose.runtime:runtime-livedata:1.3.2"
}
```

## Đi đến View

```kotlin
@Composable
fun MainApp() {
    val navController = rememberNavController()

    NavHost(
        navController = navController,
        startDestination = "first_page", // Default "first_page"
    ) {
        // Setup First Page
        composable(route = "first_page") {
            FirstPage()
        }
        // Setup Second Page
        composable(route = "second_page") {
            SecondPage(
                // ===== Navigate to First Page =====
                onNavigateToFirstPage = { navController.navigate("first_page") },
            )
        }
    }
}

@Composable
fun FirstPage() { }

@Composable
fun SecondPage(
    onNavigateToFirstPage: () -> Unit,
    /*...*/
) {
    /*...*/
    Button(onClick = onNavigateToFirstPage) {
        Text(text = "See First Page")
    }
}
```

## Cài đặt Định hướng

```kotlin
// Pop everything up to the "home" destination off the back stack before
// navigating to the "friendslist" destination
navController.navigate("friendslist") {
    popUpTo("home")
}

// Pop everything up to and including the "home" destination off
// the back stack before navigating to the "friendslist" destination
navController.navigate("friendslist") {
    popUpTo("home") { inclusive = true }
}

// Navigate to the "search” destination only if we’re not already on
// the "search" destination, avoiding multiple copies on the top of the
// back stack
navController.navigate("search") {
    launchSingleTop = true
}
```

## Gởi dữ liệu qua View

```kotlin
// ===== Gởi
composable(route = "send_info") { backStackEntry ->
    // Gọi trong SendInfoPage: onNavigateToReceivePage?.invoke("abc", "xsss")
    SendInfoPage(onNavigateToReceivePage = { username, password ->
        navController.navigate("receive_info?username=" + username + "&password=" + password)
        // Hoặc
        //navController.navigate("receive_info/{" + username + "}/{" + password +"}")
    })
}

// ===== Nhận
composable(
    route = "receive_info?username={username}&password={password}",
    arguments = listOf(
        navArgument("username") { type = NavType.StringType },
        navArgument("password") { type = NavType.StringType },
    )
) { backStackEntry ->

    ReceiveInfoPage(
        username = backStackEntry.arguments?.getString("username"),
        password = backStackEntry.arguments?.getString("password"),
    )
}
```

### Gởi, nhận object

```kotlin
@Parcelize
data class User(val id: Int, val name: String) : Parcelable

@Composable
fun ScreenA(navController: NavController) {
    val user = User(id = 1, name = "Nguyen Van A")
    Button(onClick = {
        val route = "screenB?user=${Uri.encode(Gson().toJson(user))}"
        navController.navigate(route)
    }) {
        Text("Go to Screen B")
    }
}

@Composable
fun ScreenB(user: User?) {
    Text("Received: ${user?.name}, ID: ${user?.id}")
}

@Composable
fun AppNavigation() {
    val navController = rememberNavController()
    NavHost(navController = navController, startDestination = "screenA") {
        composable("screenA") { ScreenA(navController) }
        composable("screenB?user={user}") { backStackEntry ->
            val userJson = backStackEntry.arguments?.getString("user")
            val user = userJson?.let { Gson().fromJson(it, User::class.java) }
            ScreenB(user)
        }
    }
}

// ===== Lấy trong ViewModel
class ScreenBViewModel(
    savedStateHandle: SavedStateHandle
) : ViewModel() {
    private val userJson: String? = savedStateHandle["user"]
    private val user = MutableStateFlow<User?>(null)

    init {
        userArg?.let {
            val book: Book = Json.decodeFromString(bookArg)
            user.value = userJson?.let { Gson().fromJson(it, User::class.java) }
        }
    }
}
```

## Gởi dữ liệu qua NavHostController

```kotlin
// ===== Gởi (@Composable fun Screen A)
navHostController.currentBackStackEntry
    ?.savedStateHandle
    ?.set("book_id", "book_1")

// ===== Nhận (@Composable fun Screen B)
val bookID = navController.previousBackStackEntry
    ?.savedStateHandle
    ?.get<String>("book_id")
```

## Nhận dữ liệu trả về

```kotlin
// ===== Trả dữ liệu về
composable(
    route = "receive_info"
) { backStackEntry ->

    ReceiveInfoPage(
        // Gọi trong ReceiveInfoPage: popBack?.invoke("dfa")
        popBack = { userId ->
            
            navController.previousBackStackEntry
                ?.savedStateHandle
                ?.set("user_id", userId)
            navController.popBackStack()
        }
    )
}

// ===== Nhận dữ liệu trả về
composable(route = "send_info") { backStackEntry ->

    val userIdResult = navController.currentBackStackEntry
        ?.savedStateHandle
        ?.getLiveData<String>("user_id")?.observeAsState()
    val userId = userIdResult?.value

    SendInfoPage(userId = userId)
}
```

## Dùng Nav toàn Ứng dụng (Cách 1)

```kotlin
val LocalNavController = compositionLocalOf<NavHostController> {
    error("No LocalNavController provided")
}

@Composable
fun MainApp() {
    val navController = rememberNavController()

    CompositionLocalProvider(LocalNavController provides navController) {
        NavHost(
            navController = navController,
            startDestination = "first_page", // Default "first_page"
        ) {
            // Setup First Page
            composable(route = "first_page") {
                FirstPage()
            }
            // Setup Second Page
            composable(route = "second_page") {
                SecondPage()
            }
        }
    }
}

@Composable
fun FirstPage() { }

@Composable
fun SecondPage() {
    val navController = LocalNavController.current
    /*...*/
    Button(onClick = {
            // ===== Navigate to First Page =====
            navController.navigate(_2_TipsDestination.route)
        }) {
        Text(text = "first_page")
    }
}
```

---

## Reference

- https://developer.android.com/jetpack/compose/state?hl=vi
- 