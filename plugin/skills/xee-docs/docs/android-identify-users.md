Xee keeps a profile for every person using your app. If your app has accounts, give Xee your own user ID, so the person's access follows them to any device they sign in on, Android or iOS.

## Profiles and customer user IDs

- The SDK creates a profile on activation. You never create one yourself.
- A profile is **anonymous** until it has a **customer user ID**: your own ID for the person, from your login system.
- One customer user ID belongs to one profile in an app, on both stores. Xee never moves an ID from one profile to another.

|  | Anonymous | Identified |
| --- | --- | --- |
| Profile | A new one on each install | One per customer user ID |
| Purchases | Belong to this install's profile | Belong to the profile that follows the customer user ID |
| Other devices | No access there | Access on every device signed in with the ID, iOS included |

## How Xee links a purchase to a profile

Every profile has an account token, a UUID the SDK keeps on the device. The SDK passes it to Google with each purchase as `obfuscatedAccountId`, and Google repeats it in everything it says about the purchase. Xee then picks the first of these that applies:

1. **The profile whose token the purchase carries.**
2. **The profile the subscription already belongs to.** A later notification keeps it where it is.
3. **The profile that reported it**, with `makePurchase`, `restorePurchases` or `reportPurchase`.

A subscription never moves away from a profile that owns it. A purchase made without Xee carries no token: it attaches to the profile that restores it.

## Identify at activation

If the person is signed in when the app starts, pass their ID to `activate`. The SDK then creates the profile with that ID, without an anonymous profile first.

```kotlin snippet="android-activate-config"
val config = XeeConfig.Builder("YOUR_SDK_KEY")
    .customerUserId("customer-1")
    .build()
Xee.activate(context, config)
```

`customer-1` stands for the value from your own account system. Never hardcode it: every install would share one profile.

## Identify after login, and log out

When the person signs in or signs up after the app has started, call `identify`. When they sign out, call `logout`:

```kotlin snippet="android-identify"
Xee.identify("customer-1")
// ... and when the person signs out:
Xee.logout()
```

What `identify` does depends on the ID:

- **An ID Xee has not seen:** it is attached to the current anonymous profile, which becomes the customer's profile.
- **An ID that already belongs to another profile:** the device switches to that profile, the one the customer has on their other devices. Anything the anonymous profile bought stays on it, and is not merged.

> [!NOTE]
> Purchases made while the person was anonymous are not moved when they sign in as an existing customer. If a purchase should count for the account, have the person sign in before they buy.

Wait for `identify` to return before other Xee calls: it returns the profile the device is now on. `logout` starts a new anonymous profile on this device, and returns it.

## Add facts about the person

```kotlin snippet="android-update-profile"
Xee.updateProfile(XeeProfileParameters(email = "a@example.com"))
```

`XeeProfileParameters` also takes `phoneNumber`, `firstName`, `lastName`, `gender` and `birthday`. Each one you leave out stays as it is.

## People on several devices

| Your setup | What happens |
| --- | --- |
| Customer user ID passed at activation, on a fresh install | The device gets the person's existing profile, or a new one with the ID. |
| `identify` after login, with an ID Xee has not seen | The anonymous profile becomes the customer's profile. |
| `identify` after login, with an ID that already has a profile | The device switches to that profile. |
| The same ID on iOS and Android | One profile: access bought on either store unlocks both. |
| No login at all | Profiles stay anonymous. Access does not follow the person to another device; Restore brings it back on the same Google account. |

Keep a **Restore** button on your paywall anyway. See [Make purchases](/docs/android-make-purchases).
