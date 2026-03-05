# CircleUp

**Automatically track the time you spend with the people who matter most.**

CircleUp runs quietly in the background, detects when friends are nearby using Bluetooth, and logs your hangouts as Calendar events — no manual input needed. Over time it builds a personal record of your social life: who you see, how often, and for how long.

---

## How It Works

1. You and your friends each install CircleUp
2. You invite them into your Circle via a private share link
3. The app detects when Circle members are near you (via Bluetooth LE)
4. After 20 minutes together, a hangout is automatically logged to your Calendar
5. Stats build up over time — frequency, duration, streaks, and more

No accounts. No passwords. No GPS tracking between users. Everything lives in your own iCloud.

---

## Features

- **Passive detection** — CoreBluetooth scans in the background; you never have to open the app
- **Private invite system** — friends join via a 72-hour link; no public user directory exists
- **Apple Calendar integration** — every hangout becomes a native Calendar event you can edit or delete
- **Per-person stats** — total hangouts, hours spent together, average duration, longest streak
- **Social heatmap** — a 52-week GitHub-style grid of your social activity
- **Group hangouts** — if multiple Circle members are present, one combined event is created
- **Hangout notes** — add a note or emoji tag to any session after the fact
- **Weekly digest** — optional Sunday notification summarising your week
- **Re-engagement nudges** — optional reminder if you haven't seen someone in X weeks
- **Full privacy controls** — export or delete all your data at any time from Settings

---

## Requirements

| Requirement | Minimum |
|---|---|
| iOS | 16.0+ |
| Xcode | 15.0+ |
| Swift | 5.9+ |
| iCloud account | Required (CloudKit sync) |
| Bluetooth | Required |
| Calendar access | Required |
| Location | Optional (venue tagging) |

> CircleUp uses **zero third-party dependencies**. Everything is built on native Apple frameworks.

---

## Setup

### 1. Clone the repo

```bash
git clone https://github.com/YOURNAME/circleup.git
cd circleup
```

### 2. Open in Xcode

```bash
open CircleUp.xcodeproj
```

### 3. Set your Bundle ID

In Xcode → CircleUp target → **Signing & Capabilities**, set your bundle identifier:

```
com.YOURNAME.circleup
```

### 4. Configure CloudKit

In `CloudKitService.swift`, replace the TODO with your container identifier:

```swift
// TODO: Replace with your CloudKit container ID
let container = CKContainer(identifier: "iCloud.com.YOURNAME.circleup")
```

Then in Xcode → **Signing & Capabilities** → add the **CloudKit** capability and create a container matching that identifier.

### 5. Configure Universal Links (for invites)

In `Info.plist`, replace the TODO under `Associated Domains` with your domain:

```
applinks:YOURDOMAIN.com
```

You'll need to host an `apple-app-site-association` file at `https://YOURDOMAIN.com/.well-known/apple-app-site-association`. If you don't have a domain, you can use a free Cloudflare Pages or GitHub Pages site.

### 6. Run on a real device

> ⚠️ Bluetooth does not work in the iOS Simulator. You must run on a physical iPhone.

Select your device in Xcode and hit **Run (⌘R)**. Grant permissions when prompted on first launch.

---

## Distributing to Friends (TestFlight)

CircleUp is designed to be distributed via **TestFlight** — Apple's free beta platform. Anyone you invite gets a normal App Store-style install experience with no technical setup on their end.

**Requirements:** Apple Developer Program ($99/year — one person pays, everyone benefits)

1. Archive the app in Xcode: **Product → Archive**
2. Upload to App Store Connect via Xcode Organizer
3. In [App Store Connect](https://appstoreconnect.apple.com), go to **TestFlight → External Testing**
4. Add your friends' email addresses
5. They receive an email, install the TestFlight app, and join CircleUp

TestFlight builds are valid for **90 days**. Re-upload a new build before expiry to keep things running.

---

## Permissions Explained

| Permission | Why it's needed |
|---|---|
| **Bluetooth (Always)** | Detecting when Circle members are nearby — this is the core feature and must run in the background |
| **Calendars (Full Access)** | Creating hangout events and checking for duplicates before writing |
| **Location (When In Use)** | Optional — used only to tag the venue of a hangout if you enable it |
| **Notifications** | Weekly digests and optional friend check-in reminders |

CircleUp does **not** collect your location to track you between users. Bluetooth proximity is used instead — it only tells the app that two devices are close together, not where they are.

---

## Privacy

- No email address, phone number, or Apple ID is ever collected
- All data is stored in **your own private iCloud container** — the developer has zero access
- Bluetooth advertisements use a hashed ID — your identity is never broadcast in plaintext
- You can export all your data as JSON: **Settings → Privacy → Export My Data**
- You can permanently delete everything: **Settings → Privacy → Delete All Data**

---

## Project Structure

```
CircleUp/
├── App/
│   ├── CircleUpApp.swift          # Entry point, Universal Link handling
│   └── AppState.swift             # Global observable state
├── Features/
│   ├── Circle/                    # Friends list, person detail, invite sheet
│   ├── Timeline/                  # Hangout history, session detail
│   ├── Stats/                     # Heatmap, rankings, summaries
│   ├── Settings/                  # Preferences, privacy controls
│   └── Onboarding/                # First launch permissions + profile setup
├── Services/
│   ├── BLEProximityService.swift  # CoreBluetooth advertising + scanning
│   ├── HangoutDetector.swift      # State machine: PENDING → CONFIRMED → CLOSED
│   ├── CalendarService.swift      # EventKit read/write
│   ├── CloudKitService.swift      # CloudKit CRUD + invite tokens
│   ├── InviteService.swift        # Link generation + deep link handling
│   ├── NotificationService.swift  # Weekly digest + nudges
│   └── HangoutStatsService.swift  # Pure stats computation functions
├── Models/
│   ├── CircleMember.swift         # SwiftData model
│   └── HangoutSession.swift       # SwiftData model
└── Utilities/
    ├── Constants.swift
    └── Extensions.swift
```

---

## Key Frameworks

| Framework | Usage |
|---|---|
| `CoreBluetooth` | Background BLE advertising and scanning |
| `EventKit` | Calendar event creation and management |
| `CloudKit` | Private data sync and invite token sharing |
| `SwiftData` | Local persistence |
| `Swift Charts` | Heatmap, bar charts, visualizations |
| `NearbyInteraction` | Optional UWB fine proximity (iPhone 11+) |
| `UserNotifications` | Weekly digest and nudge notifications |
| `BackgroundTasks` | BGProcessingTask for post-hangout writes |

---

## Roadmap

- [ ] watchOS companion app (detect hangouts from wrist)
- [ ] Widget — "Last seen" glanceable for home screen
- [ ] Shared hangout notes (both parties can annotate the same session)
- [ ] CSV export for the data nerds
- [ ] Public App Store release

---

## License

MIT License — do whatever you want with it, just don't sell it back to people as-is.
