What to do when something on Android does not work. Set `Xee.logLevel = XeeLogLevel.VERBOSE` while you look: log lines say what the SDK did and why, under the tag `Xee`.

| What you see | Why | What to do |
| --- | --- | --- |
| `Success` with `verified` false | Xee could not check the purchase | Check App settings, Android: the package name and key must be set, and the service account needs the permissions in [Set up Google Play](/docs/android-google-play-setup). New permissions can take up to a day |
| The customer is refunded after three days | The purchase was never acknowledged | Same as above. Check that **Manage orders and subscriptions** is on |
| No products on the paywall | They have no Google ID, or Google does not offer them | Add the IDs on each product's page, activate the product in Play Console, and install from a testing track. The log names each one |
| `BILLING_UNAVAILABLE` | The device has no usable Play Store | Use a device or emulator image with the Play Store, signed in |
| **Last notification** stays at None yet | The push does not reach Xee | Check the Push URL, that authentication is on, that the audience is Xee's, and that Google's account can publish to the topic |
| Access does not end after a refund | The notification did not arrive | Xee also checks for refunds once a day, so it ends within a day. Connect notifications for it to happen at once |
| Revenue is missing but access works | Xee cannot read the order | Check **View financial data** on the service account |
| A new test has no Android numbers | The paywall's products have no Google ID, or no view was logged | See the warning on the paywall's page, and call `logShowPaywallVariant` |
| `Xee.track` sends nothing | The name or keys are not snake_case, or the person opted out | The log says which |
| Access bought on iOS does not show on Android | Two different profiles | Call `identify` with the same customer ID on both |
