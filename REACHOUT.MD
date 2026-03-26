# Reach Out 📞

> **We track habits. We track fitness. We track finances.**
> But we forget to track the people who matter most.

**Reach Out** is a privacy-first relationship reminder app that nudges you to stay in touch with the people you care about — before weeks quietly turn into months.

100% offline. No backend. No cloud. No tracking. Ever.

---

## The Problem

You don't forget *who* you care about.  
You forget *when* you last reached out.

A quick "I should text them sometime" becomes a year of silence. Reach Out fixes that — not with guilt, but with gentle, smart reminders.

---

## Features

- 📋 **Contact Tracking** — Pick the people that matter. They get stored locally.
- 📞 **Auto Call Log Sync** — App reads your call history and updates *last contacted* automatically. No manual input.
- 🔔 **Smart Reminders** — Notifications fire at the right time, not randomly.
- 🔥 **Streak System** — Every interaction bumps your streak. Simple, motivating, non-toxic.
- 💬 **One-tap Actions** — Call or WhatsApp directly from the contact card.
- 🌙 **Dark-first UI** — Distraction-free, action-focused design.
- 🔒 **100% Local** — Nothing leaves your device. No accounts, no servers, no tracking.

---

## Tech Stack

| Layer | Technology |
|---|---|
| UI | Flutter |
| State Management | Riverpod |
| Local Database | Hive |
| Contacts Access | flutter_contacts |
| Call History | call_log (Android) |
| Notifications | flutter_local_notifications |
| Timezone Handling | timezone |

---

## Architecture

Everything runs on-device. There is no network layer.

```
Phone Contacts
     │
     ▼
  [Selected Contacts] ──────► Hive (local DB)
                                    │
  Call Logs ──► Number Matching ────► lastContacted updated
                                    │
                               Reminder Engine
                                    │
                             Local Notifications
```

No APIs. No syncing. No accounts. It just works, offline.

---

## How It Works

### 📞 Call Log Sync

Instead of asking you to manually log every conversation, Reach Out reads your call history and matches it against your tracked contacts — automatically updating the *last contacted* timestamp.

Phone numbers are normalized before comparison (strips country codes, spaces, dashes) so matching works reliably across formats.

### 🔔 Notifications

Scheduling reliable notifications on Android is harder than it looks.

**Issues hit:**
- Exact alarms blocked on Android 12+ without special permission
- Background notifications silently failing
- Timezone inconsistencies causing off-schedule triggers

**Fixes applied:**

```dart
// Use inexact scheduling (Android 12+ compatible)
AndroidScheduleMode.inexact

// Set timezone correctly — not just 'local'
tz.initializeTimeZones();
tz.setLocalLocation(tz.getLocation('Asia/Kolkata'));
```

All notifications are also rescheduled fresh on every app startup to survive reboots and OS-level kills.

### 🔥 Streak System

Streaks count interactions, not consecutive days. Every call or message increments your streak.

No punishment for missing a day. Just positive reinforcement for showing up.

---

## Getting Started

### Prerequisites

- Flutter 3.x+
- Android SDK (for call log and notification features)
- Physical Android device recommended (emulators can't read real call logs)

### Run Locally

```bash
git clone https://github.com/likhith1542/reach-out.git
cd reach-out
flutter pub get
flutter run
```

### Permissions Required (Android)

```xml
<uses-permission android:name="android.permission.READ_CONTACTS" />
<uses-permission android:name="android.permission.READ_CALL_LOG" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<uses-permission android:name="android.permission.SCHEDULE_EXACT_ALARM" />
```

---

## Roadmap

- [ ] Recurring notification engine (weekly / monthly cadences)
- [ ] Weekly relationship insights
- [ ] Badges and milestone rewards
- [ ] iOS support (call log equivalent via Screen Time API research)
- [ ] Export / backup contact list locally

---

## Lessons Learned

1. **Android OS restrictions are real.** Exact alarms, background processes, and doze mode will fight you. Work with the OS, not against it.
2. **Offline-first is simpler to build but harder to sync.** Smart local logic (number normalization, startup reschedule) does the heavy lifting.
3. **Notifications are a UX contract.** If they're unreliable, users lose trust fast.

---

## Privacy

- No user accounts
- No analytics
- No network requests
- All data stored in Hive on your device
- Uninstalling the app deletes everything

---

## Contributing

PRs are welcome. If you find a bug or have a feature idea, open an issue.

---

## License

MIT — use it, fork it, ship it.

---

> *We optimize everything in life. But relationships? We leave them to chance.*  
> *Maybe it's time we don't.*

**⭐ Star the repo if this resonates with you.**
