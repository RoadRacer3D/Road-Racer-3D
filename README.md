
name: Build Car Racing APK

on:
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Set up Android
        uses: android-actions/setup-android@v3
        with:
          packages: 'platform-tools'

      - name: Install SDK
        run: sdkmanager "platforms;android-35" "build-tools;35.0.0"

      - name: Set up Gradle
        uses: gradle/actions/setup-gradle@v6
        with:
          gradle-version: '8.7'

      - name: Extract game project
        run: |
          mkdir game
          unzip -q "$(find . -maxdepth 1 -name '*.zip' -print -quit)" -d game

      - name: Build APK
        run: |
          PROJECT_DIR=$(dirname "$(find game -name settings.gradle.kts -o -name settings.gradle | head -n 1)")
          cd "$PROJECT_DIR"
          gradle assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: Road-Racer-3D-APK
          path: game/**/app/build/outputs/apk/debug/app-debug.apk
