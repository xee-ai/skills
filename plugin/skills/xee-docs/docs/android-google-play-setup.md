Connect your Google Play app to Xee, so Xee can check each purchase with Google and hear about renewals, cancellations and refunds. It takes about half an hour, and Google can take up to a day to apply new permissions.

Everything here also shows in Xee, in App settings, Android, which says what is still missing.

## 1. Add the package name

In Xee, open App settings, Android and enter your **Package name** exactly as in Play Console, such as `com.example.app`. Xee ignores notifications for any other package.

## 2. Create the service account

1. In [Google Cloud Console](https://console.cloud.google.com/), choose or create a project.
2. Enable the **Google Play Android Developer API** and the **Cloud Pub/Sub API**.
3. Under IAM & Admin, Service Accounts, create a service account. It needs no project role.
4. Open it, then Keys, Add key, JSON. Upload that file in Xee, under **Service account key**. Xee stores it and never shows it again.

## 3. Give it access in Play Console

In Play Console, open Users and permissions, Invite new users, and enter the service account's email. Give it these permissions for your app:

- **View app information and download bulk reports**
- **View financial data, orders and cancellation survey responses**: Xee reads the price paid and refunds with it
- **Manage orders and subscriptions**: Xee finishes each purchase with it

> [!IMPORTANT]
> Google refunds any purchase that is not acknowledged within three days. Xee acknowledges a purchase once it has recorded it, so without this permission your customers get refunded.

## 4. Connect notifications

1. In Google Cloud, under Pub/Sub, create a topic, such as `xee-play`.
2. On the topic's permissions, add `google-play-developer-notifications@system.gserviceaccount.com` with the role **Pub/Sub Publisher**.
3. Create a subscription on the topic. Choose **Push**, and paste Xee's **Push URL** as the endpoint.
4. Turn on **Enable authentication**, choose a service account, and paste Xee's **Audience** in the audience field. Xee refuses a push without it.
5. In Play Console, open Monetize with Play, Monetization setup. Under Real-time developer notifications, turn them on and paste the topic's full name, `projects/<project>/topics/xee-play`.
6. Choose to get notifications for **subscriptions, all one-time products and voided purchases**.
7. Press **Send test notification**. In Xee, **Last notification** should change within a minute.

## 5. Add your products

1. In Play Console, create each product: under Monetize with Play, Products, Subscriptions with a base plan, or One-time products. Activate them.
2. In Xee, open Products and the product. Under **Google Play**, enter the **Google product ID** and, for a subscription, the **Base plan ID**.

A product in Xee is one thing with a name on each store, so the same paywall and test can sell on both. An Android device only shows products that have a Google ID, and Xee warns you on the product list and on each paywall about any that lack one.

> [!NOTE]
> Xee does not import products from Play Console yet. Enter the IDs by hand.

Next: [Test Android purchases](/docs/android-sandbox-testing).
