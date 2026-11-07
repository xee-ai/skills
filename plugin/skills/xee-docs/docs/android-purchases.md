Sell a product and check who has access. Xee never takes the app's word for a purchase: it asks Google, and only then gives access.

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


`Success` with `verified` false means the person paid and Xee could not check it yet: the Google Play setup is unfinished, or Google could not be reached. The SDK tries again at each launch. Say that access may take a moment, and do not charge again.

A purchase that is never checked is refunded by Google after three days. The customer does not lose money for nothing, but check [the setup](/docs/android-google-play-setup) if you see this.

## Upgrade or downgrade

Pass what the new product replaces:

```kotlin snippet="android-replace"
Xee.makePurchase(activity, yearly, XeeReplacement("premium_monthly", XeeReplacement.Mode.CHARGE_PRORATED_PRICE))
```


## Restore

Offer a restore button. Google requires a way to get purchases back on a new install.

```kotlin snippet="android-restore"
val profile = Xee.restorePurchases()
if (profile.accessLevels["premium"]?.isActive == true) unlockPremium()
```


`restorePurchases` reports everything on the person's Google account. A purchase never moves away from a profile that already owns it.

## Check access

```kotlin snippet="android-status"
val profile: XeeProfile = Xee.getProfile()
val premium = profile.accessLevels["premium"]?.isActive == true
```


With no connection, `getProfile` gives the saved profile. To hear about changes as they happen:

```kotlin snippet="android-listener"
Xee.listener = XeeListener { profile ->
    if (profile.accessLevels["premium"]?.isActive == true) unlockPremium()
}
```


`premium` is on if the person has access from either store, so a person signed in with the same customer ID on iOS and Android has it on both. A person subscribed on both stores keeps access until the later one ends, and the profile page in Xee shows both.

## If your app has its own purchase code

Keep it, and tell Xee about each purchase. Xee checks it with Google the same way.

```kotlin snippet="android-report-purchase"
Xee.reportPurchase(purchaseToken = token, productId = "premium_play", isSubscription = true)
```
