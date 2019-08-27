# Dagger 2

Tuân thủ nguyên tắc DI (Dependency Injection).
Tránh khởi tạo đối tượng trong 1 đối tượng.

- [Dagger 2](#dagger-2)
  - [Cài đặt](#c%c3%a0i-%c4%91%e1%ba%b7t)
  - [Simple Start](#simple-start)
    - [Dùng Dagger](#d%c3%b9ng-dagger)
    - [Một số annotation đặc biệt](#m%e1%bb%99t-s%e1%bb%91-annotation-%c4%91%e1%ba%b7c-bi%e1%bb%87t)
  - [Tiêm Pet vào Activity](#ti%c3%aam-pet-v%c3%a0o-activity)
    - [Không dùng Anotation (@Inject)](#kh%c3%b4ng-d%c3%b9ng-anotation-inject)
    - [Dùng Anotation (inject) - tiêm qua Application](#d%c3%b9ng-anotation-inject---ti%c3%aam-qua-application)
  - [Tham khảo](#tham-kh%e1%ba%a3o)

## Cài đặt

build.gradle

```js
dependencies {
    api 'com.google.dagger:dagger:2.17'
    api 'com.google.dagger:dagger-android:2.17'
    annotationProcessor 'com.google.dagger:dagger-android-processor:2.17'
    annotationProcessor 'com.google.dagger:dagger-compiler:2.17'
}
```

## Simple Start

```java
// Có đối tượng pet trong Person
public class Pet {
    public Pet() {}
}

public class Person {
    Pet pet;
}

// Ta có thể tránh khởi tạo trực tiếp pet trong Person
public Person() {
    this.pet = new Pet();
}
// Có thể dùng Tiêm gián tiếp
public Person(Pet pet) {
    this.pet = pet;
}
```

### Dùng Dagger

```java
// Khởi tạo
@Provides
public Pet providePet(){
    return new Pet();   // Khởi tạo bên ngoài
}

// Sử dụng
public class Person {
    @Inject
    Pet pet;    // Tiêm vào thông qua Dagger
}
```

### Một số annotation đặc biệt

![annotation]("dagger2_map.png")

- @Module : cho các lớp mà có phương thức cung cấp sự phụ thuộc.
- @Providers : cho các phương thức bên trong các lớp @Module.
- @Component : là cầu nối interface giữa các modules và injection.
- @Inject : để yêu cầu một sự phụ thuộc ( một hàm khởi tạo, một trường, hoặc một phương thức).

## Tiêm Pet vào Activity

### Không dùng Anotation (@Inject)

```java
import dagger.Module;
import dagger.Provides;
import dagger.Component;

public class Pet {
    public String name;

    public Pet(String name) { this.name = name; }
}
// -----------------------

@Module
public class PetModule {
    @Provides
    Pet petProvider() {
        return new Pet("Dog");
    }
}
// -----------------------
@Component(modules = {PetModule.class})
public interface PetComponent {
    Pet petProvider();
}
// -----------------------

// Sử dụng trong Activity
PetComponent petComponent = DaggerPetComponent.builder().petModule(new PetModule()).build();
Pet pet = petComponent.petProvider();

Log.e(TAG, pet.name); // Dog
```

### Dùng Anotation (inject) - tiêm qua Application

```java
import dagger.Module;
import dagger.android.ContributesAndroidInjector;
import dagger.BindsInstance;
import dagger.Component;
import dagger.android.AndroidInjectionModule;

// Tạo Module chứa Activity
@Module
public abstract class ActivityModule {
    @ContributesAndroidInjector()
    abstract MainActivity contributeMainActivity();
}
//-------------------------------------------

// Tạo Component Chứa Activity Module
@Component(modules = {
        AndroidInjectionModule.class,
        PetModule.class,
        ActivityModule.class
})
public interface AppComponent {

    @Component.Builder
    interface Builder {
        @BindsInstance
        Builder application(Application application);
        AppComponent build();
    }

    void inject(MyApplication app);
}
// --------------------------------------------

public class MyApplication extends Application implements HasActivityInjector {
    @Inject
    DispatchingAndroidInjector<Activity> activityDispatchingAndroidInjector;

    @Override
    public void onCreate() {
        super.onCreate();

        DaggerAppComponent.builder()
                .application(this)
                .build()
                .inject(this);
    }

    @Override
    public DispatchingAndroidInjector<Activity> activityInjector() {
        return activityDispatchingAndroidInjector;
    }
}
// ----------------------------------
// Manifest
//<application
//        android:name=".MyApplication"...
// -------------------------------------
// Sử dụng trong Activity
@Inject
Pet pet;

@Override
protected void onCreate(Bundle savedInstanceState) {
    AndroidInjection.inject(this);
    super.onCreate(savedInstanceState);

    Log.e(TAG, pet.name); // Dog
}
```

---

## Tham khảo

- <https://viblo.asia/p/android-injector-trong-dagger2-924lJY6aZPM>
- <https://viblo.asia/p/dependency-injection-voi-dagger-2-trong-android-Az45bAYwlxY>
