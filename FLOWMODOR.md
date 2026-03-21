**Title:** I Built a Sessions-Like Focus App in One Day. Here's Everything I Learned.
 
**Subtitle:** Day 3 of my 30 Apps in 30 Days challenge — and this one nearly broke me.
 
---
 
It's 11 PM. I have a half-broken Rust compiler error, a floating timer window with white corners that refuses to cooperate, and exactly one day left on my self-imposed deadline.
 
Welcome to Day 3.
 
---
 
### What I built
 
Flowmodoro is a Pomodoro-style focus session manager for macOS and Windows. Think of it like the Sessions app — but fully local, open source, and built in a single day.
 
Here's what it does:
 
**Named sessions.** Not just a faceless countdown. You pick a project, write a task, and optionally add a focus note — a one-line intention that shows up mid-session to keep you honest. "Stay off Slack. No new tabs." That kind of thing.
 
**A floating mini timer.** An always-on-top compact window that sits in the corner while you work. 24:37 in red. That's it. Nothing else.
 
**Session history and stats.** Every session is logged. There's a GitHub-style heatmap showing your focus activity over the last 15 weeks. Per-project breakdowns. Daily totals.
 
**Sound effects.** Built entirely with the Web Audio API — no external files. Ascending chime when work ends. Descending when break ends. A little triumphant fanfare when you complete a session.
 
**100% local.** No accounts. No cloud. No subscription. Your data lives in a JSON file on your machine.
 
---
 
### The stack
 
- **Tauri v2** — Rust backend + webview frontend. ~8MB bundle vs Electron's ~150MB.
- **React 18 + TypeScript** — component-driven UI
- **Zustand** — state management (genuinely the best thing to happen to React state)
- **Tailwind CSS** — utility classes
- **Web Audio API** — synthesized sound effects
 
---
 
### What went wrong (a lot)
 
**The transparent window saga.** I wanted a frameless, rounded window with no white corners. Sounds simple. It is not simple. I spent two hours fighting Tauri's `WebviewWindowBuilder` API before discovering that `transparent: true` only works when defined in `tauri.conf.json`, not in Rust code. The mini floating timer alone took longer than the entire timer logic.
 
**AudioContext in production.** Web Audio works fine in dev. In a production Tauri build, the `AudioContext` gets suspended until a user gesture. So my phase-change sounds were completely silent on first launch. Fix: call `audioCtx.resume()` before every tone and prime the context on first click.
 
**Session completion via skip.** The original `skipPhase` function just transitioned phases. It had zero pomodoro-counting logic. You could skip through all 4 work phases and the session would never end. Fixed by unifying natural end and skip into a single `_advancePhase()` function.
 
**macOS Mission Control.** Frameless transparent windows don't appear in Option+Tab by default. After trying `NSWindowCollectionBehaviorManaged` via `objc2`, activation policies, and `setup()` hooks, the fix was embarrassingly simple: just use `decorations: true` on the main window, like every normal app.
 
---
 
### What I'm proud of
 
The timer state machine. It handles work → break → work cycles, long break intervals, auto-start preferences, and session completion in ~100 lines of clean Zustand. No setTimeout chains, no imperative mess.
 
The project snapshot pattern. When you delete a project, its sessions still show up in history with the original name and color. Because the session stores a snapshot of `projectName` and `projectColor` at creation time, not a reference.
 
The sound design. Three distinct audio signatures — ascending for work complete, descending for break complete, triumphant four-note fanfare for session complete. All synthesized. All under 20 lines of code.
 
---
 
### What I'd do differently
 
I'd design the data model first and the UI second. I built the timer UI before the store was solid, and paid for it in refactoring time.
 
I'd also skip the frameless window entirely. The native macOS titlebar is fine. Fighting the OS for aesthetic reasons cost me three hours I could have spent on features.
 
---
 
### Try it
 
**GitHub:** github.com/likhith1542/flowmodoro
**Download:** github.com/likhith1542/flowmodoro/releases/latest
 
Self-signed. Right-click → Open → Open anyway on first launch.
 
---
 
*This is Day 3 of my 30 Apps in 30 Days challenge. I'm shipping one small app every day in public. Follow along on X @likhith1542.*
 
*Day 1: Bloomer — macOS menu bar plant care reminders*
*Day 2: Streakr — privacy-focused habit tracker*
*Day 3: Flowmodoro — focus session manager*
 
---
