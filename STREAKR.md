# I Built a Habit Tracker That Doesn't Know Your Email Address

### Day 2 of my #30AppsIn30Days challenge — and this one made me rethink everything I assumed about productivity apps.

---

Every habit tracker I've ever used has asked me the same thing before I tracked a single habit.

*Create an account.*

Then it syncs my data to a server I don't control, sends me push notifications I didn't ask for, and eventually offers me a "Pro plan" to unlock the features I actually need.

I've tried Habitica, Streaks, Habit — good apps, all of them. But somewhere between creating my account and setting up my first habit, I always felt a small friction. A reminder that I'm not really the user. I'm the product.

So on Day 4 of my #30AppsIn30Days challenge, I built something different.

---

## Meet Streakr

Streakr is a local-first habit tracker for macOS, built with Tauri and React. It has no backend. No database. No analytics. No account.

When you open it for the first time, there's no signup screen. You just... start.

Your data lives in a single JSON file on your machine at:

```
~/Library/Application Support/com.streakr.habittracker/streakr_data.json
```

That's it. I genuinely don't know you exist.

---

## What it does

The app covers everything you actually need from a habit tracker:

**Streak tracking that respects real life.** Not all habits are daily. Some are "Mon/Wed/Fri". Some are "3 times a week, whenever". Streakr handles all three frequencies, and rest days don't break your streak. A Wednesday rest day on a Mon/Wed/Fri habit is expected — it shouldn't penalise you.

**At-risk warnings.** If you have a streak of 5+ days going and haven't checked off a habit yet today, Streakr flags it. Gentle. Not pushy. Just a heads-up before you accidentally let it die.

**GitHub-style heatmaps.** Every habit gets a 6-month activity grid — the same visual language developers use to see their contribution history. You can also click any day on the monthly calendar to retroactively mark or unmark it. Forgot to log yesterday? No problem.

**A stats view that actually shows you something.** Completion rate, current streak, best streak, 14-day bar charts, a habit leaderboard. All calculated locally from your JSON file.

**Dark and light mode.** Warm dark by default (because building at midnight is a lifestyle), clean light mode for mornings. Follows your system or you can set it manually.

**Full export and import.** Download your entire data as JSON. Restore it on a new machine. Forever.

---

## The tech stack

I built this with **Tauri v2** — and if you haven't tried Tauri yet, it's worth a look.

Tauri lets you build native desktop apps with a web frontend (React, in this case) and a Rust backend. The resulting binary uses the OS's built-in webview instead of bundling Chromium, so the whole app is **~5MB**. Compare that to Electron apps, which routinely ship 150–200MB just for the runtime.

The full stack:

- **Tauri v2** — native shell, file system access, window management
- **React 18 + TypeScript** — UI
- **Zustand** — state management (lightweight, no boilerplate)
- **Tailwind CSS** — styling
- **Recharts** — charts in the stats view
- **Framer Motion** — animations
- **date-fns** — all the date logic
- **LocalStorage / Tauri FS** — storage, with automatic fallback

The app detects whether it's running inside Tauri or a plain browser. In Tauri it uses the filesystem. In a browser it falls back to localStorage. This means the same React code works in both environments.

---

## The hard parts

### Streak logic is trickier than it looks

A "daily" habit is easy — did you do it today? But "3 times per week" is genuinely tricky. Does a week start on Sunday or Monday? If you complete it on Monday and Wednesday, and then it's Saturday — are you on track? What if you travel and miss a whole week?

I ended up building a `calculateStreak` function that handles three modes separately: daily (simple backwards walk), weekly (day-of-week matching), and times-per-week (weekly bucket counting). The streak number you see is always accurate to your frequency, not just a raw day count.

### Tauri v2 permissions are unforgiving

Tauri v2 has a new capability-based permission system that's more granular than v1. The documentation is still catching up. I spent more time than I'd like to admit fighting permission strings like `fs:allow-appdata-read-recursive` versus `fs:allow-read-recursive` (the latter doesn't exist).

If you're building with Tauri v2, the error messages do tell you the exact list of valid permissions — read them carefully. The answer is always in there.

### TypeScript strict mode catches everything

I kept `strict: true` in tsconfig. It caught 19 errors on the first build attempt — unused imports, wrong CSS property names, incorrectly typed function parameters. All real bugs, all things that would have caused subtle runtime issues. Strict mode is painful for about 20 minutes and then permanently worth it.

---

## What I'd do differently

**Drag-to-reorder habits.** I planned it, didn't ship it. The order is fixed to creation date right now. Would add it in v1.1.

**Native notifications.** The architecture supports it (tauri-plugin-notification is installed), but wiring up per-habit reminder times with actual OS notifications needs a Rust command to schedule them. Left it for a later day.

**Better onboarding.** The first-launch screen is fine but could be warmer. Show a sample habit. Make the empty state feel inviting rather than just empty.

---

## Why local-first?

This is the question I keep coming back to when I'm deciding what to build.

There's a certain class of apps where the cloud adds genuine value — collaboration, real-time sync across devices, sharing with others. Those apps need accounts. The trade is worth it.

But a habit tracker? The only person who needs to see my habit data is me. I don't want to share it. I don't need it synced to five devices. I just need it to be there when I open my laptop tomorrow.

For apps like this, the cloud is overhead. Account management, GDPR compliance, servers to maintain, potential data breaches to worry about. The user gets none of the upside and all of the downside.

Local-first software is a design philosophy I'm increasingly drawn to. Your data is yours. The app works offline, always, because it has no other mode. And when the company behind the app eventually sunsets it — because they all do eventually — your data doesn't disappear with it.

---

## Try it

Streakr is free and open source.

- **Website:** [Website](https://streakr.likhith.co.in)
- **Download:** [GitHub Releases](https://github.com/likhith1542/streakr/releases)
- **Source code:** [github.com/likhith1542/streakr](https://github.com/likhith1542/streakr)

macOS 11+ required. Apple Silicon native. ~5MB.

---

## The challenge

This is App 2 of 30. I'm building and shipping one app every day for 30 days, in public.

The rules are simple: ship something real every day. No half-finished prototypes. No "coming soon" pages. A real, downloadable, working app.

So far:
- **Day 1:** Bloomer — macOS menu bar plant care reminder (Swift/SwiftUI)
- **Day 2:** Streakr — privacy-first habit tracker (this one)

Follow along on [LinkedIn](https://linkedin.com/in/likhith-garapati) and [X/Twitter](https://x.com/Likhith_1542) with **#30AppsIn30Days** and **#BuildInPublic**.

If you're building something in public too — reply or reach out. I'd love to see what you're making.

---

*Built in ~8 hours. Shipped the same day. Not perfect. But real.*

---

**Tags:** Productivity · Software Development · Tauri · React · Open Source · Build In Public · Indie Hacking · macOS · Privacy
