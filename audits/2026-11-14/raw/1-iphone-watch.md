Base path for every file:line below: `<project>/6529-validator-monitor-features/` (branch `claude/rc16-chain`, HEAD `03e128d`, no tracked file modified).

## SCOPE CHECKED

- **Read in full:** `Shared/{Feed,RemoteTexts,Store,AppGroup,Plan,Purchases,Models,History,DaySnapshot}.swift`, `Shared/PrivacyInfo.xcprivacy`, `iOS/{MonitorApp,PushRelay,Alerts,Intents,PhoneSync,SetupView,PaywallView}.swift`, `Watch/MonitorWatchApp.swift`, `Widget/MonitorWidget.swift`, `WatchWidget/MonitorWatchWidget.swift`, `push/{worker.js,wrangler.toml,README.md}`, `project.yml`, `testflight.sh`, `install-personal.sh`, `.gitignore`, `docs/privacy/monitor-privacy.js`, `appstore/metadata/en-US/description.txt`, generated `iOS/Info.plist`, `Watch/Info.plist`, `Widget/Info.plist`, `iOS/Monitor.entitlements`, `Watch/MonitorWatch.entitlements`.
- **Read in part or by search:** `Shared/WidgetShared.swift` (1–130), `iOS/TodayView.swift` (1–135 plus gating lines), `iOS/MoreViews.swift` (60–145), `Watch/WatchTodayView.swift` (14–60), `iOS/NetworkView.swift`, `Shared/Strings.swift` (English and Portuguese tables, STR logic).
- **Not read:** the ten other `Strings+<lang>.swift` tables, `StoreKit/Monitor.storekit`, `push/test.mjs`, `appstore/*` other than the English description, `tools/*`.
- **Repo-wide searches:** imports and package managers, Keychain/crypto/signing/wallet terms, HTTP methods, URL sinks (`openURL`, `Link`, WebView, markdown, URL schemes), trackers and SDK names, ATS and usage-description keys, secret patterns in the tree and in the 92-commit history.
- **Live GETs (generic user agent, nothing identifying):** the remote-texts URL, `network.json`, `releases.json`, and one api.6529.io profile (a public third-party handle).

## CONFIRMED CLAIMS

1. **Read-only, cannot write to the network or move assets — confirmed.** The only non-GET request in the app is the relay POST (`iOS/PushRelay.swift:129`). All other traffic is GET through `Feed.get` (`Shared/Feed.swift:54-62`) and `RemoteTexts.refresh` (`Shared/RemoteTexts.swift:125-129`). There is no RPC, transaction or contract code.
2. **No wallet connection, no keys, nothing to sign — confirmed.** Searches for Keychain, CryptoKit, secp256, WalletConnect, web3, mnemonic and LocalAuthentication return nothing. Imports are Apple frameworks only. The only signing code is the Worker's APNs token (`push/worker.js:262-270`), server-side and unrelated to wallets.
3. **No account or login — confirmed.** Setup is one text field (`iOS/SetupView.swift:21-28`) feeding `Feed.resolve` (`Shared/Feed.swift:69-88`). No credential is stored anywhere.
4. **"Only reads what the network already publishes" — partly.** The day's data is an unofficial republication by Casa NUA from one validator node, not a 6529 publication (`Shared/Models.swift:3-6`). The app says so in-app (`Shared/Strings.swift:97,99,223`). Profiles come from 6529's public API and releases from 6529.io (`Shared/Feed.swift:34-35`).
5. **Data source is `github.com/Casa-NUA/6529-network-data` — confirmed** (`Shared/Feed.swift:33`, `push/worker.js:28`), plus the three other hosts in the endpoint table.
6. **"Data Not Collected" — confirmed for build 1.0 as committed, with one unverifiable condition.**
   - `PUSH_RELAY_URL` is empty (`project.yml:28`) and has been in every commit, so the relay switch is hidden (`iOS/PushRelay.swift:20-21,40`; `iOS/MoreViews.swift:101`). No device token is ever requested or sent (`iOS/PushRelay.swift:57,154` are both gated on it).
   - The one request to a publisher-run server is a parameterless GET for the texts file. Whether that server logs IP addresses is outside this repo.
   - The label would stop being accurate once the relay is enabled (Finding 1).
7. **Subscription unlocks widgets, Watch, alerts, Siri, up to 5 accounts — confirmed.**
   - Product id: `Shared/Plan.swift:19`.
   - Gates: widgets `Shared/WidgetShared.swift:57`; Watch `Watch/WatchTodayView.swift:22`; alerts `iOS/Alerts.swift:67`; Siri `iOS/Intents.swift:15`; accounts `Shared/Store.swift:87`; history and countdown `iOS/TodayView.swift:263,286`.
   - Free: Today screen, network map (`iOS/TodayView.swift:101-102`), glossary.
8. **No ads, analytics, trackers, crash reporters or remote fonts — confirmed.** No third-party dependency exists (no SPM, Pods or Carthage; `project.yml` has no packages). The font is bundled (`Shared/Brand.swift:94`). `NSPrivacyTracking` is false (`Shared/PrivacyInfo.xcprivacy:10-11`).

## FINDINGS

**1. Medium (latent, not active in 1.0) — Enabling the push relay makes "Data Not Collected" inaccurate**
- Evidence:
  - `iOS/PushRelay.swift:116-117` posts `token`, `wallet`, `env`, `kinds`, `day`, `shown`.
  - `push/worker.js:68` stores them with a 7-day TTL, renewed daily and automatically (`iOS/PushRelay.swift:114-115`, the day is part of the sync signature).
  - `Shared/PrivacyInfo.xcprivacy:14-15` declares `NSPrivacyCollectedDataTypes` empty.
- Why it matters: a persistent device-level identifier (the APNs token) linked to the followed wallet is kept on the developer's Cloudflare KV beyond the request. That is collection under Apple's definition. The entry also reveals that the device is subscribed, which the draft policy admits (`docs/privacy/monitor-privacy.js:19`).
- Fix: before setting `PUSH_RELAY_URL`, declare Identifiers → Device ID (App Functionality, not linked, not tracking) and the followed wallet in the label and in the privacy manifest.

**2. Medium (latent) — Relay keeps device tokens after "off", contrary to "deletes at once"**
- Evidence:
  - `push/worker.js:179,190,210` writes `marks:<day>` keyed `${sub.token}:${kind}` with `DAY_TTL` of 3 days (`:33`).
  - `push/worker.js:199,204,211` writes `marks:update:<version>` keyed by token with `UPDATE_TTL` of 60 days (`:40`). The TTL restarts on every write, so the key lives while that version stays latest.
  - `/v1/unsubscribe` deletes only `sub:<token>` (`push/worker.js:71-73`).
  - Claims: "Switching this off deletes them" (`Shared/Strings.swift:135`); "deletes the entry at once… keeps exactly this" (`docs/privacy/monitor-privacy.js:19`).
- Why it matters: after a user turns alerts off or uninstalls, the token (without the wallet) stays readable in KV for 3 to 60+ days. The published list of what is stored omits these marks.
- Fix: store a salted hash of the token in marks, or remove the token from both mark objects on unsubscribe; shorten the update TTL; correct the wording.

**3. Medium (latent) — Relay endpoints are unauthenticated and unlimited**
- Evidence:
  - `push/worker.js:58-70` accepts any hex token and any wallet. There is no App Attest, no entitlement check, no rate limit (acknowledged in `push/README.md:25-26`).
  - `MAX_PUSHES = 35` per run (`:44`), and subscriptions are processed in key order (`:94-95,152`).
  - A dead token costs one push plus one KV delete (`:188-191`). Marks are saved only at the end of the run (`:210-211`), and KV calls in the loop are not caught.
  - `body.token` on a JSON `null` body throws (`:55`).
- Why it matters:
  - Anyone can fill the KV with fake subscriptions that use up each run's push budget, delaying or stopping real users' alerts.
  - If a run aborts mid-loop, for example on the subrequest ceiling the Worker's own comment assumes (`:42-43`), marks are lost and alerts already sent can repeat every 5 minutes. I did not test this.
  - Non-subscribers can register for pushes. A device token is not guessable, so targeted unsubscribing of a victim is not practical.
- Fix: App Attest or a signed-transaction check on subscribe; per-wallet and global caps; save marks incrementally; wrap KV operations in try/catch; reject non-object bodies.

**4. Low — Remote texts can replace almost all in-app wording after review**
- Evidence:
  - `project.yml:30` points at a `workers.dev` URL; `Shared/RemoteTexts.swift:119-142` fetches it hourly; `Shared/Strings.swift:60-62` applies overrides.
  - Only `.planLegal` is refused (`Shared/RemoteTexts.swift:43`).
  - Checks: 512 KB cap, 1000 characters per text, strict placeholder whitelist that must match the built-in text (`Shared/RemoteTexts.swift:60-116`).
  - No link, markdown or WebView sink exists; every URL the app opens is hard-coded (`iOS/PaywallView.swift:14`, `Shared/Plan.swift:34`, `iOS/TodayView.swift:90`).
  - Live file today: `{"format":1,"texts":{}}`.
- Why it matters: the file cannot inject code, links or markup, and the format-string checks look sound. But whoever controls that Worker can rewrite privacy assurances (`setupPrivacy`, `pushPrivacy`), paywall feature text, the buy-button label and notification texts without App Review. There is no signature beyond TLS. The Worker's source and logging are not in this repo.
- Fix: also refuse the privacy and paywall keys, or sign the file and verify with a bundled public key; publish the Worker source and its log setting.

**5. Low — `PrivacyInfo.xcprivacy` comment is stale**
- Evidence: `Shared/PrivacyInfo.xcprivacy:6-9` says "Nothing reaches a server of ours — there is none" and names 6529scan. The app contacts Casa NUA's Worker (`project.yml:30`) and reads 6529-network-data.
- Why it matters: it is a comment, not a declared field, but it ships in the bundle and contradicts the code.
- Fix: update the comment.

**6. Low — Network data is trusted without integrity checks, and version parsing is lenient**
- Evidence:
  - `Shared/Models.swift:12`: "signed feed; signature not checked here".
  - `Shared/Feed.swift:33` reads a mutable `main` branch.
  - `Shared/Feed.swift:176-186` accepts any string whose dotted head contains one integer, for example `1.any text-rc.5`. The raw string is then shown in the update notification (`iOS/Alerts.swift:195-198`).
- Why it matters: whoever controls the Casa-NUA repo or 6529.io can trigger false "signed", "missed" or "new version" alerts and place free text in a notification. No link is opened and nothing is executed.
- Fix: strict version regex; verify the releases signature; optionally cross-check a second source.

**7. Low — Malformed remote integers can trap the app, and the bad file is cached first**
- Evidence:
  - No response-size cap in `Shared/Feed.swift:54-62`.
  - Unchecked arithmetic on remote `day`: `iOS/Alerts.swift:117` (`s.day + 1`), `:207` (`s.day - 10`), `Shared/History.swift:57,86`.
  - The cache is written before these run (`Shared/Store.swift:118` precedes `:122-123`).
- Why it matters: an extreme `day` value from the source could crash the app or widget, and may repeat from the cached copy. This is from code reading; I did not test it. It requires the publisher's own source to be corrupted or compromised.
- Fix: range-check `day` and timestamps on decode; cap the response size; use overflow-safe arithmetic.

**8. Low — Alert wording states single-node observations as fact; relay wording understates what is stored**
- Evidence:
  - "Your validator signed %@." and "%@ ended without your validator's signature." (`Shared/Strings.swift:140,144`). Notifications carry no "unofficial" label, unlike the screens (`:97,99`).
  - `pushPrivacy` says the relay "keeps only" the push address and wallet (`Shared/Strings.swift:135`). The Worker also keeps alert switches, APNs environment, the seen-day list (`push/worker.js:61-67`) and the marks from Finding 2.
  - The store description advertises "Optional instant alerts" (`appstore/metadata/en-US/description.txt`, PRIVACY paragraph), which 1.0 cannot show (`project.yml:28`).
- I found no "verified" or "official" overclaim in the English or Portuguese tables. Sourcing is disclosed (`Shared/Strings.swift:223`: "It is not an official 6529 source"), and self-declared locations are flagged (`:183`). The reminder texts are carefully hedged (`:137-138`). The other ten language tables were not read.
- Fix: hedge the missed and signed alerts ("the source reports…"); list everything the relay stores; drop the instant-alerts sentence until the relay ships.

**9. Info — Subscription state is a plain date in App Group defaults**
- Evidence: `Shared/Plan.swift:45-50`. On iPhone the `plan.phone.until.v1` key is honoured (`:47`) but never written or cleared there.
- Why it matters: someone who edits a backup or jailbreaks could unlock features. This is a revenue risk for the publisher, not a user risk. StoreKit verification itself is correct: only `.verified`, non-revoked transactions for the product count (`Shared/Purchases.swift:48-56`).
- Fix: ignore the phone key on iOS.

**10. Info — The unlock flag exists but is off for release**
- Evidence:
  - `Shared/Plan.swift:42-46` reads `MonitorUnlockAll`.
  - Default is `UNLOCK_ALL: "NO"` (`project.yml:34`).
  - `YES` is set only by `install-personal.sh:19`, under a different bundle id (`:16`).
  - `testflight.sh:46-53` refuses to upload an archive carrying `YES`.
  - `Info.plist` is covered by the code signature, so it cannot be flipped after signing.
- A visible "Test build" banner shows when it is on (`iOS/PaywallView.swift:29-30`). I could not inspect the submitted binary.
- Fix: compile the flag out with `#if DEBUG` or a build condition instead of an Info.plist key.

**11. Info — Debug launch-argument hooks ship in release**
- Evidence: `iOS/MonitorApp.swift:86` (`widgetGallery`), `iOS/TodayView.swift:11` (`sheet`), `Watch/WatchTodayView.swift:16,20`.
- Why it matters: not reachable by users on stock devices, and the paid logic is still gated in code (`iOS/Alerts.swift:67`, `Shared/Store.swift:87`).
- Fix: wrap in `#if DEBUG`.

**12. Info — Handle input is safely encoded, with minor gaps**
- Evidence: `Shared/Feed.swift:75-77` percent-encodes everything outside `[A-Za-z0-9-_.]` and appends to a fixed host, so the path and host cannot be altered.
- Gaps: `.` and `..` pass, and there is no length limit. The worst case is a GET to another public path on the same host, which then decodes to "not found". A 42-character `0x…` input is added as a wallet without a hex check (`:83-85`); it is only compared locally.
- Fix: validate against `^(0x[0-9a-fA-F]{40}|[A-Za-z0-9_]{1,N})$` (cap N at 6529's handle limit).

**13. Info — Requests use sessions with default cookie and cache storage**
- Evidence: `Shared/Feed.swift:47-52`, `iOS/PushRelay.swift:133`. RemoteTexts correctly uses an ephemeral session (`Shared/RemoteTexts.swift:127`).
- Why it matters: a cookie set by api.6529.io or GitHub would persist and be re-sent, and lookup responses may sit in the on-disk cache. No `Set-Cookie` was seen in today's responses.
- Fix: use an ephemeral configuration with no cookie storage.

**14. Info — Identifiers and fixtures in the repo, no secrets**
- Evidence:
  - `testflight.sh:16-19` contains the Apple Team ID, the App Store Connect Key ID and Issuer ID, and the path to the key file. The key file itself is outside the repo.
  - `push/wrangler.toml:27` repeats the Team ID in a comment.
  - `Shared/Store.swift:137-140` and `Shared/WidgetShared.swift:31,38` hard-code the developer's own public handle and wallets as preview data, shipped in the binary.
- No private key, token or password was found in the tree or in history. The two "BEGIN PRIVATE KEY" hits in history are a test that generates a key at run time and a comment.
- Fix: none required; optionally move the IDs to environment variables.

**15. Info — Entitlements and permissions**
- No usage-description keys, no ATS exceptions, all URLs HTTPS, no URL scheme, universal links or `onOpenURL`.
- Notification permission is asked only for subscribers (`iOS/TodayView.swift:81-85`) or from the alerts screen.
- `UIBackgroundModes: fetch` is used (`iOS/MonitorApp.swift:72-76,108-112`). The App Group is used. The time-sensitive entitlement is used (`iOS/Alerts.swift:128,173`).
- `aps-environment` is shipped (`project.yml:72`) but unused in 1.0, because the relay is off.
- The Siri intent takes no parameters, is read-only and is gated (`iOS/Intents.swift:12-25`).

**Local storage (item 3, no finding)**
- No Keychain use. Everything sits in App Group UserDefaults:
  - followed identity (typed input, handle, wallets) and extra accounts (`Shared/Store.swift:17-18`);
  - per-wallet 60-day history (`Shared/History.swift:29`);
  - alert switches and shown-keys (`iOS/Alerts.swift:36,39`);
  - relay on/off, token, sync and leave state (`iOS/PushRelay.swift:43-46`);
  - plan end dates (`Shared/Plan.swift:36-37`);
  - the remote texts file (`Shared/RemoteTexts.swift:49-50`).
- The file `day-cache.v1.json` holds public data only (`Shared/Feed.swift:145-148`).
- The Watch receives only the identity and the plan end date, through WatchConnectivity (`iOS/PhoneSync.swift:39-44`).
- Nothing secret is stored; all of it would be in device backups.

## ENDPOINT TABLE

| Endpoint | Sent | Purpose |
|---|---|---|
| `GET raw.githubusercontent.com/Casa-NUA/6529-network-data/main/docs/data/network.json`, `validators.json`, `cycles/<day>.json` | Nothing about the user. `<day>` comes from `network.json`. Standard headers only (IP, User-Agent with app name and build, Accept-Language). | The day's data. Used by the app, widgets, Watch, complications and Siri (`Shared/Feed.swift:101-121`). The whole list is downloaded and matched on device. |
| `GET 6529.io/validator/releases.json` | Nothing about the user. | Latest validator version (`Shared/Feed.swift:103`). |
| `GET api.6529.io/api/profiles/<handle or 0x address>` | The handle or address typed, in the path. Only at setup and add-account (`Shared/Store.swift:51,83`), not on refresh. | Resolve to consolidation wallets. Returns the public profile; the app keeps only handle and wallets. |
| `GET nua-published-curations.casanua.workers.dev/site/monitor-texts.json` | Nothing; no query or body. At most hourly, from the iPhone and Watch apps. | Replacement wording. The publisher's own server. |
| `POST <PUSH_RELAY_URL>/v1/subscribe` — **not configured in 1.0** | `{token, wallet, env, kinds, day, shown}`. No handle, language or time zone in the body. The Worker does not read or store headers or IP, and logging is off (`push/wrangler.toml:21-22`). | Instant alerts. Stored 7 days from last renewal. |
| `POST <PUSH_RELAY_URL>/v1/unsubscribe` — **not configured in 1.0** | `{token}`. Retried until the Worker answers 204 (`iOS/PushRelay.swift:80-83`). | Delete the subscription (see Finding 2). |
| Apple: StoreKit, APNs, WatchConnectivity | Handled by the OS. | Purchase state, pushes, phone-to-Watch sync. |
| Opened in the browser on tap: `apple.com/legal/…/stdeula/`, `app.nua.casa/?app=monitor#privacy`, `app.nua.casa` | Nothing from the app. | Terms, privacy policy, publisher's other apps. |
| Worker, server-side every 5 minutes | Reads the same GitHub files and `releases.json`; posts to `api.push.apple.com/3/device/<token>` with localisation keys only (`push/worker.js:226-246`). | Send alerts. |

## NOT CHECKED / LIMITS

- **The submitted binary.** No App Store archive is on disk, so I could not confirm that 1.0 was built from `03e128d` with `UNLOCK_ALL=NO` and an empty `PUSH_RELAY_URL`. This rests on `project.yml` and git history.
- **The App Store Connect privacy label and published policy.** I read only the draft in `docs/privacy`.
- **The texts Worker** (`nua-published-curations`). Its source, logging and access control are not in this repo.
- **Whether the push Worker is deployed.** Its Cloudflare plan, KV access list and actual subrequest and write limits are unknown; the abort-and-repeat scenario in Finding 3 is unverified reasoning.
- **Server-side logging** by GitHub, 6529.io and api.6529.io.
- **Runtime behaviour.** Nothing was built or run: no traffic capture, no crash reproduction for Finding 7, no check of what URLSession actually writes to disk.
- **The data collector** and the accuracy of the data itself.
- **The Android app** that the remote-texts comments mention.