Buy each product with a Google Play license tester before you release, and check that the purchase reaches Xee. License testers pay nothing, and their subscriptions renew in minutes.

> [!IMPORTANT]
> Xee's Android support is a preview. It has been tested against stand-ins built from Google's documents, not yet against Google itself. Run every check below before you sell to real customers, and tell Xee if one fails.

## 1. Prepare

1. Finish [Set up Google Play](/docs/android-google-play-setup).
2. In Play Console, under Settings, License testing, add your Google account as a tester, and set the response to **RESPOND_NORMALLY**.
3. Upload a signed build to the **Internal testing** track, with the same package name. Google offers products only to a package it knows.
4. Open the track's opt-in link with the tester's account.

## 2. Install and buy

Use a real device, or an emulator image with the Play Store, signed in as the tester. Install from the testing track, or with a debug build signed with the same key.

Open your paywall and buy. Google's sheet says it is a test.

## 3. The checks

| # | Do this | What should happen |
| --- | --- | --- |
| 1 | Send a test notification from Play Console | **Last notification** changes in App settings, Android |
| 2 | Open the paywall | Your Google price shows, and the title comes from Xee |
| 3 | Buy a subscription | `Success` with `verified` true, and `premium` on at once |
| 4 | Open the profile in Xee | The subscription shows with store `play_store`, marked sandbox |
| 5 | Wait three days, or check the order in Play Console | The purchase is acknowledged and not refunded |
| 6 | Check the revenue in Xee's analytics | The price you paid shows once, not twice |
| 7 | Wait for a renewal (minutes for a tester) | A new order appears, and access continues |
| 8 | Upgrade to another product with `XeeReplacement` | Access continues on one chain, not two |
| 9 | Cancel in the Play Store | `willRenew` turns off, and access lasts until the end date |
| 10 | Refund and revoke the order in Play Console | Access goes, and a refund shows in Xee |
| 11 | Buy a one-time product, if you sell one | `premium` on, and `isLifetime` true |
| 12 | Reinstall, then press Restore | `premium` comes back on the same profile when signed in |
| 13 | Buy with a test card that declines, or the slow card | `Pending`, with no access until it is paid |

## If something is wrong

See [Android troubleshooting](/docs/android-troubleshooting). With Claude connected, `get_doc` reads this page and it can check the setup with you.
