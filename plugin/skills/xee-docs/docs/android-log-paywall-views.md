Xee learns about purchases on its own, but not about views: only your code knows when the paywall is actually on screen. Call `Xee.logShowPaywallVariant` each time it appears.

## Why it matters

[A/B test](/docs/android-ab-tests) results are built on views. For each arm, **Viewers** is the number of distinct people who logged a view of that arm, and **Conversion** and **Revenue per 1K viewers** are divided by it. If the app never logs views, every arm has zero viewers, and the test cannot tell you which arm is ahead.

## Log a view

Pass the `XeePaywallVariant` you got from `Xee.getPaywallVariant` once the paywall is visible. In Compose:

```kotlin snippet="android-log-view-compose"
@Composable
fun PaywallScreen(variant: XeePaywallVariant, products: List<XeePaywallVariantProduct>) {
    LaunchedEffect(variant.variationId) {
        Xee.logShowPaywallVariant(variant)
    }
    PaywallContent(variant.config, products)
}
```

With views and fragments, call it in `onResume` of the screen that shows the paywall, or `onViewCreated` of its fragment.

`logShowPaywallVariant` returns at once and never throws. The view is saved on the device and sent in the background with the SDK's other events, so it can take a few seconds to show in the dashboard. Before `Xee.activate`, it logs an error and sends nothing.

## When to call it

- **Each time the paywall appears.** If the person closes it and it opens again later, log again. Viewers counts distinct people, so repeat views do not inflate a test.
- **After it is visible, not when you fetch it.** Fetching a paywall ahead of time is not a view.
- **Only when it really showed.** If loading failed and you showed nothing, or the person already has access and you skipped the paywall, do not log.

## Pass the same paywall you showed

A view counts for the variation on the object you pass, through its `variationId`, such as `main.v4.B` for arm B of version 4. So keep the object you fetched and hand the same one to your screen, to `logShowPaywallVariant` and to `getPaywallVariantProducts`. The products then carry the same variation, and purchases count for the same arm as the view.

Do not fetch the paywall a second time just to log it: after a test starts, a second fetch can return a different variation than the one on screen.

## Check that views arrive

1. Start a test on the paywall in the dashboard.
2. Open the paywall in a build made for the latest version, such as one from the internal testing track.
3. Open the test under **A/B tests**. After a few seconds, **Viewers** for your arm goes up by one.

If Viewers stays at zero, set `Xee.logLevel = XeeLogLevel.VERBOSE` and see [Android troubleshooting](/docs/android-troubleshooting).
