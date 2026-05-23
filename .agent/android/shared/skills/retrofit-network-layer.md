# Retrofit Network Layer

This skill covers setting up a robust network layer using Retrofit, OkHttp, and Kotlin Serialization.

## 1. Setup
Add dependencies to `libs.versions.toml`:
```toml
retrofit = "2.11.0"
okhttp = "4.12.0"
kotlinxSerialization = "1.6.3"

[libraries]
retrofit-core = { group = "com.squareup.retrofit2", name = "retrofit", version.ref = "retrofit" }
retrofit-kotlin-serialization = { group = "com.jakewharton.retrofit", name = "retrofit2-kotlinx-serialization-converter", version = "1.0.0" }
okhttp-logging = { group = "com.squareup.okhttp3", name = "logging-interceptor", version.ref = "okhttp" }
```

## 2. API Interface
```kotlin
interface LeadApi {
    @GET("leads")
    suspend fun getLeads(): List<LeadDto>

    @POST("leads/profile")
    suspend fun profileCompany(@Body request: ProfileRequest): ProfileResponse
}
```

## 3. Data Transfer Objects (DTOs)
Use `@Serializable` and `SerialName` for clarity.
```kotlin
@Serializable
data class LeadDto(
    val id: String,
    @SerialName("company_name") val companyName: String,
    val website: String
)
```

## 4. Error Handling
Create a wrapper for network responses.
```kotlin
sealed class NetworkResult<T> {
    data class Success<T>(val data: T) : NetworkResult<T>()
    data class Error<T>(val message: String, val code: Int? = null) : NetworkResult<T>()
    class Loading<T> : NetworkResult<T>()
}
```

## Best Practices
- **Use `suspend` functions** in API interfaces.
- **Log network traffic** in debug builds using `HttpLoggingInterceptor`.
- **Add a timeout** to OkHttp client.
- **Isolate DTOs** from domain models.
