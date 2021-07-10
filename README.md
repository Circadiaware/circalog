# Circalog

<img src="docs/circalog-logo.svg" title="Circalog-logo" alt="" data-align="center">

(WIP) An easy cross-platform logger for daily circadian/periodic events. Stop guessing, start understanding.

This app is a generalization of the concept of sleep diary, but instead of recording just sleep periods, it can record any type of event at the simple tap of a button. Events can either be an instant period (eg, drug intake time), or a duration event (eg, sleep period, with a start time and end time, which requires 2 taps). It's also possible to add tags and comments to add additional informations. Databases can be exported as simple csv files, which exports not only the events log but also the configuration (eventtypes).

Aims for cross-platform support for computers (Windows, Linux, MacOS) and mobile (Android, iOS such as iPhone). Since the app will support automatic merge via unique identifiers of events and eventtypes, it's possible to use the app on multiple devices (eg, Android and Windows) and synchronize the database back and forth by exporting and importing in the app on each device.

This app is intended for both end-users (patients) and clinicians and research investigators. Indeed, the app can be preconfigured with the event types the investigator would like to monitor. Then, even if there is no logged events, the database can be exported. The investigator can then provide a link to this database through a link online (eg, Dropbox) for the study's participants or patients to import it and preconfigure their app. There's no need to worry if the user already started using the app before, as there is an auto-merge feature which will merge the imported events and eventtypes in while preserving the old ones (each event and eventtypes get a unique identifier - which is meaningless in itself but allows to merge databases).

The app will be developed with react-native and typescript. Database will likely be stored as SQLite to be cross-platforms compatible.

Circalog sleep diary format specification, intended to be generic to store a maximum of pertinent data for any kind of circadian periodic event, is readable here: https://github.com/Circadiaware/circalog/blob/main/docs/circalog-format-spec.md
