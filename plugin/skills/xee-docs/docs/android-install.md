Add the Xee SDK to your Android app with Gradle, then activate it once at launch with your SDK key. Every other SDK call depends on this step.

> [!IMPORTANT]
> The Android SDK is in preview and is not on Maven Central yet. Use it with Google Play license testers until the checks in [Test Android purchases](/docs/android-sandbox-testing) pass.

## Requirements

- Android 6 (API 23) or later, and a `targetSdk` of 35 or later for Google Play Billing 9.
- Kotlin. Java apps use the callback forms in `XeeJava`.
- The app in Xee, with its package name set in App settings, Android. It is the same Xee app as your iOS app, with the same SDK key.

## Add the SDK

Until the first release, build the library from the SDK repository with `./gradlew :xee:publishToMavenLocal`, then add it:

```kotlin
// settings.gradle.kts: add mavenLocal() to dependencyResolutionManagement.repositories
// app/build.gradle.kts
dependencies {
    implementation("ai.xee:xee-android:0.1.0-preview")
}
```

The SDK brings Google Play Billing with it, so do not declare another version.

## Activate

Copy the **SDK key** from App settings, Android, and call `activate` in `Application.onCreate`. It returns at once. The first sync, and any purchase left over from an earlier launch, run behind it.

```kotlin snippet="android-activate"
class MyApp : Application() {
    override fun onCreate() {
        super.onCreate()
        Xee.activate(this, "YOUR_SDK_KEY")
    }
}
```


If you already know who the person is at launch, pass their ID:

```kotlin snippet="android-activate-config"
val config = XeeConfig.Builder("YOUR_SDK_KEY")
    .customerUserId("customer-1")
    .build()
Xee.activate(context, config)
```


## Identify users

Without an ID, the person is an anonymous profile that lives on this install. Call `Xee.identify` when they sign in, so their access follows them to a new device, and to iOS. See [Identify users](/docs/android-identify-users).

## Google's Data Safety form

Google asks what your app collects. For the SDK, declare:

| Data | Why | Notes |
| --- | --- | --- |
| User IDs: the profile ID and any customer ID you pass | App functionality, analytics | |
| Purchase history | App functionality, analytics | |
| App interactions: paywall views and the events you track | Analytics | Not sent after `Xee.optOutTracking()` |
| Device or other IDs: App Set ID | Analytics | Only if your app ships `play-services-appset` |
| Device or other IDs: Advertising ID | Analytics, attribution | Off by default. Needs `collectAdvertisingId(true)`, the `AD_ID` permission and `play-services-ads-identifier` |

Data goes to Xee's servers over HTTPS. Check this against your own app's use before you submit.

## What is different from iOS

- `makePurchase` needs the `Activity` on screen.
- Xee's server finishes purchases for you, so there is no manual-finish mode and no `onUnfinishedTransaction`.
- Refund Saver, Apple Ads and offer-code sheets are Apple's and have no Android call. `Xee.presentCodeRedemption` opens Google's promo-code page.
- App Store keyword and listing tools cover the App Store only.

Next: [Set up Google Play](/docs/android-google-play-setup).
