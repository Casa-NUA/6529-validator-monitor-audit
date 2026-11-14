**SCOPE CHECKED**

Reviewer note: I am not a lawyer; this is a practical review, not legal advice. Everything was read-only.

- **Privacy policy:** live bundle `https://nua-published-curations.casanua.workers.dev/site/privacy.js` (fetched 14 Nov 2026). Its Monitor block is byte-identical to `web/app-nua-casa/privacy-monitor.js` in the NUA-frame checkout. Also read `privacy-apps.js`, `privacy-embed-1..5.html` (NUA app only) and `docs/privacy/monitor-privacy.js` in the features repo.
- **Terms and support:** `web/app-nua-casa/terms/terms-en.md`, `README-terms.txt`, `support-embed.html`, and the live `terms.js` / `support.js`.
- **iOS (features repo, branch `claude/rc16-chain`, HEAD 03e128d = build 7 / 1.0):** `Shared/Feed.swift`, `RemoteTexts.swift`, `Plan.swift`, `Purchases.swift`, `Store.swift`, `Strings.swift`, `PrivacyInfo.xcprivacy`, `iOS/PushRelay.swift`, `PaywallView.swift`, `TodayView.swift`, `project.yml`, `push/worker.js`, `push/wrangler.toml`, `push/README.md`, `appstore/*`.
- **Android (branch `claude/rc16-chain-android`, HEAD 708767c):** `core/.../Feed.kt`, `Plan.kt`, `RemoteTexts.kt`, `StringsTable.kt`, `StringsAndroid.kt`, `app/.../ui/PaywallSheet.kt`, `Purchases.kt`, manifests, Gradle dependencies, `store/listing/*`.
- **Other:** the texts Worker's `wrangler.toml`, `monitor-site-embed.html`, and the project handoff notes for what was entered in App Store Connect and Play Console.

**WHAT IS GOOD**

- **Network calls match the policy.** The only hosts in code are raw.githubusercontent.com, api.6529.io, 6529.io (`Shared/Feed.swift:33-35`, `core/Feed.kt:33-35`) and the texts Worker (`project.yml:30`, `RemoteTexts.kt:44`). The policy names all four.
- **No trackers.** There are no analytics, ads or crash SDKs. iOS has no third-party packages; Android has only AndroidX, Play Billing and play-services-wearable (`app/build.gradle.kts:81-95`).
- **Only the profile lookup carries user input.** The validator list is downloaded whole and matched on the device (`Feed.swift:92-94`). The app says so at setup: "Your handle is looked up on api.6529.io. The validator list is downloaded whole and searched on this device." (`Strings.swift:105`).
- **The texts request carries nothing personal.** It is a plain GET with an ephemeral session (`RemoteTexts.swift:125-127`). The Worker has logging switched off (`workers/published-curations/wrangler.toml:14-24`).
- **Android limits what is kept.** `allowBackup="false"` on phone and watch; permissions are only INTERNET and POST_NOTIFICATIONS.
- **The subscription legal text cannot be changed remotely.** `static let refused: Set<Key> = [.planLegal]` (`RemoteTexts.swift:43`, `RemoteTexts.kt:46`).
- **The iOS paywall shows the essentials.** Price from the store (`PaywallView.swift:35`, "Subscribe for %@ a month"), renewal and cancellation text, restore, Terms and Privacy links (`PaywallView.swift:49-56`).
- **Independence is stated.** Both store descriptions say "This app is independent and is not made by 6529." The glossary says "It is not an official 6529 source." (`Strings.swift:223`).
- **Limits are stated.** "Each validator declares its own location; nobody checks it." (`Strings.swift:176`); alert delay caveats (`Strings.swift:161`); a "Source stopped" alert; the single collector node is disclosed in the listing ("collects every 10 minutes from one validator node").
- **No earnings promises found.** The App Review note says the app "shows no prices".

**FINDINGS**

1. **High — No Terms of Use (EULA) link in the App Store description.**
   - Evidence: `appstore/metadata/en-US/description.txt` ends with "The subscription renews every month until cancelled. Payment is charged to your Apple ID; cancel at any time…" and contains no URL. A search for "stdeula|Terms|EULA" finds nothing in any of the 13 locale descriptions.
   - Why it matters: Apple's subscription rules (3.1.2 / Schedule 2) require a working Terms of Use link in the store metadata as well as in the app. With Apple's standard EULA, the link goes in the description. This is a common rejection reason.
   - Limit: I could not read App Store Connect; these files were last changed before the submission.
   - Fix: add to every locale's description: "Terms of Use (EULA): https://www.apple.com/legal/internet-services/itunes/dev/stdeula/ · Privacy Policy: https://app.nua.casa/?app=monitor#privacy".

2. **High (Google Play) — The Android paywall has no privacy policy link, and its Terms link is Google's own.**
   - Evidence: `core/.../Plan.kt:26-27`: "null until Casa NUA publishes one (NOT enough to ship)" / `val privacyURL: String? = null`. `PaywallSheet.kt:76` shows the link only when it is non-null. `PaywallSheet.kt:40`: `termsURL = "https://play.google.com/about/play-terms/"`.
   - Why it matters: Google's User Data policy asks for the privacy policy in the Console and inside the app. The Play Terms of Service is the user's contract with Google, not terms from Casa NUA.
   - Limit: this is branch HEAD; the binary in Play review may differ.
   - Fix: set `privacyURL` to the live address, as iOS does (`Shared/Plan.swift:34`), and point Terms at Casa NUA terms that cover the Monitor.

3. **Medium — No Casa NUA terms cover the Monitor.**
   - Evidence: `terms/terms-en.md:12-19` scopes the Terms to "the NUA app for iPhone, iPad and Apple TV… (together, 'NUA')". The live `terms.js` has no "Validator Monitor". `PaywallView.swift:13`: "PREMISE: the app uses Apple's, not its own."
   - Why it matters: Apple's standard EULA is acceptable to Apple. But nowhere are there Monitor-specific terms on data accuracy and availability, the single-node dependence, the non-affiliation, governing law, refunds, or the supplier's identity. Brazilian e-commerce rules (Decreto 7.962/2013) expect the supplier's name, CNPJ and address on the offer. Android users get no developer terms at all.
   - Fix: publish short Monitor terms at `app.nua.casa/?app=monitor#terms` using the same selector, or extend §1.2 of the NUA terms. Include: "Data comes from an unofficial public record collected by one node; it may be late, incomplete or wrong. Do not rely on the app as your only check that your validator signed."

4. **Medium — The policy does not identify the controller or cover LGPD/GDPR/CCPA basics.**
   - Evidence: `privacy-monitor.js:33`: "Questions about this policy: {MAIL}" (bugs@nua.casa) is the only identification. The Monitor text has zero occurrences of CNPJ, LGPD, GDPR, California, controller or encarregado. The legal name and CNPJ exist only in the NUA terms (`terms-en.md:10`).
   - Missing items:
     - legal name, CNPJ and address;
     - legal bases (LGPD art. 7 / GDPR art. 6);
     - rights and how to exercise them (LGPD art. 18, including complaint to the ANPD; GDPR arts. 15-22);
     - international transfer (Cloudflare, GitHub, Apple, Google, 6529; LGPD art. 33);
     - encarregado contact (LGPD art. 41, or a statement relying on the small-agent waiver);
     - a California "we do not sell or share personal information" line.
   - Flag, not resolved: whether an EU/UK Art. 27 representative is needed for a paid app offered in all countries.
   - Fix: add a "Who we are and your rights" section, for example: "Controller: CASA NUA MUSEU DE ARTE EM REDE LTDA, CNPJ 19.035.440/0001-02, [address]. Privacy contact: [e-mail]. We do not sell or share personal information. You may ask for access, correction or deletion; for instant alerts, switching them off deletes your entry."

5. **Medium — The Support URL leads to a page about the other app.**
   - Evidence: the handoff `1-testflight-asc.md:5` records "Support URL https://app.nua.casa/#support (13 locales)". The live page's lead is "Help with NUA on iPhone, iPad, Android and your TVs." (`support-embed.html`, T.en) and has no mention of "Monitor" or "6529". It points to an in-app "Send an error report" link the Monitor does not have. The Monitor has no in-app contact; its menu holds only Refresh, Alerts, Network, Glossary, add account, Subscribe, Casa NUA apps and Change account (`TodayView.swift:97-116`).
   - Why it matters: the contact address bugs@nua.casa is there, so Apple's minimum (1.5) is probably met. A Monitor subscriber still lands on TV-curation help.
   - Fix: add a Monitor section to the page (contact, how to cancel, refunds through Apple/Google, "data looks old") and a "Support" item in the app menu.

6. **Medium (when the relay ships) — The instant-alerts retention statement is not exact.**
   - Evidence, policy `privacy-monitor.js:23`: "keeps exactly this: your iPhone's notification token…, the public wallet…, which alert switches are on, and which of today's alerts your iPhone already showed… Turning instant alerts off deletes the entry at once".
   - Evidence, code: unsubscribe deletes only `sub:<token>` (`push/worker.js:72`). The Worker also writes `marks:<day>` keyed by `${sub.token}:${kind}` (`worker.js:179, 210`, kept 3 days, `DAY_TTL` line 33) and `marks:update:<version>` keyed by the token (`worker.js:198, 211`, kept 60 days, `UPDATE_TTL` line 40). It also stores the APNs environment (`worker.js:63`).
   - Why it matters: after switch-off, the bare token (without the wallet) can stay up to 60 days. That contradicts "exactly this" and "deletes at once". The same gap is in the app text (`Strings.swift:135`) and the App Store description ("switching them off deletes both").
   - Status: not live in 1.0, because `project.yml:28` has `PUSH_RELAY_URL: ""` and `wrangler.toml:15` still has a placeholder ID.
   - Fix: remove the token from the marks on unsubscribe, or key the marks by a hash; otherwise disclose "a record of which alerts were sent to a token, kept up to 60 days".

7. **Medium — The policy exists only in English and Portuguese; the app ships in 12 languages and the stores in 13 locales.**
   - Evidence: `MONITOR_PRIVACY_T` has only `en` and `pt` (`privacy-monitor.js:12, 35`). The selector falls back to English (`privacy-apps.js:67`). The terms are Apple's English EULA; the NUA terms are English and Portuguese.
   - Why it matters: no store requires translation. But GDPR transparency expects information the targeted audience can understand, and the listing is localized into German, French, Italian and Spanish. The app README also says the translations are machine-made and not reviewed by native speakers, which is risky for legal subscription text.
   - Fix: add at least es/fr/de/it policy versions, or state that English prevails; have `planLegal` reviewed natively.

8. **Low — The paywall does not show the subscription's name, and the name differs between sources.**
   - Evidence: the screen title is "Subscription" (`Strings.swift:234`). The store display name is "Monthly" in `subscription.json:9` and the StoreKit file; the handoff calls the product "6529 Monitor Pro".
   - Why it matters: Apple asks for the subscription's title on the purchase screen. Length and price are shown.
   - Fix: show the store's `product.displayName` above the button and align the names.

9. **Low — The description mentions "Optional instant alerts" that build 7 cannot show.**
   - Evidence: en-US description, PRIVACY paragraph. The switch is hidden while the relay address is empty (`PushRelay.swift:40`, `project.yml:28`).
   - Why it matters: a reviewer or user cannot find the feature (metadata accuracy, 2.3.1). The same applies to the policy's iPhone alerts paragraph, where over-disclosure is harmless but should read "when available".

10. **Low — The website shows one price to people who will pay another.**
    - Evidence: `monitor-site-embed.html:130-138` shows "R$5" only when the timezone is Brazilian and the browser language starts with "pt"; everyone else sees "US$2 a month". The handoff records US$1 in 68 countries on Apple and other prices on Play. `subscription.json:5` still says `"price_brl": "10.00"`.
    - Why it matters: the in-app price comes from the store and is correct, and the website errs upward, so nobody is overcharged. A flat "US$2" is still inaccurate for many visitors. The lower regional price is a normal practice.
    - Fix: "US$2 a month in the United States; the price in your country is shown before you subscribe (R$5 in Brazil)."

11. **Low — No refund or withdrawal wording anywhere.**
    - Evidence: `planLegal` (`Strings.swift:249`, `StringsAndroid.kt:23`) covers renewal and cancellation only.
    - Why it matters: neither Apple nor Google strictly requires it. Brazil's 7-day right of withdrawal (CDC art. 49) and EU withdrawal rules are worth flagging.
    - Fix: "Refunds are handled by Apple / Google under their policies; this does not limit your legal rights."

12. **Low — The children statement sits oddly with the 4+ rating.**
    - Evidence: "The app is not directed to children under 13." (`privacy-monitor.js:31`); rating 4+ (`appstore/README.md`).
    - Why it matters: not a contradiction, but the text is US-centric. LGPD art. 14 and GDPR art. 8 use different ages.
    - Fix: "The app is not directed to children and we do not knowingly hold data about them."

13. **Low — The third-party picture is incomplete.**
    - The policy says requests "carry what you typed or public addresses" (`privacy-monitor.js:18`). Requests also carry default headers. On Android only `Accept` is set (`Feed.kt:43-47`), so the system User-Agent goes out, which normally includes device model and OS version.
    - The policy never names Apple's notification service, Siri processing, or iPhone device/iCloud backups of app data.
    - "It keeps no request log" is true of the Worker's configuration, but Cloudflare and GitHub still process IP addresses as infrastructure.
    - Fix: one sentence naming these.

14. **Low — The date in the policy is off by a day.**
    - Evidence: "From versions released after 13 November 2026, the app also downloads its descriptive texts" (`privacy-monitor.js:18`). The feature landed on 13 Nov 01:52 UTC (commit 6e48668) and is in builds 6 and 7, both uploaded on 13 November.
    - Fix: "from 13 November 2026".

15. **Low — The privacy manifest is stale.**
    - Evidence: `Shared/PrivacyInfo.xcprivacy:5-9` comment: "downloaded whole from 6529scan… Nothing reaches a server of ours — there is none." Both statements are now false (data source changed; texts Worker exists).
    - It declares UserDefaults reason CA92.1, while the app uses App Group defaults (`AppGroup.swift:28`), for which Apple lists 1C8F.1.
    - `NSPrivacyCollectedDataTypes` must be filled when the relay is switched on.

16. **Info — Remote texts can change privacy and subscription wording after review.**
    - Evidence: only `planLegal` is refused (`RemoteTexts.swift:43`). `setupPrivacy`, `pushPrivacy`, `planProse` and the `planFeat*` texts can be replaced.
    - The live file is currently empty (`{"format":1,"texts":{}}`).
    - Fix: add the privacy and plan keys to `refused`, or keep a public change log.

17. **Info — Forward-looking statements about 6529 are not attributed.**
    - Evidence: `Strings.swift:225`: "They earn its test currency, $fake6529… $fake6529 is deleted at the switch to $6529, planned for 1 January 2027."
    - Why it matters: it is descriptive, not a promise, and the app shows no awards. It still states third-party plans as fact.
    - Fix: add "according to 6529; test currency with no monetary value; dates may change".
    - The independence statement appears in the listings and website but not inside the app, which only says "unofficial" / "not an official 6529 source". Consider one line on the setup screen.

18. **Info — Versioning.**
    - The Monitor policy shows "Last updated: 13 November 2026". The NUA policy shows 10 November. The terms show "In force from 7 November 2026".
    - Policy changes are not announced: "this page changes with it and the date above is updated" (`privacy-monitor.js:32`). There is no archive or change log. The NUA terms promise 30 days' notice (`terms-en.md:217`); the Monitor has no equivalent.
    - `docs/privacy/monitor-privacy.js` in the features repo is an old draft ("Last updated: [[DATE]]", iPhone only, "NOT TO BE PUBLISHED"). Delete it or sync it.

**ANSWER TO (b)**

It depends, and for build 7 as checked the answer is yes. "Data Not Collected" is defensible for 1.0 because the relay address is empty, so no device token or wallet reaches Casa NUA. Handle lookups go straight from the device to api.6529.io and GitHub for that request only. The single Casa NUA request (the texts file) carries no identifier, and its Worker keeps no logs. Apple's definition excludes data that is not kept beyond serving the request, and purchases are handled by Apple. The label becomes wrong the day the relay goes live. Then declare Identifiers → Device ID and Other Data (the followed wallet), purpose App Functionality, not used for tracking. Declaring them as linked is the conservative choice the project's own README already plans. Also consider Purchases, since the policy itself says an entry "shows that the app on that iPhone is subscribed". For Google Play, Android has no relay, so Data safety can state no data collected and no data shared, data encrypted in transit, with the privacy policy URL filled. The handle sent to api.6529.io is a user-initiated transfer to the service the user is querying. A more cautious option is to declare it as "Other" data shared for app functionality.

**NOT CHECKED / LIMITS**

- Not legal advice; I am not a lawyer. Statute references are checklist pointers only.
- I could not read App Store Connect or Play Console. The ASC and Play facts (Support URL, product name, prices, label) come from repo files and handoff notes, not the live consoles.
- I did not inspect shipped binaries. Whether a push Worker is deployed anywhere is unknown; only the repo configuration was read.
- Only English and Portuguese strings were read in full. The other 10 languages were spot-checked for `planLegal` only.
- The privacy practices of 6529.io, GitHub, Cloudflare, Apple and Google were not examined.
- The Watch, widget, Siri and Wear code was checked by search only (no additional hosts found).
- The Carrd page was verified only as far as its sections exist and it loads the Worker bundles.
- No key or secret file was opened.