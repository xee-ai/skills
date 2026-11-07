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


The second `track` carries `duration_ms`. Use `Xee.optOutTracking()` when a person declines: it stops `track` and drops what is still queued. `Xee.optInTracking()` starts it again. See [Track events](/docs/track-events) for what the dashboard does with them.
