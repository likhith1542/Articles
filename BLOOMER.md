> **30 Apps in 30 Days — Day 01**

# I Built a macOS Menu Bar App to Stop Killing My Plants

*Bloomer is a tiny Swift/SwiftUI app that lives in your menu bar and reminds you to water your plants — because your Monstera doesn't send calendar invites.*

---

## The problem

I have a bad habit: I buy plants, love them for two weeks, and then completely forget they exist. Not because I don't care. Because there's no nudge. My calendar is full of standups and deadlines — not "water the Pothos."

So on Day 01 of my 30 Apps in 30 Days challenge, I built **Bloomer**: a macOS menu bar app that keeps a quiet eye on your plants and taps you on the shoulder when they're thirsty.

---

## What Bloomer does

Bloomer is deliberately small. It does exactly three things:

- Sits in your menu bar as a tiny animated leaf icon
- Tracks each plant's watering and care schedule
- Sends you a native macOS notification when it's time to water

No subscription. No account. No cloud. Your plant data lives in `UserDefaults` and nowhere else.

---

## The build

I built Bloomer in Swift and SwiftUI — my first time shipping a native macOS app. Here's the stack:

| Layer | Technology |
|---|---|
| Menu bar presence | AppKit + `NSStatusItem` |
| Plant animation | CoreGraphics (sine-curve leaf sway) |
| Notifications | `UNUserNotificationCenter` |
| Data persistence | `UserDefaults` |
| Launch at login | `SMAppService` |

The trickiest part? The animation. The leaf in the menu bar gently sways using a sine-curve driven CoreGraphics path rendered inside an `NSImage`. It updates on a `Timer` tied to the main `RunLoop` — a detail that bit me early when my timer silently refused to fire because I hadn't attached it to the right run loop mode.

Another subtle bug: a missing `import Combine` that caused a cryptic compiler error, and an `NSWindow` warning that took longer to chase than it should have. Classic first-native-app tax.

---

## Lessons from Day 01

### Shipping without an Apple Developer account is possible — but annoying

Without a paid developer account, you can't notarize your app. That means Gatekeeper will block it by default. My workaround: distribute via GitHub Releases and a Homebrew Cask (`likhith1542/homebrew-bloomer`) with clear install instructions. Not elegant, but it works.

### Start with the simplest possible feature set

My first instinct was to add growth-stage tracking, soil type settings, humidity logs — the whole greenhouse. I cut all of it. Day 01 had a hard deadline. Bloomer shipped with one job: remind you to water your plant. Everything else is a future version problem.

### Native feels different

Most of my apps are web or cross-platform. Building something that lives in the macOS menu bar, uses native notifications, and launches at login — it feels different. More permanent. Like you've actually shipped something real onto someone's machine.

---

## Install Bloomer

### Option 1 — Homebrew (recommended)

```bash
brew tap likhith1542/homebrew-bloomer
brew install --cask bloomer
```

That's it. Bloomer will appear in your menu bar on next launch.

### Option 2 — Direct download (DMG)

1. Head to the [GitHub Releases page](https://github.com/likhith1542/Bloomer/releases)
2. Download the latest `Bloomer.dmg`
3. Open the DMG and drag **Bloomer.app** to your Applications folder
4. On first launch, macOS may block it since the app isn't notarized. To allow it:

```bash
# Run this once in Terminal after copying the app
xattr -dr com.apple.quarantine /Applications/Bloomer.app
```

5. Launch Bloomer from Applications — the leaf icon will appear in your menu bar

### Option 3 — Build from source

Requires Xcode 15+ and macOS 13 Ventura or later.

```bash
git clone https://github.com/likhith1542/Bloomer.git
cd Bloomer
open Bloomer.xcodeproj
```

Hit **⌘R** to build and run. No external dependencies or package manager setup needed.

---

## System requirements

- macOS 13 Ventura or later
- Apple Silicon or Intel Mac
- ~3 MB disk space

---

## Try Bloomer

Bloomer is free and open source. Source code is on GitHub at [likhith1542/Bloomer](https://github.com/likhith1542/Bloomer).

*If your plants have been silently judging you, now they don't have to.*

---

*This is Day 01 of my 30 Apps in 30 Days challenge. I'm building one small, useful app every day for 30 days — in public. Follow along for Day 02: Streakr, a habit tracker that respects your privacy.*

`#IndieHacker` `#MacOS` `#Swift` `#SwiftUI` `#30AppsIn30Days` `#BuildInPublic` `#PlantCare` `#Bloomer`
