Xee shows each paywall's and view's text in the language the device runs in. Languages and translations belong to the Xee app, so the ones you set up for iOS serve Android too.

## Choose languages

Open **App settings** and find **App Languages**:

- **Source language**: the language your values are written in, English unless you change it.
- **Offered in**: the languages your users can get. Choose **Add languages**, tick them and save.

## Which language a person gets

The SDK sends the device's language, such as `fr-CA`. Xee answers with the closest offered language:

| The device runs in | You offer | The person gets |
| --- | --- | --- |
| French (Canada) | French, French (Canada) | French (Canada) |
| French (Canada) | French | French |
| Spanish (Mexico) | Spanish, Spanish (Latin America) | Spanish (Latin America) |
| German | French | Your source language |

A text with no translation shows in your source language. Numbers and on/off values are the same in every language. Pass `locale` to `getPaywallVariant` or `getView` only to show another language than the device's.

## Translate

On a paywall's or view's page, a tab per language shows the preview in that language. Each text has a field for its translation; save it and people get it at once. A translation belongs to the words, so one translation of "Start free trial" serves every paywall that says it.

A test needs B in every language A is translated in, and cannot start without it.

## In your app

Read text with a default from your string resources, so a person who opens the app offline, before the paywall loads, still sees their language:

```kotlin snippet="android-localized-default"
Text(config.string("headline", stringResource(R.string.unlock_everything)))
```

The fallback file you download from **Products › Fallbacks** carries every offered language. Download it again after you add translations.
