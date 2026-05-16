# Play Console — Data Safety form

Answers for the **Data Safety** section of the Play Console listing. Open the form at Play Console → App → App content → Data safety.

These answers match what `docs/legal/privacy-policy.md` already discloses. If you ever change one, change the other.

## 1. Data collection & security

| Question | Answer |
|---|---|
| Does your app collect or share any of the required user data types? | **Yes** |
| Is all of the user data collected by your app encrypted in transit? | **Yes** (AdMob and RevenueCat both use HTTPS) |
| Do you provide a way for users to request that their data be deleted? | **Yes** — Settings → Clear All Data wipes on-device data; for RevenueCat / AdMob data, email `skubisz.mobile.apps@gmail.com` |

## 2. Data types

Answer the per-type checklist this way:

### Personal info
- **Name** — No.
- **Email address** — No.
- **User IDs** — Yes (RevenueCat-generated anonymous UUID for tracking subscription entitlement). Collected, Not Shared, Required. Purpose: **App functionality** (subscription tracking).
- **Address** — No. Phone number — No. Other personal info — No.

### Financial info
- **User payment info** — No (Google Play handles payment, your app never sees it).
- **Purchase history** — Yes (RevenueCat receives the SKU and date). Collected, Not Shared, Required. Purpose: **App functionality**.
- **Credit score / Other financial info** — No.

### Health & fitness — No.

### Messages — No.

### Photos and videos
- **Photos** — Yes. Collected, Not Shared, **Optional** (user chooses what to capture). Stored locally only; never transmitted. Purpose: **App functionality**.
- **Videos** — No.

### Audio files — No.

### Files and docs — No (the receipt JPEGs are stored in app-private storage and never shared).

### Calendar — No. Contacts — No.

### App activity — No analytics or interaction tracking SDK shipped in v1.0.

### Web browsing — No.

### App info and performance
- **Crash logs** — No (Crashlytics is not wired in v1.0).
- **Diagnostics** — No.
- **Other app performance data** — No.

### Device or other IDs
- **Device or other IDs** — Yes (**Advertising ID** via AdMob, Free tier). Collected **AND Shared with Google** (third-party advertising). Required when ads are served (Free tier). Purpose: **Advertising or marketing** + **Analytics**. Pro tier removes this.

## 3. Sharing

For Advertising ID:
- Shared with: **Google AdMob**.
- Purpose: **Advertising / marketing**.
- Optional?: **Yes** — upgrading to Pro stops the AdMob SDK from being initialised at all.

For Purchase history (RevenueCat):
- Shared with: **RevenueCat (subscription analytics)** and **Google Play (billing)**.
- Purpose: **App functionality**.
- Optional?: **No** — required to use the Pro tier.

## 4. Security practices

- Data is encrypted in transit: **Yes**.
- Users can request data deletion: **Yes** — in-app `Settings → Clear All Data`; for third-party data, contact `skubisz.mobile.apps@gmail.com`.
- App is independently security reviewed: **No** (small indie publisher).
- Family Policy declarations: not applicable — not directed at children.

## 5. Government requests

You hold no identifiable receipt content. If a government request arrives, the truthful response is "we have no data identifying this user." Document any received requests for transparency.
