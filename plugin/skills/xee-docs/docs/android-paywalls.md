Show a paywall or a view from Xee on Android. Paywalls, views, tests and languages are the same ones as on iOS: you change them in one place, and only the product IDs differ per store.

## Get a paywall

```kotlin snippet="android-get-paywall"
try {
    val variant = Xee.getPaywallVariant("main")
    val title = variant.config.string("title", "Go premium")
    val products = Xee.getPaywallVariantProducts(variant)
    show(variant, products)
    render(title)
    Xee.logShowPaywallVariant(variant)
} catch (e: XeeError) {
    showError(e)
}
```


Read each value with the default your code already has. A missing value or one of another type gives that default, so a paywall never breaks because of the server. The getters are `string`, `int`, `double`, `bool` and `stringList`.

`getPaywallVariantProducts` asks Google for each product's price and offers. Show `localizedPrice` as it is. A product Google does not offer is left out, with a log line that says why.

Call `logShowPaywallVariant` when the paywall is on screen. [A/B tests](/docs/ab-tests) count a person from this call, so a test has no numbers without it.

## With no connection

The SDK saves the last answer for each paywall. For a person with no connection on their first launch, add a fallback file:

1. In Xee, open Products, Fallbacks, and download the file **for Google Play**. It has your Google product IDs, which the App Store's file does not.
2. Put it in your app's `assets` folder, and set it at launch:

```kotlin snippet="android-fallback"
Xee.setFallback(context, XeeFallbackFile.Asset("xee_fallback.json"))
```


## Views

A view is a screen or flow that sells nothing, such as onboarding. `getView` never throws: if it cannot load, every getter gives your code's default, so the screens always show.

```kotlin snippet="android-view"
val view = Xee.getView("onboarding")
render(view.config.string("title", "Welcome"))
Xee.logShowView(view)
```


See [Views](/docs/views) for how views and their tests work.

## Tests on two stores

One person gets one arm. Results combine both stores, and prices can differ between stores, so read a test that sells on both with that in mind.
