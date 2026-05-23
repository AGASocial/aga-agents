# Hilt Dependency Injection

This skill provides a standard setup for Hilt in an Android project.

## 1. Dependency Setup
Add to `libs.versions.toml`:
```toml
hilt = "2.51.1"
androidxHiltNavigationCompose = "1.2.0"

[libraries]
hilt-android = { group = "com.google.dagger", name = "hilt-android", version.ref = "hilt" }
hilt-compiler = { group = "com.google.dagger", name = "hilt-android-compiler", version.ref = "hilt" }
androidx-hilt-navigation-compose = { group = "androidx.hilt", name = "hilt-navigation-compose", version.ref = "androidxHiltNavigationCompose" }

[plugins]
hilt-android = { id = "com.google.dagger.hilt.android", version.ref = "hilt" }
```

## 2. Application Class
Annotate the Application class:
```kotlin
@HiltAndroidApp
class LeadgenApplication : Application()
```

## 3. Providing Dependencies
Use Hilt Modules to provide instances of interface or external classes.

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {

    @Provides
    @Singleton
    fun provideRetrofit(): Retrofit {
        return Retrofit.Builder()
            .baseUrl(BASE_URL)
            .addConverterFactory(Json.asConverterFactory("application/json".toMediaType()))
            .build()
    }
}
```

## 4. Injecting in Compose
Use `hiltViewModel()` for automatic ViewModel injection.

```kotlin
@Composable
fun MainScreen(
    viewModel: MainViewModel = hiltViewModel()
) {
    // ...
}
```

## Best Practices
- **Use `@Inject constructor`** whenever possible instead of modules.
- **Prefer Interface Binding** with `@Binds` in abstract modules.
- **Scope appropriately**: Use `@Singleton` for app-wide services, `@ActivityRetainedScoped` for ViewModels.
