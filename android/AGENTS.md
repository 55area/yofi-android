# Android instructions

Read the [Android README](README.md) for the toolchain, build commands, and source layout.

- Open `android/` in Android Studio and run Gradle from this directory using the checked-in wrapper and JDK documented in the README.
- Keep the starter app in `:app`. Introduce additional modules when an accepted increment establishes a concrete ownership or dependency boundary.
- Define user-facing text in Android string resources. Keep English defaults in `res/values/` and maintain the supported translations and locale configuration when text or language support changes.
- Keep SDK paths, signing credentials, and generated artifacts local.
- Run the affected build, lint, and tests. Record emulator or physical-device evidence separately when verifying device behavior.
- Preserve Android 8.0 (API 26) support. Verify startup on API 26 and a recent API when changing the build toolchain or startup dependencies.
