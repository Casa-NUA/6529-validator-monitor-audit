SCOPE CHECKED

- **A (web page).** Source at NUA-frame origin/main `24d0d72`, under `<temp>/nuaframe-audit`: `web/app-nua-casa/monitor-site-embed.html`, `monitor-site-loader-embed.html`, `site-texts.mjs`, `workers/published-curations/worker.js`, `curations.js:473-523` (the common request path and `/site/*`), `wrangler.toml`.
- **A (live).** Six GETs: `https://app.nua.casa/`, `/site/monitor-site.js`, `/site/monitor-texts.json`, `/site/privacy.js`, one 404 probe and one Origin probe. The live `monitor-site.js` is byte-identical to the repo bundle (149,467 chars, sha256 `52639288…af6148`). The live JSON is identical to the repo file (`{"format":1,"texts":{}}`).
- **B (remote texts).** Server side as above, plus the iOS consumer `Shared/RemoteTexts.swift` in `<project>/6529-validator-monitor-features` (branch `claude/rc16-chain`, `03e128d`).
- **C (push server).** `<project>/6529-validator-monitor-features/push/` (`worker.js`, `wrangler.toml`, `README.md`, `test.mjs`), plus the client `iOS/PushRelay.swift` and `project.yml`.
- **Claims.** App source on that branch (`Plan.swift`, `TodayView.swift`, `Alerts.swift`, `Intents.swift`, `Store.swift`, `WidgetShared.swift`, `appstore/`), and public GETs to the GitHub API and raw files for `Casa-NUA/6529-network-data`.

CONFIRMED CLAIMS

1. **"No wallet to connect. No keys. Nothing to sign." → confirmed at source level (iOS).** No wallet-connect, signing or key-handling code was found in the Swift sources. The only "wallet" handled is a public address the user types (`PushRelay.swift:116`).
2. **"An independent app. Read-only." → confirmed, with one nuance.** The app only reads public data. The one write path is the optional push relay (POST of device token and wallet), which is not deployed. "Independent" was not verifiable.
3. **Data-source paragraph → partly.**
   - The repo is public and contains a `collector/` directory and `docs/data/*.json`, so "see the code and the data" holds.
   - "Every 10 minutes" is the nominal cadence only. In the 8 hours before 12:26Z on 2026-11-14, most commits were about 10 minutes apart, but there were gaps of 20, 30, 41 and 60 minutes (for example 11:25Z to 12:25Z).
   - "A 6529 Validator node run by Casa NUA" and "anyone running a node can check it" were not verifiable from here.
4. **"US$2/month" → partly.**
   - The base price is US$2.00 (`StoreKit/Monitor.storekit:32`, `appstore/subscription.json:4`).
   - The publisher's own plan sets US$1 in 68 storefronts and other half prices in 8 more (`appstore/README.md:35-39`, `appstore/accessible-prices.csv`). The page shows US$2 to everyone outside the Brazil rule, so it overstates the price for those visitors.
   - Actual App Store Connect prices were not checked.
5. **"R$5 only for Brazilian time zone + pt browser" → confirmed, with an override.** The logic is at `monitor-site-embed.html:131-132`, and R$5 matches `accessible-prices.csv:70`. A `?br=1` or `?br=0` query parameter overrides it for anyone (line 133).
6. **Platform list "iPhone · Apple Watch · Android" → partly.**
   - `LINKS.googlePlay` and `LINKS.appStore` are empty (line 7).
   - The store block honestly says "Public beta on TestFlight" and "Coming soon / Google Play".
   - The hero line lists Android with no qualifier, although Android is not obtainable.
7. **Free plan (the day, network map, glossary) → confirmed.** Network and glossary open without the plan check (`iOS/TodayView.swift:98-103`). The header comment at `Shared/Plan.swift:3-6` says the network overview is paid, which is stale and contradicts the code.
8. **Subscription features → confirmed in source.**
   - Widgets: `Shared/WidgetShared.swift:57`
   - Watch: `Watch/WatchTodayView.swift:22`
   - Alerts: `iOS/Alerts.swift:67`
   - Siri: `iOS/Intents.swift:15`
   - 5 accounts: `Shared/Plan.swift:24`, `Shared/Store.swift:87`
   - Countdown and history: `iOS/TodayView.swift:263,286`
   - The TestFlight binary itself was not checked.
9. **"Scheduled on your phone ahead of time" (alerts) → confirmed for current builds.** The relay URL is empty (`project.yml:28`), so alerts are local.
10. **Page figures (173 accounts, 96.4%, 134 of 185) are a hard-coded snapshot dated "Tue 10 Nov".** They are not live data. The page labels the date but not that it is an illustration.

FINDINGS

**1. Medium — Remote texts have no integrity protection beyond HTTPS**
- **Evidence:**
  - `curations.js:486-492` serves `monitor-texts.json` from the Worker bundle with no signature.
  - `RemoteTexts.swift:60-77` accepts any text of 1,000 characters or fewer, for any key except `planLegal`.
  - The app fetches it hourly (`RemoteTexts.swift:47`).
- **Why it matters:** Whoever controls the Worker deploy, the Cloudflare account or the `casanua.workers.dev` name can rewrite almost every on-screen sentence without App Review. That includes paywall wording around the price (`planBuy`), alert texts and the glossary. For example, they could show instructions sending validator operators to a hostile site.
- **Mitigations present:**
  - Plain text only; there is no markdown or link rendering, and link targets are hard-coded.
  - Format arguments are strictly validated (`RemoteTexts.swift:86-116`).
  - 512 KB cap, ephemeral session, applied at next launch, and a 404 restores the built-in texts.
  - Response is `application/json` with `nosniff` and `max-age=300`.
- **Suggested fix:** Sign the file (Ed25519, public key in the app) and reject unsigned files. Consider excluding paywall keys.

**2. Medium (design; not deployed) — Push relay accepts unauthenticated registrations that can starve or spam real users**
- **Evidence:**
  - `push/worker.js:50-70`: no auth, no attestation, no rate limit (acknowledged at `README.md:25-26`).
  - Each fake token costs one of only 35 pushes per run (`worker.js:44,186-191`) before it is deleted, and it can be re-registered indefinitely.
  - Every subscribe is a KV write. If the KV daily write quota is exhausted, the "already sent" marks at `worker.js:210-211` fail to persist. Alerts would then be re-sent every 5 minutes, and new subscribes would error.
  - While the relay is active, the iPhone stops scheduling its own alerts (`iOS/Alerts.swift:78`, `PushRelay.swift:8-9`).
- **Why it matters:** An anonymous script could make subscribers miss "not signed yet" reminders or receive repeated time-sensitive alerts. The quota part assumes the Cloudflare free plan, which `worker.js:42` states as a premise.
- **Suggested fix:**
  - Require App Attest (the sibling Worker already has `app-gate`).
  - Add a Cloudflare rate-limit binding; it does not require storing IPs.
  - Persist marks before sending, or per token.
  - Keep local reminders as a fallback.

**3. Low — Device tokens outlive "delete" in the sent-marks**
- **Evidence:**
  - `worker.js:71-73` deletes only `sub:<token>`.
  - Tokens remain inside `marks:<day>` for 3 days (`:33,210`) and `marks:update:<version>` for 60 days (`:40,211`). The 60-day TTL restarts on every rewrite.
  - `README.md:10` and the live privacy text say switching off "deletes the entry at once".
- **Why it matters:** The token, plus which alert kinds fired for it (for example "missed"), stays on the server after opt-out. The wallet does not.
- **Suggested fix:** Key marks by a salted hash of the token, or purge them on unsubscribe, and state the retention in the policy.

**4. Low — Third-party version string flows unvalidated into notifications and a KV key**
- **Evidence:**
  - `worker.js:101-108`: `vnum` checks only the numeric core. Anything after "-" passes.
  - `latest.s` becomes a `loc-args` value (`:202,228`) and part of the KV key `marks:update:${latest.s}` (`:145`).
- **Why it matters:** Whoever controls `6529.io/validator/releases.json` could put arbitrary text in the "new version" push. An over-long string would throw at the KV read and halt all alerts.
- **Suggested fix:** Accept only `^\d+\.\d+\.\d+(-rc\.\d+)?$` and cap the length.

**5. Low — Input bounds and error handling in subscribe**
- **Evidence:**
  - `worker.js:54,64,66`: no body-size cap, and the `kinds` and `shown` arrays are filtered but not de-duplicated or capped.
  - Oversized metadata makes `SUBS.put` throw uncaught (`:68`), producing a Cloudflare 500 page. No stack is exposed.
- **Why it matters:** Robustness only.
- **Suggested fix:** Cap and de-duplicate the arrays, wrap in try/catch, and return 400.

**6. Low — Anyone holding a device token can overwrite or delete its subscription**
- **Evidence:** `worker.js:58-73`.
- **Why it matters:** Tokens are random and not exposed by any endpoint, so this needs prior token knowledge. Enumeration is not possible: there is no read route, all valid requests return 204, and there are no CORS headers. An outsider cannot learn which accounts a device watches.
- **Suggested fix:** Have the server issue a per-subscription secret.

**7. Low — The page script has no integrity pin and runs with full page access**
- **Evidence:**
  - `monitor-site-loader-embed.html:3` loads the script with no SRI. It cannot use SRI by design, since the content is meant to change.
  - `https://app.nua.casa/` sends only `content-security-policy: frame-ancestors https://carrd.com *`. It sends no HSTS, `X-Content-Type-Options` or `Referrer-Policy`.
  - The same page also runs `privacy.js`, `terms.js`, `support.js` and `get.js` from the same Worker.
- **Why it matters:** Control of the Worker, the Cloudflare account or the workers.dev subdomain is control of the page, including the TestFlight link. The page can also be framed by any site.
- **Suggested fix:** Serve from a custom domain you own, add a CSP with a `script-src` allow-list and HSTS where Carrd allows, and limit who can deploy.

**8. Low — Google Fonts is a third-party request**
- **Evidence:** `monitor-site-embed.html:9-12` loads `fonts.googleapis.com`, and the Carrd page loads it too. `privacy.js` has no Google Fonts or cookie disclosure for the website (its only `fonts.googleapis` occurrence is the bundle's own font link).
- **Why it matters:** The visitor's IP address and referrer reach Google.
- **Suggested fix:** Self-host the two fonts, or disclose it.

**9. Low — Marketing overclaims**
- **Evidence:**
  - Android is listed as a platform but is unavailable (`monitor-site-embed.html:7,24`).
  - "Every 10 minutes" (see Claim 3).
  - US$2 is shown where the plan is US$1 (see Claim 4).
  - The sample figures are not marked as an example.
- **Suggested fix:** "Android (coming soon)", "about every 10 minutes", "from US$2 / price varies by country", and "example day".

**10. Info — Push relay is not deployed, as far as can be observed**
- `push/wrangler.toml:15` still has `id = "REPLACE_WITH_KV_NAMESPACE_ID"`.
- `project.yml:28` has `PUSH_RELAY_URL: ""`, so the switch is hidden (`PushRelay.swift:40`).
- `push/` is absent from origin/main.
- `https://monitor6529-push.casanua.workers.dev/` returns the same Cloudflare "error code: 1042" as a made-up control name.
- Deployment under another name or account cannot be excluded.
- The live privacy policy already describes the service.

**11. Info — Sharing the Worker**
- `/site/*` is answered before the app gate and rate limiters (`curations.js:486-493`). It is a static import, and no runtime route can alter it.
- No logging is configured (`workers/published-curations/wrangler.toml:13-24`). Errors are generic (`:518-519`).
- The risk is coupling only. Every NUA-app deploy re-ships the Monitor page and texts, so the Monitor inherits that Worker's deploy and secret exposure.
- `/site/*` has no rate limit. The 404 response lacks `nosniff`.

**12. Info — DOM, storage and links**
- `innerHTML` receives only built-in constants (`monitor-site-embed.html:16,147-158`). No URL or user data reaches it; `lang` and `br` are compared to literals.
- No fetch/XHR, cookies or analytics. `localStorage` holds only `vm6529-lang` (lines 122, 272). Images are embedded data URIs.
- If embedded on a hostile page, the script only renders public content.
- External links (TestFlight, GitHub, privacy) use `target="_blank" rel="noopener"` without `noreferrer` (lines 16, 156).
- `app.nua.casa` sets Cloudflare's `__cf_bm` cookie (30 minutes).
- Live script headers: `application/javascript; charset=utf-8`, `cache-control: public, max-age=300`, `access-control-allow-origin: *`, `x-content-type-options: nosniff`. `ACAO *` is harmless for public static content.

**13. Info — Secrets and logging (push relay)**
- No secrets are in `push/`. The APNs key, key ID and team ID are Wrangler secrets (`wrangler.toml:24-27`). `test.mjs` generates a throwaway key.
- The APNs provider JWT is cached in KV in clear for about 45 minutes (`worker.js:271`).
- `observability.enabled = false` (`wrangler.toml:21-22`), but it is less explicit than the sibling Worker (no `logpush = false` or `invocation_logs = false`).
- There are no `console.*` calls.

DATA HELD BY THE PUBLISHER'S SERVERS

- **Site/texts Worker (`/site/*`):** nothing is stored per visitor by the code, and logging is disabled in config. Cloudflare necessarily processes the IP address and user agent in transit.
- **Carrd / app.nua.casa:** Cloudflare's `__cf_bm` cookie. Whatever Carrd itself logs is unknown.
- **Google (third party):** IP address and referrer via Fonts.
- **Push relay, if ever deployed:**
  - Per device: APNs token, followed validator wallet (one), APNs environment, enabled alert kinds, and day number plus alerts already shown. TTL is 7 days, renewed daily; the entry is deleted on unsubscribe or an APNs "gone" answer.
  - Tokens also sit in the sent-marks (3 days, and 60+ days).
  - The APNs provider JWT.
  - An entry's existence implies an active subscription.
  - Language and time zone are not stored; the device localises the alerts.
  - No IP address, handle or name.
- **Purchases:** none server-side (StoreKit on the device).

NOT CHECKED / LIMITS

- Cloudflare dashboard state: actual log settings, account access, who can deploy, and plan or quota.
- App Store Connect prices.
- The shipped TestFlight binary.
- Android's `RemoteTexts.kt`; it exists on `origin/claude/rc16-chain-android` but was not reviewed.
- The NUA-app routes (`push.js`, `admin.js`, `siwt.js`), the gate internals and the collector code.
- The node-verifiability claim.
- No POSTs were sent, so push-endpoint behaviour is from source reading only. `test.mjs` was not run.
- The workers.dev/1042 deployment inference is indirect.
- The KV quota scenario in Finding 2 depends on plan limits that were not verified.