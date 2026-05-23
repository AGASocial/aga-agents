# Android Project Initialization

This skill provides instructions for setting up a modern Android project using Kotlin, Jetpack Compose, and Gradle Version Catalogs.

## Prerequisites
- Android Studio Jellyfish or newer.
- JDK 17+.

## Project Setup Steps

### 1. Initialize with Version Catalog
Create `gradle/libs.versions.toml`:
```toml
[versions]
agp = "8.4.0"
kotlin = "1.9.23"
coreKtx = "1.13.1"
composeBom = "2024.05.00"

[libraries]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
androidx-compose-bom = { group = "androidx.compose", name = "compose-bom", version.ref = "composeBom" }
androidx-ui = { group = "androidx.compose.ui", name = "ui" }
androidx-material3 = { group = "androidx.compose.material3", name = "material3" }

[plugins]
android-application = { id = "com.android.application", version.ref = "agp" }
kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
```

### 2. Configure Root `build.gradle.kts`
```kotlin
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
}
```

### 3. Configure App `build.gradle.kts`
```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
}

android {
    namespace = "com.example.app"
    compileSdk = 34

    defaultConfig {
        applicationId = "com.example.app"
        minSdk = 24
        targetSdk = 34
        versionCode = 1
        versionName = "1.0"
    }

    buildFeatures {
        compose = true
    }

    composeOptions {
        kotlinCompilerExtensionVersion = "1.5.11"
    }
}

dependencies {
    implementation(libs.androidx.core.ktx)
    implementation(platform(libs.androidx.compose.bom))
    implementation(libs.androidx.ui)
    implementation(libs.androidx.material3)
}
```

## Best Practices
- Use **Version Catalogs** for all dependencies.
- Enable **Non-transitive R classes** in `gradle.properties`.
- Use **Kotlin DSL** for Gradle files.
