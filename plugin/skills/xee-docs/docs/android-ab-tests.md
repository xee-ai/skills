An A/B test shows different values of one paywall variable to different people and counts who buys. You start and end tests in the dashboard; your app needs no extra code beyond reading the paywall and logging views. A test runs on both stores at once.

## What a test is

A test runs on one variable of a paywall's latest version, such as its headline or button text. It has two arms:

| Arm | Value | Traffic |
| --- | --- | --- |
| A | The value served today. | The rest |
| B | The new value you enter. | 5 to 95%, 50% by default |

Only one test runs on a paywall at a time. To start one, open **Paywalls**, choose the paywall, choose **Start test** next to a value, fill in B and the traffic, choose **Create draft**, then **Start test** on the draft.

## How people are split

The server sends the SDK one variation per arm, with its weight, and the device picks one. The pick is a hash of the test's ID and the profile ID, so the same profile lands in the same arm on every device, Android and iOS. A person keeps their arm for the whole test.

Your app reads the drawn values the same way with or without a test:

```kotlin snippet="android-ab-test-read"
val variant = Xee.getPaywallVariant("main")
// variant.abTestName is the test's name, or "default" when none is running.
// variant.variationId is "main.v4.B" for arm B on version 4, "main.v4" with no test.
val products = Xee.getPaywallVariantProducts(variant)
Xee.logShowPaywallVariant(variant)
```

> [!NOTE]
> Only builds made for the latest version take part. A build made for an older version gets that version's values, with no test.

## What counts

- **A view** is a call to `logShowPaywallVariant` for an arm's variation. Without it a test has no numbers: see [Log paywall views](/docs/android-log-paywall-views).
- **A purchase** counts for an arm when it is made with a product from that arm's `getPaywallVariantProducts`. If you buy with your own Billing code, pass `variationId` to `Xee.reportPurchase`.

## Tests on two stores

Results combine both stores. Prices can differ between stores, so read the revenue of a test that sells on both with that in mind. A paywall whose products have no Google ID sells nothing on Android: the paywall's page warns about those products.

## What the results show

For each arm: **Viewers**, **Purchasers**, **Conversion** (purchasers divided by viewers), **Revenue** in USD and **Revenue per 1K viewers**, plus the **Probability to be best**, from a Bayesian model of each arm's conversion. Only views and purchases inside the test's window count.

## End a test

- **Ship A** or **Ship B** ends the test and makes that arm's value the one everyone on the latest version gets.
- **Stop test** ends it and keeps the current value.

## Tests on a view

A [view](/docs/android-views) has no products, so a test on one is judged by a **goal**: one of the events the view declares, sent with `Xee.track`. The results show who saw the view, who reached the goal, the rate and the probability to be best.
