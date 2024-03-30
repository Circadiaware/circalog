# TODO

Additional implementation details to complement the specification:

* circalog add a mode to track phone usage and auto input events, this way it can make an actogram out of smartphone use and infer indirectly sleep. Can be enabled for an event type, eg, Wakefulness. Smartphone usage is likely a very good proxy estimator of the wakefulness period since we use smartphones so much all the time!
* Add vibration feedback when tapping widgets to track or untrack sleep https://www.youtube.com/watch?v=rAVV4kAYlI8
* App needs to implement a homescreen widget to tap, like plees-tracker (open-source) and sleepmeter (closed-source) do.
* App also needs to implement tapping widgets on the lockscreen, and hide time if possible, or being tappable (for the sleep diary) by pressing physical buttons, to allow to run sleep studies without having the user look at the time at night when they are trying to sleep (a major recommendations by sleep clinicians - although not necessarily backed by evidence, but without a tool to test, we cannot know if this makes a difference or not - currently, paper sleep diaries are used, and they can't be used at night due to this constraint, so clinical sleep diaries do not represent faithfully sleep fragmentation nor even sleep onset and offset times since the user is instructed not to look at the time!).
* Deno modern typescript engine, can cross-compile for Windows, Linux and MacOS: https://docs.deno.com/runtime/manual/tools/compiler
* get inspiration from UI of https://github.com/SamAmco/track-and-graph great tracking and grouping of events types, and great duration charts and correlations, and previously entered tags are suggested
  * but no 24h chart so cannot correlate start times and end times, and no explicit start time, only based on duration and end time, so very unintuitive to modify an event. also no margin on tapping. and very difficult to modify existing entries.
* SUPER IDEA: to mimic how it's easy to notice errors and modify on a paper sleep graph, when editing an event, show a previsualization of this event on the sleep graph as well as a few days earlier and later (if available) for context, so it will be much more easy to spot if the event looks alright, and fix mistakes.
* SUPER IDEA: also do it like calendars: allow to edit directly on sleep graph, to create a new event on a 1h block where the user taps, or if holding on an already existing event, it will open in edit mode. No need to be able to resize as on a computer, but at least to.open the event in edit mode from the sleep graph will make it much easier to modify the sleep graph aposteriori.

* [ ] tapping interface ala track and graph, with recording but bigger button or in fact default when tappinp big square, then a small button to see list, and another to add. So we can do everything from tapping interface.
    * [ ] Quick short input dialog.
    * [ ] tapping interface should be a dashboard including graphs, to see everything at a glance. But may bias users, we want a sober input interface for sleep medicine because of cognitive hyperstimulation hypothesis.
    * [ ] editing events on graph: tap a bar on graph and it opens the relevant event. Allows to edit semi-visually, mimicking a paper sleep graph.

* [ ] at first startup, ask user to estimate the average time they take to fall asleep once they really try or taf the app (they can change later but it's important for accurate estimation of sleep duration and fragmentation). And same for wake up (usually it's the same time).
* [ ] flutterflow?
* [ ] inspiration ui for questions maybe? https://gitlab.com/zerodogg/org.zerodogg.migraineLog
