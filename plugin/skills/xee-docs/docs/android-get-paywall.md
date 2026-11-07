Your app asks Xee for a paywall by its paywall ID and gets back two things: the paywall's values (headline, button text, toggles, anything you made a variable) and the products it offers. You draw the paywall yourself with those. Paywalls, tests and languages are the same ones as on iOS; only the product IDs differ per store.

## Before you start

1. Add your products in Xee under **Products**, each with its Google product ID and base plan. A product with no Google ID is left out on Android.
2. Create the paywall: **Paywalls**, then **Create paywall**. The **Paywall ID** (for example `main`) is what your app passes to the SDK.
3. [Install the SDK](/docs/android-install) and activate it.

## Get the paywall

```kotlin snippet="android-get-paywall-basic"
val variant = try {
    Xee.getPaywallVariant("main")
} catch (e: XeeError) {
    null // Show the paywall with the defaults in your code.
}
```

| Parameter | Default | What it does |
| --- | --- | --- |
| `paywallId` | required | The paywall ID you set in the dashboard. |
| `locale` | the device's language | Leave it out: text comes in the language the device runs in, when you offer it in Xee, otherwise in your source language. Pass a code such as `fr` only to show another language. See [Languages](/docs/android-localization). |
| `loadTimeoutMs` | 5,000 | How long to wait for the server, in milliseconds. It cannot be less than 1,000. |

```kotlin snippet="android-get-paywall-options"
val variant = Xee.getPaywallVariant("main", locale = "fr", loadTimeoutMs = 3_000)
```

The SDK saves the last answer for each paywall on the device. If the server cannot be reached, you get that copy, then the [fallback file](#with-no-connection). With neither, the call throws, so keep working defaults in your code. It also throws when the paywall ID does not exist, the paywall is inactive or it has no version yet.

## What you get back

`getPaywallVariant` returns an `XeePaywallVariant`:

| Property | What it is |
| --- | --- |
| `paywallId` | The paywall ID you asked for. |
| `name` | The version and arm, for example `main v4`. |
| `variationId` | Which variation this person got. During a test it names the arm; purchases are counted by it. |
| `config` | The paywall's values. |
| `products` | The products this version offers, in order, with their Google IDs. |
| `abTestName` | The name of the running test, or `default` when there is none. |
| `locale` | The language the text came in. |

## Read the values

Read each value with the default your code already has. A missing value, or one of another type, gives that default, so a paywall never breaks because of the server.

```kotlin snippet="android-read-values"
val config = variant.config
val headline = config.string("headline", "Unlock Unlimited Shorts")
val showTrialToggle = config.bool("show_trial_toggle", true)
val features = config.stringList("features", listOf("Unlimited shorts", "AI captions"))
```

| Variable type | Getter | Kotlin |
| --- | --- | --- |
| `string` | `string` | `String` |
| `int` | `int` | `Int` |
| `double` | `double` | `Double` |
| `bool` | `bool` | `Boolean` |
| `string_list` | `stringList` | `List<String>` |

> [!NOTE]
> Each version is pushed for an app build. The SDK sends your app's `versionCode` with every request, and Xee serves the newest version pushed for that build, or for the closest older one, so an older build keeps getting values its code understands. Only the latest version gets the running test.

## Get the products

Pass the paywall to `getPaywallVariantProducts`. It asks Google for each product's price and offers, in the order the version lists them:

```kotlin snippet="android-product-rows"
@Composable
fun ProductRows(products: List<XeePaywallVariantProduct>, onBuy: (XeePaywallVariantProduct) -> Unit) {
    Column {
        products.forEach { product ->
            Button(onClick = { onBuy(product) }) {
                val trial = product.freeTrialPeriod?.let { " · free trial" }.orEmpty()
                Text("${product.title ?: product.vendorProductId}: ${product.localizedPrice.orEmpty()}$trial")
            }
        }
    }
}
```

Each item is an `XeePaywallVariantProduct`:

| Property | What it is |
| --- | --- |
| `vendorProductId`, `basePlanId` | The Google product ID and base plan. |
| `title`, `description` | From Play Console. |
| `localizedPrice` | The price formatted for the person's country, for example `$9.99`. Show it as it is. |
| `priceAmountMicros`, `currencyCode` | The price in millionths and its currency, for your own formatting. |
| `billingPeriod` | The period in ISO 8601, such as `P1M` for a month or `P1Y` for a year. |
| `freeTrialPeriod` | The free trial's length when the offer starts with one, such as `P1W`. |
| `isSubscription` | `false` for a one-time product. |
| `productDetails` | Google's own `ProductDetails`, for anything else. |

A product Google does not offer, such as one not active in Play Console, is left out, with a log line that says why.

> [!IMPORTANT]
> Do not hardcode product IDs or how many there are. Products are chosen per paywall version, so a new version can offer three products where the last one had two. Show every product you get back.

## With no connection

For a person with no connection on their first launch, add a fallback file:

1. In Xee, open **Products › Fallbacks**, and download the file **for Google Play**. It has your Google product IDs, which the App Store's file does not.
2. Put it in your app's `assets` folder, and set it at launch:

```kotlin snippet="android-fallback"
Xee.setFallback(context, XeeFallbackFile.Asset("xee_fallback.json"))
```

`XeeFallbackFile.RawResource` and `XeeFallbackFile.File` read it from `res/raw` or from a file instead.

## Put it together

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

1. Get the paywall and read its values; fall back to your defaults on error.
2. Get its products.
3. Show the paywall, then call `Xee.logShowPaywallVariant` once it is on screen: see [Log paywall views](/docs/android-log-paywall-views).
4. When the person taps a product, pass it to `Xee.makePurchase`: see [Make purchases](/docs/android-make-purchases).

When a test runs on the paywall, you do nothing different. Each person gets one arm and keeps it, and the values you read already carry that arm's value. See [A/B tests](/docs/android-ab-tests).
