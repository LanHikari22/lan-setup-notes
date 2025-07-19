#lan #idea #tooling #obsidian #schedule

2025-07-18 Wk 29 Fri - 20:19

# 1 Objective

The [Day Planner](<https://github.com/ivan-lednev/obsidian-day-planner>) plugin comes with intuitive customizable schedules. 

2025-07-18 Wk 29 Fri - 20:19

- [ ] Add built-in support for multi-user schedules.
- As is, each user will have to customize where daily notes go. This may be achieved by having each user configure `.obsidian/daily-notes.json` and adding this to `.gitignore`. Alternatively, it can be part of the plugin itself to specify an identity. And we can `.gitignore` that specific setting.

- [ ] Create Named schedule templates. 
- It should be easy to carry the schedule from last week onward without having to manually copy 7 file contents or things like this. Maybe this can also be more modular than just copying the last template, and instead we can move around items across days or within same day under the same template. They move together, but their relative positioning remains fixed.

- [ ] Investigate allowing note links in UI
- Currently, you can add a link with double braackets in the daily note itself, but not in the UI. Even if you add it, you need to double click, first to go do the daily, then to go to the note. Maybe this can be more seamless.
- This is already requested in this [feature issue #745](<https://github.com/ivan-lednev/obsidian-day-planner/issues/745>).

- [ ] Bug: Settings say it only fetches bullets under the specified Day Planner heading in the daily, this has been reproduced false.
	- [#730](<https://github.com/ivan-lednev/obsidian-day-planner/issues/730>) issue has already been filed for this.