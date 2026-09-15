# Flutter App Builder Action

A custom GitHub Action wrapper that simplifies Flutter CI/CD pipelines. It automates Java setup, Flutter environment configuration, dependency fetching, and handles both standard builds and **Shorebird** over-the-air (OTA) updates in a single step.

## 🚀 Features
* ☕ **Java & Flutter Setup:** Configures Java 17 (Zulu) and caches your Flutter SDK installations.
* 📦 **Dependency Management:** Automatically executes `flutter pub get`.
* ⚡ **Dual Build Engine:** Seamlessly switches between standard `flutter build` commands and `shorebird` release/patch updates.

---

## 🛠️ Inputs

| Input | Description | Required | Default |
| :--- | :--- | :---: | :--- |
| `type` | The build pipeline to use (`flutter build` or `shorebird`) | **Yes** | `'flutter build'` |
| `target` | Platform compilation target (`apk`, `appbundle`, `ios`, `web`). *Note: Shorebird accepts `android` or `ios`.* | **Yes** | `'apk'` |
| `build-mode` | Standard Flutter compilation flags (`release`, `debug`, `profile`) | No | `'release'` |
| `flutter-version`| The target Flutter SDK branch or version channel | No | `'stable'` |
| `shorebird-command`| The deployment mechanism if using Shorebird (`release` or `patch`) | No | `'release'` |
| `shorebird-token` | Your authenticated Shorebird token secret. *Required if type is `shorebird`.* | No | `""` |

---

## 📖 Usage Examples

Incorporate this action into your application's workflow file (e.g., `.github/workflows/build.yml`) after checking out your repository code.

### Example 1: Standard Flutter APK Build
```yaml
name: Build Android App

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Compile Release APK
        uses: Justine-zip/flutter-app-builder@v1
        with:
          type: 'flutter build'
          target: 'apk'
          build-mode: 'release'
```

### Example 2: Shorebird Android Release
Before running this, make sure to add your `SHOREBIRD_TOKEN` to your GitHub Repository Secrets.

```yaml
name: Deploy Shorebird Engine

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Push Shorebird OTA Release
        uses: Justine-zip/flutter-app-builder@v1
        with:
          type: 'shorebird'
          shorebird-command: 'release'
          target: 'android'
          shorebird-token: \${{ secrets.SHOREBIRD_TOKEN }}
```

---

## 📦 Output Artifacts
When executing standard builds, the action automatically packages and attaches items found at these paths into your workflow summary:
* `build/app/outputs/flutter-apk/*.apk`
* `build/app/outputs/bundle/release/*.aab`
