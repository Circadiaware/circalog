TASKS TODO
----------

* 2 types of event: single or double (start and end), or instant and duration
* gui:
	1. toggle buttons, events listed there can be configured in settings (just a checkbox). When pressed, show a temporary popup to confirm, and change text if it's a duration event. Save in database even if duration event, will complete later (keep a registry of undergoing duration events to finish? Yes it's the fastest way and it will avoid errors). Also can cancel if tap aqain within 20 min (configurable in options). We register directly, but before registering a new event (or finishing a duration event), we go back and check whether the last previous same eventtype was under the timeframe.

single events can overlap with duration events. multiple duration events too, don't care.

2. list of buttons of all possible events, ordered by most frequent use in last 30 days (configurable in options) then by alphabetical order (configurable in option, can disable ordering by frequent use and keep only alphabetical). When one event is selected, all other events disappear. Can deselect and all others reappear.
3. Select start date. If duration event, can select end date, and also can add holes, as many as we want. This will ease things. Use default hole time of 15min in the middle.
4. Other options:
	* tags (simple buttons showing all tags to select as checkboxes)
	* link with previous event
	* holes (+ with toggle buttons / quick log, if under a defined period eg 1h, auto create holes by moving the end date to start of hole and current time to end of hole)
	* whole day milestone events, just a checkbox to check. If single event, add inversed flag at bottom of graph. Else if duration, add a colored horizontal line covering the periods (multiple lines can align one below the other if multiple duration milestone events). These events cover the whole day in any case. It's to mark phases of experiments. 
5. free note taking / comments field


* Circalog separate in 2 different tabs the shortcuts and record form, but add option to select which screen to show on startup. Then Stats and Graphs tabs, just like in Sleepmeter.
* List of records
	* group by date
	* Option to filter records by sql column ( allow to select one field and one comparison operator and a value) 
	* color duration with predefined optimal durations? Would need additional fields in eventtype
* database: allow imports from Sleepmeter and merge from other instances of Pevlog between devices (eg, import log of computer to phone and vice versa):
	* generate ids in all tables by sha1 hash of a random string, just like how commits numbers are generated in git? The id will stay even if the entry is modified.
	* add 2 columns for all tables: datetime_inserted and datetime_modified, same value for both on insertion, then only modified is changed on modifications.
	* to merge:
		* first insert all rows where the id does not exist in db but is in the log.
		* then when ids collide, update only if datetime_modified is more recent.
	* to import from sleepmeter, reuse same merge/import function but first preprocess sleepmeter log to convert to pevlog log format
	* button in options to reset the events database (but keep eventtypes) or reset everything including eventtypes (simply sql truncate and reimport default sql database, keep a copy in repo with default eventtypes)
	* one table for last tapped event, one unique row per eventtype. When tapping on homescreen, first check if there is a last record for this eventtype in this db, if yes check if the last tap time is below the cancel time, if not then check the event type to know if it's a duration event, if yes then end the duration event by saving a new event record (and update this record too). All the other cases we create the last tap record or update it to current time and that's all.
		* When modifying an eventtype configuration (in eventtype table), then delete last tap record for this eventtype (because the type of event may have changed so we need to delete to avoid mishaps).
		* Having a specific table for last tap event allows to differenciate events created manually by users from tap created events, and it's faster to access too.
* stats: select main factor (can be sleep for eg), then all stats computed accordingly:
	* if main factor is single event, simply compute average time it phase advances/delay the main factor + average and stdev between this event and the main factor + diff timeseries of main factor between previous day, how much it shifts + compute the same vs without the current factor to see the real difference/interaction
	* if main factor is a duration event: compute as before but for both start and end times + compute average duration and stdev with and without
	* add a no factor stat, with the same as above but without the influence of another factor, just like sleepmeter
	* check what stats sleepmeter does. I'm pretty sure all it does is a simple SQL squery that groupby and averages. Here we can do the same and better with metrics specifically for DSPD and non24 (so using the difference between events rather than absolute timing, will be more informative for phase shifts)
	* if linked events, compute stats for all events (start will be start of first event, wake up will be of the last event, duration will be the sum of events durations)
	* Difference of variability in bedtime and wake up time with previous day, compare against mean stdev or mad between each 2 days. Because some factors such as digestive issues or exceptional circumstances don't shift the circadian rhythm per se but can add a lot of variability. 
	* **BEST: calculate the rolling average midpoint of sleep + wake up time over a selectable window (4 days by default). Will allow to more easily calculate optimal time for light therapy and melatonin. Can even include a calculation for optimal intake, all timings definable in options, this will make things MUCH easier for patients. Can also calculate EMA in addition to SMA to more quickly adapt to variations. Can also show on graph.**
	* BEST CRITICAL: Wake up time (and even current circadian night) predictor. For the moment assume stable circadian rhythm but later can make a model accounting for timelag (progressive phase delay, and even differential phase delay depending on absolute time of day = sunlight exposure and even seasonal change in sunlight duration, with a hmm?).
		* Simple model: use latest circadian night estimation based on selecting average of 5 latest sleep sessions longer than 6h.
		* Then when go to sleep tapping button to recosd, popup window (option disactived by default) to say when will likely wake up. Rule is as follows:
		* if fall asleep time (hence bedtime minus sleep latency) at start of circadian night, will sleep until end of circadian night (about 8h).
		* if fall asleep time earlier, then will sleep 8h or max 1 ultradian cycle more if does not go beyond the end of circadian night, otherwise will sleep only 8h if not enough space for one more ultradian cycle.
		* if fall asleep time later, then will sleep up to wake up time. If at wake up time would have slept less than 5h30, then extend sleep until it lasts at least 5h. This is only if the previous wakefulness period (without long naps) lasted at least 12h.
		* include how long already slept in the last 12h. If slept 2h, then max 5h of sleep in total sum night + expected night if out of circadian night or 8h total sum if alignment.
		* Assumptions: 1) relatively stable circadian phase (entrainment) or little drift between entered dates (in last month) - could improve by also allowing date input and then calculating drift between these dates (from midpoint of sleep?) and then extrapolating to today or any date entered, 2) wake up without an alarm clock, 3) no premature wake up due to disturbances such as noise or sunlight or sleep apnea.
		* SCRAP THAT: simpler rules for **SLEEP-WAKE PATTERN PREDICTOR (circadian rhythm and sleep pressure estimator from sleep diary)**:
			* Wake time + sleep duration is best predictor of the circadian rhythm, and wakefulness duration for sleep homeostat. Do not use midpoint of sleep, it only averages the wake up time with the sleep time and there is no biological basis linking midpoint of sleep with the circadian rhythm, but there are for the wake up time.
			* filter to keep only long sleep sessions (>6h) and calculate the average delay and timing. In the future, use a probabilistic model for more accuracy, but for now simply calculate from first to last how much delay between mid-points of sleep (or better, calculate the diff between each timepoint, so that if it goes around the clock we don't miscalculate as we would with just last-first). Then we can extrapolate where is the circadian night currently given the difference in time.
				* Keep only the sessions over the last 2 months because the circadian rhythm can change beyond (especially if started an entrainment therapy). This is a changeable option.
			* In addition to circadian night, we can know the last sleep session duration and timing. With both, we can calculate when the user is more likely to fall asleep: during circadian night + at least 10h of wakefulness, with the more the better. Beyond 16h of wakefulness, red zone of max adenosine buildup (it can get in purple though if more than 20h). Circadian night is also another color, when both are combined it's the perfect time for sleep, other periods are less favorable (purple is an exception as the user will still sleep, but less well).
				* implement sleep gates? with the gradual change of color on the graph it would be even better. Make a 3 horizontal bars graph, one for adenosine sleep pressure, one for circadian rhythm, one for combinated sleep propensity.
			* And now we can also predict the wake up time with this set of rules:
				* no more than 8h of sleep (or whatever amount of sleep the user needs, editable option) per 24h if sleeping in circadian alignment.
				* no more than 5h of sleep (or optimal-sleep-duration minus 2 ultradian cycles of 1h30) per 24 if sleeping in circadian misalignment.
				* If sleep sessions smaller than 8h, eg 6h, use wake up time and extrapolate bedtime by minus optimal sleep duration. Also account for possibility of sleeping 1 ud later or earlier, hence window of 2ud in total (use a temporal derivative model?).
			* Can implement in Python at first and then integrate into Circalog
* Graph: Primary plot is a 24h by day date chart (ie, sleep graph) showing bars for start and end times of each events.
	* plot point (for single time events) and bars (for duration events) with a random different color per eventtype. Can store and make the color permanent or user modifiable in event_type.additional_data .
	* selectable legend: allow to click on legend text to hide everything but the selected eventtypes (eg, will be able to plot sleep and light therapy). Add a "Show all" button to reset and show everything.
		* Allow to click and maintain on the legend to get additional options such as changing the event's color (because the randomly selected ones may not be adequate). Also add a button to randomize all colors again.
	* Swipe to reduce timeperiod + button to plot a specific period of time (missing in sleepmeter, can't go back in time))
	* for duration events, also plot the start event and end event as points. And allow in legend to select them (ie, allow to plot only wake up time without bedtime nor sleep duration). Visualization is the most powerful and easy way to see patterns and strong correlations. Eg plot wake up time and light therapy duration. Can also do stats.
	* allow to highlight events in a specific color based on a selected tags (can allow to choose multiple tags and colors for each). Eg add a menu for the Graph tab, with an option to "Filter". Then, can either just highlight the selected tags or events (eg, by making others more transparent), or can hide all others altogether.
		* allow ellipses to show only highlighted events and a few days before/after as context (can set number of days of context in options), then vertical ... ellipse as separator
	* Add option to select a specific window of time. Allow also a quick resizing by swiping left or right, similarly to Sleepmeter. Allow in options that swipe does not resize but only scrolls left or right to browse interactively the rest of the data while not zooming too much.
	* checkbox (not in option but main graph view) to allow scrollable graph (ie, fixed width column, with option to set column width in options) or if unchecked by default show all graph and resize to not be scrollable
	* **BEST IDEA: implement circadian misalignment visualization with the CPD method (Composite Phase Deviation)**, can be calculated just from the sleep diary! <https://pubmed.ncbi.nlm.nih.gov/27929109/>
	* BEST IDEA: alternative visualization of the sleep graph, a circular sleep graph, with each disc representing one day around a 24h clock, may allow to more easily spot long term and repeating patterns:
* make an importer from Sleepmeter, and tags should be renamed according to AidsNameOfTag, HindranceNameOfTag, and just NameOfTag if type is tag.
* field "created-on" for tags and eventtypes, so when exported we know why maybe they aren't used before the creation date. Useful for stat tests.
* allow export as long table (ie, columns = dates, rows = event types, cells = date of start to end or just start).
* next app idea: use smartphone to regularly log light intensity + gps position anonymized (perimeter for work, home, shopping or errands, commuting, etc), to quantify ecological light diet (only light intensity but would allow to accumulate lots of data, more than ever, great for a study, because anyone has a smartphone). Absolute values may be off (but some sensors are quite accurate, except below 100 lux), but the relative variations between places should already give a lot of clues.
* <https://semantic-ui.com/>
* BEST IDEA: light sensor measurements:
	* Logo P with the round part as a dynamic O with arrows all around the edge, to show it's circular, like periodic event.
	* Add metadata field for mire like light intensity 
	* Option to capture median light intensity over last second at startup ( check if last acquisition was at least 5 min before) or over last 5s by fast button that can be disabled in options. Show on screen the current intensity and median. 
	* Draw light intensity as edge curve so no point, so can overlap with day graphic or plot separately 
	* This will allow to easily capture ambient light. Capture in phones but also on computers if available, check ef kivy has a cross-platform function maybe. 
	* for each timeperiod (binned by 15min), save min, max, average. On graph, show average by default but can show max or min too.
	* Add a tool tab with a luxmeter that will graph in realtime the lux + save on graph, so users can use the same app for measuring exposure to bright light.
	* for Android, use Plyer:
		* <https://plyer.readthedocs.io/en/latest/#plyer.facades.Light>
		* <https://github.com/kivy/plyer/pull/292>
	* for Windows/Linux, use:
		* <https://pypi.org/project/brightml/>
		* <https://www.c-sharpcorner.com/article/how-to-use-light-sensor-in-windows-10-universal-app/>
* BEST IDEA: GPS measurement, similarly to light sensor, automatically at startup along with light when light capture is enabled at startup, or also when registering any kind of event.
	* Add option in menu to enable GPS measurements
	* Programmatically: check if GPS is enabled, if not do not try to access it (otherwise the user will get a warning to activate GPS and can't use the app, very bothersome, better is to detect ourselves and just show a warning as a temporary popup message when registering event)
	* On export menu: add checkbox (disabled by default) to enable/disable export of GPS data
	* For both light and GPS measurements, save as separate entries. Then can draw a heatmap on a map to show where light is more intense, or any other kind of event (do a SQL query joining the event of interest with the closest GPS measurement).
* IDEA: also use an external service to get free weather measurements along with light and GPS?
* More ideas me:
	* Plot bars with several alternating colors if multiple tags for an event
	* Change color depending on tags: xxx. So sleep bars will have a different color ef very long therapy etc
	* Monitor duration more than 6.5h wake up time is predictive, else ef less than 6h it's out of phase
	* Circalog allow to filter or paint according to sleep length, to filter out sleep out of phase. Will be the most accessible circadian rhythm monitoring. 
	* Event type add blocking option, when shortcut then a blocking event will stop all other blocking events as blocking events can't overlap. It's a convenience feature. 
* FINALLY: publish a small paper so it can be cited and used in sleep research, eg, in JOSS.

