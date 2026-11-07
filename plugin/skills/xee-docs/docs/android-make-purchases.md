Sell a product from your paywall and unlock access. Xee never takes the app's word for a purchase: it asks Google about it, and only then turns access on.

## How access turns on

1. Your app calls `Xee.makePurchase` with a product from `Xee.getPaywallVariantProducts`. Google's sheet opens.
2. When the person pays, the SDK sends the purchase token to Xee.
3. Xee asks Google about the token with your service account, records the subscription on the person's profile and acknowledges the purchase.
4. The SDK gets the profile back with `premium` on.

Google refunds a purchase that is not acknowledged within three days. Xee's server acknowledges it, so a person who never opens the app again keeps what they paid for. This needs the service account from [Set up Google Play](/docs/android-google-play-setup).

## Make a purchase

```kotlin snippet="android-make-purchase"
try {
    when (val result = Xee.makePurchase(activity, product)) {
        is XeePurchaseResult.Success -> {
            if (result.profile.accessLevels["premium"]?.isActive == true) unlockPremium()
        }
        XeePurchaseResult.Pending -> Unit // Waiting on payment outside the app. Access comes when it is paid.
        XeePurchaseResult.UserCancelled -> Unit // The person closed Google's sheet. Leave the paywall open.
    }
} catch (e: XeeError) {
    showError(e)
}
```

`makePurchase` needs the `Activity` on screen, because Google's sheet opens over it.

| Result | What it means | What to do |
| --- | --- | --- |
| `Success`, `verified` true | Xee checked the purchase with Google. | Read access from `profile`. |
| `Success`, `verified` false | The person paid, and Xee could not check it yet: the Google Play setup is unfinished, or Google could not be reached. | Say that access may take a moment. Do not charge again: the SDK reports the purchase again at each launch. |
| `Pending` | Payment happens outside the app, such as cash at a shop. | Leave access off. It turns on when Google says the purchase is paid. |
| `UserCancelled` | The person closed the sheet. | Leave the paywall open. |

An `XeeError` says what went wrong in its message. Its `code` is for your code: `BILLING_UNAVAILABLE` (no usable Play Store on the device), `PRODUCT_NOT_AVAILABLE`, `ALREADY_OWNED`, `NETWORK` and the others in [Android troubleshooting](/docs/android-troubleshooting).

### Buy from the paywall, not from a product ID

Always buy a product you got from `getPaywallVariantProducts`. It carries the paywall's variation, so the purchase counts for the right arm of an [A/B test](/docs/android-ab-tests), and it carries the offer Google gives this person.

### Offers

A Google base plan can have offers, such as a free trial. The SDK sells the first offer the person qualifies for, and `freeTrialPeriod` on the product says when it starts with a free trial. Google decides who qualifies: a person who had a trial before gets the base plan.

## Upgrade or downgrade

Pass what the new product replaces. Xee keeps both on one chain, so access does not stop between them:

```kotlin snippet="android-replace"
Xee.makePurchase(activity, yearly, XeeReplacement("premium_monthly", XeeReplacement.Mode.CHARGE_PRORATED_PRICE))
```

The modes are Google's replacement modes: `WITH_TIME_PRORATION`, `CHARGE_PRORATED_PRICE`, `CHARGE_FULL_PRICE`, `WITHOUT_PRORATION` and `DEFERRED`.

## Restore purchases

Offer a restore button. Google expects a way to get purchases back on a new install.

```kotlin snippet="android-restore"
val profile = Xee.restorePurchases()
if (profile.accessLevels["premium"]?.isActive == true) unlockPremium()
```

`restorePurchases` reports everything on the person's Google account. A purchase never moves away from a profile that already owns it. See [Identify users](/docs/android-identify-users) for which profile gets a purchase.

## Promo codes

Google has no in-app sheet for promo codes. This opens Google's page to redeem one:

```kotlin snippet="android-promo-code"
Xee.presentCodeRedemption(activity)
```

## If your app has its own purchase code

Keep it, and tell Xee about each purchase. Xee checks it with Google the same way.

```kotlin snippet="android-report-purchase"
Xee.reportPurchase(purchaseToken = token, productId = "premium_play", isSubscription = true)
```

Pass `variationId` too, from the paywall the person bought on, so the purchase counts for the right arm of a test.

## Next

- [Check subscription status](/docs/android-subscription-status) to unlock content.
- [Test purchases](/docs/android-sandbox-testing) with a license tester before you release.
