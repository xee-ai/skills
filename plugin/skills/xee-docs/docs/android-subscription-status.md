To decide whether to show paid content or a paywall, check the person's access level in their Xee profile. You do not check product IDs in your code: every product in Xee unlocks the `premium` access level.

## Before you start

A profile learns about a Google Play subscription in two ways:

- **From the SDK.** When the app reports a purchase or a restore, Xee asks Google about it with your service account, so `premium` is on right after the purchase.
- **From Google's notifications.** Google sends real-time developer notifications to Xee through Pub/Sub for what happens later, while the app may be closed: renewals, expiry, cancellation, grace period, account hold, and refunds. Without them, a renewal or a cancellation reaches the profile only when the SDK next reports.

Both are set up in [Set up Google Play](/docs/android-google-play-setup).

## Get the profile

`Xee.getProfile()` asks the server. With no connection, it gives the profile saved on the device, so it works offline once the person has opened the app with a connection.

```kotlin snippet="android-status"
val profile: XeeProfile = Xee.getProfile()
val premium = profile.accessLevels["premium"]?.isActive == true
```

`accessLevels` is keyed by access level ID. Xee has one, `premium`, and a profile with no access has no entry for it, so treat a missing entry as no access.

## What an access level tells you

`profile.accessLevels["premium"]` is a `XeeProfile.AccessLevel`:

| Property | Meaning |
| --- | --- |
| `isActive` | Whether the person has access now. Check this one to unlock content. |
| `vendorProductId` | The store product that gave the access. |
| `store` | `play_store` or `app_store`: where it was bought. |
| `isLifetime` | A one-time purchase that never ends. |
| `expiresAt` | When access ends, for a subscription. |
| `willRenew` | Whether the subscription will renew. `false` once the person cancels in the Play Store. |
| `isInGracePeriod` | Google is retrying a failed payment and access stays on. |
| `billingIssueDetectedAt` | Set when Google reported a payment problem. |
| `isRefund` | Whether the purchase was refunded or revoked. |

A person who cancels keeps `isActive` true until `expiresAt`. A subscription on account hold has `isActive` false until the payment goes through. The profile also has `subscriptions`, keyed by product ID, with the same detail per product plus `isSandbox`.

`premium` is on if the person has access from either store. A person signed in with the same customer user ID on iOS and Android has it on both, and a person subscribed on both stores keeps access until the later one ends.

## Listen for profile updates

To hear about changes as they happen, set a listener. The SDK calls it on the main thread each time it loads a profile that differs from the one it had: after a purchase, a restore, `identify`, `logout` or `getProfile`.

```kotlin snippet="android-listener"
Xee.listener = XeeListener { profile ->
    if (profile.accessLevels["premium"]?.isActive == true) unlockPremium()
}
```

Set it once, for example in `Application.onCreate` after `Xee.activate`.

## Decide what to show

Check access where the decision is made: at launch, when the person opens a paid section, or before you show paid content. If there is no access, show your paywall; see [Get a paywall and its values](/docs/android-get-paywall).

```kotlin snippet="android-has-premium"
suspend fun hasPremium(): Boolean = try {
    Xee.getProfile().accessLevels["premium"]?.isActive == true
} catch (e: XeeError) {
    false
}

if (!hasPremium()) showPaywall()
```

`getProfile` throws only when there is neither a connection nor a saved profile, such as offline on the very first launch.

## Next

- [Identify users](/docs/android-identify-users) so access follows the person to other devices.
- [Test purchases](/docs/android-sandbox-testing) to see a profile change end to end.
