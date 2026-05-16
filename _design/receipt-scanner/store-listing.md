# ReceiptSnap — Google Play Store Listing

All copy is in English (en-US) and tuned for Google Play's character limits. Paste straight into the Play Console "Main store listing" form.

## Identity

| Field | Value |
|---|---|
| App name | `ReceiptSnap` (max 30 chars — uses 11) |
| Package name | `io.github.jenovas.receiptscanner` |
| Default language | English (United States) |
| Category | Finance |
| Tags | Expense tracker, Receipts, OCR, Budget |
| Contact email | `skubisz.mobile.apps@gmail.com` |
| Website | `https://skubiszmobileapps.github.io/receiptsnap/` |
| Privacy policy URL | `https://skubiszmobileapps.github.io/receiptsnap/privacy-policy.html` |

## Short description (80 char max — uses 79)

```
Snap a receipt, get a tracked expense. On-device OCR. No accounts, no cloud.
```

## Full description (4000 char max — uses ~1,650)

```
ReceiptSnap turns a photo of a receipt into a tracked expense in seconds. Point your camera, tap the shutter, confirm the amount — done. Your data lives on your device. There is no account to create, no cloud to sync, and no spreadsheet to babysit.

WHAT IT DOES
• Snap a receipt and on-device OCR fills in the vendor name, amount, date and currency.
• Quickly tag each expense with a category (food, groceries, transport, shopping, healthcare and more).
• See a clean monthly feed with daily groupings and a running total.
• Browse Insights — month-by-month spending broken down by category, with previous/next month navigation.
• Edit or delete any entry. Wipe everything in one tap from Settings.

PRIVACY FIRST
• Receipt photos and extracted data stay on your phone, in app-private storage.
• Text recognition is performed locally by Google ML Kit. Your receipts are never uploaded.
• No analytics. No third-party trackers in the Free tier beyond the ad SDK.
• Uninstall and the data is gone with the app.

FREE TIER
• 25 receipt scans per month.
• Banner ads in the feed; one interstitial after a scan every few uses.
• Read-only access to historical receipts older than the rolling 90-day window.

RECEIPTSNAP PRO
• Unlimited scans.
• No ads, anywhere in the app.
• Full Insights screen unlocked, including the category breakdown.
• CSV export for your records.
• Cancel any time from Google Play → Subscriptions.

DESIGNED FOR PHONES
• Single-handed capture flow.
• Dark "Midnight Luxe" theme by default — easy on the eyes in low light.
• Built with Material 3 and the latest Compose UI on Android 7+.

OFFLINE-FIRST
• Works without an internet connection. The only network traffic is loading ads on the Free tier.

We're a one-person Polish indie studio (Skubisz Mobile Apps, JDG, NIP 7352796906). Feedback, bug reports and feature requests are welcome at skubisz.mobile.apps@gmail.com.
```

## What's new (release notes — 500 char max)

```
v1.0 — first release.
Capture receipts with your camera, on-device OCR fills in vendor, amount and date. Monthly feed and Insights with category breakdown. Pro tier unlocks unlimited scans, no ads, and CSV export.
```

## Promo text (170 char max — optional)

```
On-device receipt OCR. No accounts, no cloud, no spreadsheet. Snap, tag, done — your expenses stay on your phone where they belong.
```

## Required graphics

| Asset | Spec | File |
|---|---|---|
| App icon | 512×512, 32-bit PNG | `icon-512.png` |
| Feature graphic | 1024×500, JPG or 24-bit PNG (no alpha) | `feature-graphic.png` |
| Phone screenshots (≥2, ≤8) | Min 1080px on long edge, 16:9 to 9:16 | See `docs/portfolio-screenshots/` — re-shoot in release build before upload |

## Pricing

- **Free** with in-app subscription (RevenueCat ID `Receipt Scanner Pro`).
- Pro pricing is set inside Google Play Console → Monetisation → Subscriptions. Suggested: localised equivalent of **2.99 EUR / month** and **24.99 EUR / year** (introductory week free trial).

## Content rating

Use Google's IARC questionnaire. Expected outcome: **Everyone / PEGI 3**. Key answers:

- No violence, no profanity, no sexual content, no controlled substances.
- Does the app share user-generated content? **No** (everything stays on device).
- Does the app have in-app purchases? **Yes** (subscription).
- Does the app share personal info? **Yes — advertising ID via AdMob on Free tier**.
- Digital purchases? **Yes** (Pro subscription).

## Target audience

- Primary: 18+ (subscription, finance).
- Secondary: 13–17 only if Poland/EU rules permit; the app itself does not target minors.

## Ads declaration

- **Contains ads: Yes** (Google AdMob, Free tier only).
- Pro purchase removes all ads.

## Distribution

- **Countries**: All available.
- **Devices**: Phone only at v1.0 (Android 7.0+).

## Internal testing — recommended cadence

1. Build signed AAB locally (`./gradlew :composeApp:bundleRelease`).
2. Upload to **Internal testing** track, add 1–3 testers (one of which must be a different Google account from the publisher).
3. Smoke test on a real device: capture → confirm → save → feed → detail → delete; paywall → sandbox purchase → entitlement flip.
4. After a clean test cycle, promote the same AAB to **Production** with `Rollout: 20%`.

