A view is a screen or a flow in your app that sells nothing, such as onboarding, a welcome tour or a permission prompt. Xee serves its values the way it serves a paywall's, so you can change them without a release and test them. A test on a view is judged by an event your app sends, such as `onboarding_finished`, not by purchases.

## Read a view

```kotlin snippet="android-view"
val view = Xee.getView("onboarding")
render(view.config.string("title", "Welcome"))
Xee.logShowView(view)
```

`Xee.getView` never throws. When the view cannot load in time (no network at first launch, no version pushed yet), it returns a view whose values are all your code's defaults, so your screens always show. `loadTimeoutMs` defaults to 2,000 for views, because onboarding often runs at first launch.

A whole flow in Compose:

```kotlin snippet="android-view-onboarding"
@Composable
fun OnboardingFlow() {
    var onboarding by remember { mutableStateOf<XeeView?>(null) }
    LaunchedEffect(Unit) { onboarding = Xee.getView("onboarding") }

    val view = onboarding
    if (view == null) {
        CircularProgressIndicator()
        return
    }
    LaunchedEffect(view) {
        Xee.logShowView(view)
        Xee.time("onboarding_finished")
        Xee.track("onboarding_started")
    }
    WelcomeScreen(view.config)
}

@Composable
fun WelcomeScreen(config: XeePaywallConfig) {
    Column {
        Text(config.string("welcome_title", stringResource(R.string.welcome_title)))
        if (config.bool("show_skip", true)) {
            TextButton(onClick = { Xee.track("onboarding_skipped") }) { Text("Skip") }
        }
    }
}
```

Read each value with the default your code would use without Xee: `string`, `int`, `double`, `bool` and `stringList`, the same getters as a paywall's. A list of screen IDs lets you test the order or the number of screens:

```kotlin snippet="android-view-screen-order"
val order = view.config.stringList("screen_order", listOf("welcome", "goals", "notifications"))
```

## Log the view

Call `Xee.logShowView` once, when the first screen appears. A test counts a person from that moment: without it, every arm has no viewers. For a view that could not load it does nothing, and that person is not part of a test.

## Events

The view's code sends events with `Xee.track`, for example when each screen appears and when the flow ends. See [Track events](/docs/android-track-events). Each version of a view declares the events its code sends, in order: in the dashboard they make the view's funnel, and a test on the view is judged by one of them, its **goal**.

## Versions

Like a paywall, a view has versions, each pushed for an app build. The SDK sends your app's `versionCode`, and Xee serves the newest version pushed for that build or the closest older one. The latest version gets the running test. A view and a paywall cannot share an ID, and the same view serves your iOS app.
