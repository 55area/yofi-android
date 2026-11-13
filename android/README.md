# Yo-Fi for Android

This is the native Android workspace for Yo-Fi. The initial Kotlin and Jetpack Compose app displays a starter screen with resources for English, Brazilian Portuguese, and Spanish. Android 13 and later can expose these choices in system settings. The in-app language selector and the first Wi-Fi journey will be specified during Android development.

## Requirements

- Android Studio Rabbit 2 (2026.2.2) or newer with support for Android Gradle Plugin 9.5.1.
- JDK 25 as the Gradle runtime.
- Android SDK Platform 36 and Build Tools 36.0.0.
- An emulator or device running Android 8.0 (API 26) or later to install the debug app.

Open this directory in Android Studio and select JDK 25 under **Gradle JDK**. For command-line builds, point `JAVA_HOME` to JDK 25 and configure the Android SDK through `ANDROID_HOME` or Android Studio's local SDK configuration.

Gradle runs on JDK 25. The app compiles Java and Kotlin to JVM 17 bytecode before Android dexing and supports devices from API 26.

From the repository root, run:

```sh
cd android
./gradlew :app:assembleDebug :app:lintDebug
```

The debug APK is generated at `app/build/outputs/apk/debug/app-debug.apk`. Use `gradlew.bat` on Windows.

## Structure

- `app/src/main/` contains the launcher activity, manifest, and localized resources.
- `app/src/test/` and `app/src/androidTest/` are reserved for JVM and device tests.
- `gradle/wrapper/` pins Gradle 9.9.1 and its distribution checksum.

The initial application ID is `com.teknolista.yofi`. Confirm the application ID and supported device range before preparing a release.

See the [repository README](../README.md), [Android instructions](AGENTS.md), and [shared OpenSpec project](../openspec/).
