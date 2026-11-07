Send your own events to Xee for funnels and view tests. `track` never throws and never blocks. Events wait on the device until the server has them, so one outlives a closed app or a lost connection.

```kotlin snippet="android-track"
Xee.track("onboarding_started")
Xee.track("onboarding_step_viewed", mapOf("screen" to "goals", "step" to 2))
```


Names and property keys are lowercase snake_case: event names up to 64 characters, keys up to 40. A property is text, a number or a switch, and an event carries up to 20 of them. Anything else is left out, with a log line.

To measure how long something took:

```kotlin snippet="android-track-time"
Xee.time("onboarding_finished")
// ... the person goes through the screens ...
Xee.track("onboarding_finished")
```


The second `track` carries `duration_ms`, the milliseconds since `time`. Timers live in memory: a cold start forgets them.

## Let a person opt out

```kotlin snippet="android-track-opt-out"
if (declined) Xee.optOutTracking() else Xee.optInTracking()
```

`optOutTracking` stops `track` on this device and drops what is still queued. The choice is remembered across launches. Paywall views and purchases are not affected: tests and access need them.

## What Xee does with them

A test on a [view](/docs/android-views) is judged by one event, a view's page shows a funnel of them, and each person's profile in the dashboard lists them. Name events for what happened, `<flow>_<what happened>`, and keep a name once it ships: funnels and goals find events by name.
