# TODO

Additional implementation details to complement the specification:

* circalog add a mode to track phone usage and auto input events, this way it can make an actogram out of smartphone use and infer indirectly sleep. Can be enabled for an event type, eg, Wakefulness. Smartphone usage is likely a very good proxy estimator of the wakefulness period since we use smartphones so much all the time!
* Add vibration feedback when tapping widgets to track or untrack sleep https://www.youtube.com/watch?v=rAVV4kAYlI8
* App needs to implement a homescreen widget to tap, like plees-tracker (open-source) and sleepmeter (closed-source) do.
* App also needs to implement tapping widgets on the lockscreen, and hide time if possible, or being tappable (for the sleep diary) by pressing physical buttons, to allow to run sleep studies without having the user look at the time at night when they are trying to sleep (a major recommendations by sleep clinicians - although not necessarily backed by evidence, but without a tool to test, we cannot know if this makes a difference or not - currently, paper sleep diaries are used, and they can't be used at night due to this constraint, so clinical sleep diaries do not represent faithfully sleep fragmentation nor even sleep onset and offset times since the user is instructed not to look at the time!).
