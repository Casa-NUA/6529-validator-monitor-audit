SCOPE CHECKED

Commit 708767c (branch claude/rc16-chain-android), read-only, no build run. All paths below are under `<project>/6529-validator-monitor-android/`.

- Read: both manifests, all gradle files, `Feed.kt`, `RemoteTexts.kt`, `Store.kt`, `Plan.kt`, `History.kt` (Prefs), `Models.kt`, `Strings.kt`, `StringsTable.kt` (English), `StringsAndroid.kt` (English, part of pt/es/fr), `Purchases.kt`, `Alerts.kt`, `RefreshWorker.kt`, `PhoneSync.kt`, `MonitorApplication.kt`, `MainActivity.kt`, `StatusActivity.kt`, `PaywallSheet.kt`, `SetupScreen.kt`, the wear module, `store/*.sh`, `store/play-upload.py`, `store/listing/en-US`, `docs/privacy/monitor-privacy.js`.
- Read in part: `MoreScreens.kt`, `TodayScreen.kt`, `RingWidget.kt`, `RingComplicationService.kt`, `WearActivity.kt`.
- Also inspected: merged release manifests and generated `BuildConfig` left by a local build on 13 Nov (artifacts, not tracked source).
- Live GETs to the four public URLs with a generic client.
- No High-severity finding.

CONFIRMED CLAIMS

| Claim | Verdict | Evidence |
|---|---|---|
| Read-only; no wallet connection, keys or signing | Confirmed | The only crypto is RSA verification of Play purchases (`android/app/src/main/java/casa/nua/monitor/Purchases.kt:201-206`). No WalletConnect/web3/keystore/transaction code or dependency in app, wear or core. Network code is GET only (`android/core/src/main/java/casa/nua/monitor/core/Feed.kt:42-52`, `RemoteTexts.kt:153-158`). |
| No account or login | Confirmed | No auth code. Identity is a typed handle or address stored locally (`History.kt:16-24`). |
| Only reads what the network publishes | Partly | True for data. The app also downloads a wording file from a Casa NUA-run Cloudflare Worker (`RemoteTexts.kt:44`), which is not network data (finding 1). |
| Network data from github.com/Casa-NUA/6529-network-data | Confirmed, with two additions | `Feed.kt:33`. Also `api.6529.io` for the profile (`Feed.kt:34`) and `6529.io/validator/releases.json` (`Feed.kt:35`). |
| Subscription via Play Billing unlocks widgets, Wear, alerts, 5 accounts | Confirmed | Product `casa.nua.6529monitor.pro.monthly` (`Plan.kt:15`). Gates: `RingWidget.kt:89`, `RingComplicationService.kt:51`, `WearActivity.kt:102`, `Alerts.kt:73` and `:202`, `StatusActivity.kt:69`, `Store.kt:73`, `Plan.kt:19`. |
| No ads, no tracking | Confirmed for the app's own code | No analytics, ads or crash SDK in `android/app/build.gradle.kts:80-97`, `wear/build.gradle.kts:71-81`, `core/build.gradle.kts:18-28`. Google's billing library carries its own telemetry (finding 9). |
| Alerts are local, no push | Confirmed | AlarmManager plus WorkManager (`Alerts.kt:125`, `RefreshWorker.kt:28`). No FCM dependency. `Alerts.kt:17-18` states the iOS push relay is not ported. |

FINDINGS

1. **Medium — The app contacts a Casa NUA-operated server that the listing and in-app privacy text do not mention**
   - Evidence: `RemoteTexts.kt:44` `const val url = "https://nua-published-curations.casanua.workers.dev/site/monitor-texts.json"`. Called on every foreground, at most hourly (`MainActivity.kt:50`, `wear/.../WearActivity.kt:88`, `RemoteTexts.kt:49`).
   - Contradicting wording: `store/listing/en-US/full.txt:19` "nothing is sent to us"; `Feed.kt:12` "Nothing here talks to a Casa NUA server"; `StringsTable.kt:42` (setupPrivacy) names only api.6529.io.
   - Why it matters: no user data is in the request, but Casa NUA's Cloudflare endpoint sees the IP address, time and default User-Agent of every phone and watch that opens the app.
   - Fix: disclose the wording fetch in the listing, in setupPrivacy and in the privacy policy; correct the `Feed.kt` comment.

2. **Medium — The remote file can rewrite almost every in-app sentence, including the privacy and provenance statements, with no signature**
   - Evidence: only one key is refused, `RemoteTexts.kt:46` `val refused: Set<Key> = setOf(Key.planLegal)`. Overrides apply to everything else via `Strings.kt:54`, including setupProse, setupPrivacy, source, gSourceDef, unofficial, the paywall feature list, planBuy, and alert titles and bodies.
   - What it cannot do (verified):
     - No links or markup: all text is drawn as plain Compose `Text`, Glance text or plain notification text; there is no linkify, HTML or WebView. URLs opened by the app are hard-coded (`PaywallSheet.kt:40`, `Purchases.kt:212`, `TodayScreen.kt:180`).
     - No format-string abuse: only `%%`, `%@`, `%d` and `%N$@`/`%N$d` are allowed (`RemoteTexts.kt:98-107`), and the argument map must equal the built-in text's (`RemoteTexts.kt:86-87`).
     - No price change: the price comes from Play (`Purchases.kt:115`).
     - Size is capped at 512 KB and 1000 characters per text (`RemoteTexts.kt:47-48`, `:83`); a 404 restores the built-ins (`:169`).
   - Live file today: `{"format":1,"texts":{}}`, so nothing is overridden.
   - Why it matters: whoever controls that Cloudflare account can change what the app says about itself (for example "unofficial", or where data goes) and what alerts say, without a Play review. Trust rests on TLS and that one account.
   - Fix: add setupPrivacy, setupProse, source, gSourceDef and unofficial to `refused`, or sign the file and verify with a bundled public key; publish the file's change history.

3. **Medium — No privacy policy is reachable from the app, and the draft policy does not describe the Android app**
   - Evidence: `Plan.kt:26-27` `val privacyURL: String? = null` with the comment "NOT enough to ship"; the link is hidden when null (`PaywallSheet.kt:76`).
   - The draft `docs/privacy/monitor-privacy.js:9` covers "iPhone and Apple Watch" only, names "6529scan files" (`:14`, stale: the source is now `Casa-NUA/6529-network-data`), and omits Google Play and the wording endpoint.
   - Why it matters: a subscription app gives users no policy to read, and Play requires a policy URL.
   - Fix: publish an Android-accurate policy, set `privacyURL`, and include the endpoint from finding 1.

4. **Low — The subscription check is entirely on the device**
   - What is done correctly:
     - Each purchase's signature is verified against the Play licence public key (`Purchases.kt:143`, `:195-207`; key in `android/gradle.properties:10`, a public key, not a secret).
     - A release build without the key grants nothing (`Purchases.kt:197-199`); the unverified pass is debug-only (`:198`).
     - Pending purchases are not granted (`:142`).
     - Verified purchases are acknowledged (`:144`, `:186-193`).
     - A Play answer with nothing active revokes (`:148-149`).
   - What a tampered client can do:
     - The entitlement is a plain timestamp in SharedPreferences (`Plan.kt:29`, `:36-43`). A rooted device or a patched APK can set it and unlock everything.
     - The watch trusts whatever date the phone's data item carries (`WatchSync.kt:47-48`).
     - There is no server check, so a captured signed purchase could be replayed through a fake billing service on a rooted device.
   - Why it matters: only the publisher's revenue is at risk; the paid features show public data, so no user is harmed. After cancelling, extras last up to about 3 days (`Plan.kt:24`, `Purchases.kt:145`).
   - Fix: none needed for user safety; state it honestly. Server validation would require the server the app avoids.

5. **Low — A release bundle cannot ship unlocked through `monitor.unlockAll` (confirmation)**
   - Evidence: the release build type hard-sets the flag false in both modules (`android/app/build.gradle.kts:55`, `android/wear/build.gradle.kts:48`). `store/build-aab.sh:24` refuses when the property is true, and `:39` passes `-Pmonitor.unlockAll=false`. The generated release `BuildConfig` from the 13 Nov build shows `UNLOCK_ALL = false` and a non-empty licence key for the app.
   - Residual: a debug build made with the property on is unlocked. Debug builds are not what `build-aab.sh` produces; it also checks the signer fingerprint (`:43-54`).

6. **Low — The handle field would send a mistakenly pasted private key to api.6529.io**
   - Evidence: `Feed.kt:59` accepts any ASCII letters, digits and `-_.`; `:60` puts it in the URL path. A 64-hex string (with or without `0x`) passes. Seed phrases contain spaces and are rejected before any request.
   - Why it matters: the app never asks for a key, but a user pasting the wrong clipboard item would leak it to a third party's URL logs.
   - Fix: reject 64-hex and `0x`+64-hex input locally with a warning; accept only handle-shaped input or `0x`+40 hex.
   - Otherwise sound: path and host injection are blocked (`Feed.kt:58-60`); JSON is parsed with optional accessors (`Models.kt:13-20`); failures are caught (`Store.kt:106-109`, `SetupScreen.kt:39`).

7. **Low — Screenshot hooks and an unguarded exported activity ship in release**
   - Evidence: `MainActivity.kt:32` `intent?.getStringExtra("sheet")` opens a sheet directly (`TodayScreen.kt:121`), skipping the paywall redirect at `TodayScreen.kt:133`. `WearActivity.kt:79` reads a `page` extra. `StatusActivity` is exported with no permission (`android/app/src/main/AndroidManifest.xml:32-37`).
   - Why it matters: another installed app can open the Alerts or Add-account sheet, or make the phone speak the validator status. No entitlement is gained, because the logic re-checks the plan (`Alerts.kt:73`, `Store.kt:73`, `StatusActivity.kt:69`), and no data is returned to the caller.
   - Fix: honour the extras only in debug builds; consider `exported="false"` on `StatusActivity` if the shortcut still works.

8. **Low — Every request carries Android's default User-Agent and the device IP to four hosts**
   - Evidence: no User-Agent is set (`Feed.kt:43-47`, `RemoteTexts.kt:153-157`), so the platform default applies; it normally includes Android version, device model and build ID (platform behaviour, not observed on a device). No cookies and no cache (`Feed.kt:46`).
   - Fix: set a fixed generic User-Agent, and name it in the policy.

9. **Info — Google Play Billing bundles Google's own telemetry transport**
   - Evidence: `android/app/build.gradle.kts:92` `billing-ktx:9.1.0`. The merged release manifest (`android/app/build/intermediates/merged_manifests/release/processReleaseManifest/AndroidManifest.xml:243-258`) contains `com.google.android.datatransport…CctBackendFactory`.
   - Why it matters: the billing library can send its own diagnostic events to Google, outside the app's code. Phone app only; the watch has no billing.
   - Fix: mention it in the policy as Google Play's processing.

10. **Low — Background reads run every 15 minutes for everyone, including free users and before an account is chosen**
    - Evidence: `MonitorApplication.kt:47` schedules unconditionally; `RefreshWorker.kt:28` sets 15 minutes; `WearRefreshWorker.kt:25` does the same on the watch. Each run is 4 GETs (`Feed.kt:81-83`, `:90`); measured today, validators.json is about 94 KB and releases.json about 37 KB uncompressed.
    - Why it matters: free users get no alerts or widgets, yet pay the data and battery, and four hosts see their IP roughly every 15 minutes.
    - Fix: schedule only when subscribed or a widget exists, or lengthen the interval for free users.

11. **Low — Integrity of what is shown rests on TLS and the publishers' accounts**
    - Evidence: `Feed.kt:83` and `:96` read releases.json without checking its signature (the repo's `README.md` says the `.sig` is not verified). `Feed.kt:50` reads the body with no size cap. There is no certificate pinning.
    - Why it matters: a compromised data repo could show "Signed" falsely or trigger a false update alert. It cannot move funds, because the app holds nothing.
    - Fix: cap response size; verify the releases signature or say plainly that it is not verified.

12. **Info — Manifest and hardening facts**
    - Permissions declared: INTERNET and POST_NOTIFICATIONS on the phone (`android/app/src/main/AndroidManifest.xml:5-7`), INTERNET on the watch (`android/wear/src/main/AndroidManifest.xml:6`); both are used. Libraries add BILLING, WAKE_LOCK, ACCESS_NETWORK_STATE, RECEIVE_BOOT_COMPLETED and FOREGROUND_SERVICE (merged manifest `:15`, `:26-29`). There is no exact-alarm, location or storage permission.
    - Exported components: `MainActivity`, `StatusActivity`, `RingWidgetReceiver` (standard), `WearActivity`, `WatchSync` (for Google Play services; no permission attribute, `wear/.../AndroidManifest.xml:30`), `RingComplicationService` (protected by BIND_COMPLICATION_PROVIDER, `:44`). `ReminderReceiver` is not exported (`app/.../AndroidManifest.xml:39`). There are no deep-link filters and no providers of the app's own.
    - `allowBackup="false"` in both modules (`:16` phone, `:13` watch), but no `dataExtractionRules`, so device-to-device transfer may still copy the local data on some devices. Fix: add rules that exclude everything.
    - No `usesCleartextTraffic` and no network security config, so cleartext is off by default at targetSdk 36; all URLs are https. No `debuggable` attribute.
    - minSdk 34 phone, 33 watch; targetSdk 36 (`android/app/build.gradle.kts:23-24`, `android/wear/build.gradle.kts:21-22`).
    - R8 minify and resource shrinking are on (`app/build.gradle.kts:56-57`, `wear/build.gradle.kts:49-50`). PendingIntents are immutable (`Alerts.kt:93`, `:140`).
    - Release logging: `Purchases.kt:77`, `:171`, `:189` log billing response codes only, no personal data.
    - Signature algorithm is SHA1withRSA (`Purchases.kt:203`), which is Play's own scheme.

13. **Info — Secrets and build scripts**
    - No secrets in the tree: a grep for private keys, API keys and passwords found nothing; no keystore or service-account file is tracked or in git history; `local.properties` is ignored (`android/.gitignore:4`, `:10-11`; file not opened).
    - The upload keystore lives outside the clone and its password comes from the macOS keychain into an environment variable (`app/build.gradle.kts:38-39`, `store/build-aab.sh:21`, `:33`; `store/make-upload-key.sh:25` refuses a path inside the clone).
    - `store/play-upload.py` holds no credentials (`:25-28` reads the keychain) and refuses non-testing tracks (`:82-83`). It writes the service-account private key to a temp file for the openssl call and deletes it (`:40-47`). Fix: pipe it instead.
    - There is no `distributionSha256Sum` in `android/gradle/wrapper/gradle-wrapper.properties`, no dependency verification file and no version catalog; dependencies are inline, all Google/AndroidX/JetBrains.

14. **Info — Wording accuracy**
    - Provenance is stated honestly and without overclaim: `StringsTable.kt:34` (source), `:36` "unofficial", `:137` "It is not an official 6529 source", `:189` "nobody checks it", `:145` (history is from what this device saw).
    - `StringsAndroid.kt:3`: "Machine translation, reviewed by nobody yet". This includes the subscription terms (planLegal) in 11 languages.
    - Unused iOS push-relay strings remain in the table (`StringsTable.kt:70-72`); no Android code references them.
    - The listing says "one validator node" (`store/listing/en-US/full.txt:21`), consistent with the app.

ENDPOINT TABLE

| Endpoint | Sent | Purpose |
|---|---|---|
| `raw.githubusercontent.com/Casa-NUA/6529-network-data/main/docs/data/` (`network.json`, `validators.json`, `cycles/<day>.json`) | GET; nothing about the user (the whole list is downloaded and searched on the device); IP and default User-Agent | Day data. Phone: on foreground, every ~15 min in background, and from the Status shortcut. Watch: on foreground and every ~15 min. |
| `6529.io/validator/releases.json` | GET; IP and User-Agent | Latest validator version; same cadence |
| `api.6529.io/api/profiles/<typed handle or address>` | The typed handle or address in the URL path; IP and User-Agent | Resolve wallets. Only when the user follows or adds an account; never in background. |
| `nua-published-curations.casanua.workers.dev/site/monitor-texts.json` | GET; IP and User-Agent; no identifiers or cookies | Wording overrides; at most hourly on foreground, phone and watch each |
| Google Play Billing (on-device Play service) | Product ID, purchase token, acknowledgement; Google account handled by Play | Subscription; phone only |
| Wearable Data Layer (Google Play services), item `/monitor/state` | Followed identity (input, handle, wallets) and plan end date (`PhoneSync.kt:28-32`) | Phone to the user's own watch |
| Browser links (user tap only) | Nothing from the app | `play.google.com` terms and subscription management; `app.nua.casa` |

No FCM, no analytics, no crash-reporting endpoint. Widgets and complications make no requests of their own (`RingWidget.kt:87-91`, `RingComplicationService.kt:49-53`).

DATA SAFETY SUMMARY

- **To the developer (Casa NUA):** no user data is collected or stored by app code. The wording endpoint receives connection metadata only: IP address and User-Agent.
- **To 6529 (`api.6529.io`):** the handle or wallet address the user types, once per lookup, plus connection metadata. This is a public identifier chosen by the user, not tied to an account in the app.
- **To GitHub and 6529.io:** connection metadata only; requests are identical for all users.
- **To Google:** purchase data through Play Billing, the billing library's own diagnostics, and the Data Layer sync to the user's watch.
- **Stays on the device** (private app storage, unencrypted, nothing secret):
  - followed identity and other accounts (`History.kt:18-24`);
  - the 60-day per-wallet sign log (`History.kt:38-40`);
  - alert switches and sent-alert keys (`Alerts.kt:47`, `:58`, `:88`);
  - the plan end date (`Plan.kt:29-30`);
  - the cached public day (`Feed.kt:124`);
  - the cached wording file (`RemoteTexts.kt:52-54`).
- **Not accessed:** location, contacts, photos, microphone, camera, advertising ID, device identifiers.
- All traffic is HTTPS.
- Suggested declaration: no data collected by the developer. Whether the handle sent to api.6529.io counts as "shared" is the publisher's call; disclose it in the policy either way.

NOT CHECKED / LIMITS

- Static review only: no build, no device run, no traffic capture. The User-Agent content and the billing library's telemetry are inferred from platform and library behaviour and from a build artifact dated 13 Nov, not from the audited commit's own build.
- No released AAB was inspected; R8 output and final merged manifests for 708767c were not regenerated.
- `DaySnapshot.kt`, `Ring.kt`, `NetworkSheet.kt`, `Common.kt` and the 11 non-English string tables were not read line by line; the translations were not checked for meaning.
- Not audited: the Cloudflare Worker's code and logging, the GitHub data collector, 6529's API and Play Console configuration.
- Keystore, keychain items and `local.properties` were deliberately not opened.
- iOS was not audited. Differences noticed: iOS has a push relay (`iOS/PushRelay.swift`, `push/`) that Android does not, and `Shared/Plan.swift:34` also has a null privacy URL.