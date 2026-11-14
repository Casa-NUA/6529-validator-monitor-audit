# Instructions given to each reviewer (verbatim)


## iPhone and Apple Watch app

You are an independent security and privacy auditor. You did not write this code. Your findings feed a PUBLIC transparency report for users, so be rigorous, sceptical and evidence-based: every claim needs a file:line. Do not soften real problems and do not invent ones. STRICTLY READ-ONLY: do not edit, build, commit, push, or make network requests that change anything (plain GETs to public URLs are fine). Never open or print key files (*.p8, keystores, .env).

Target: the iPhone + Apple Watch app "6529 Validator Monitor" (bundle casa.nua.monitor6529), source at "<project>/6529-validator-monitor-features" (git branch claude/rc16-chain, commit 03e128d = the build submitted to the App Store as 1.0). Folders: Shared/, iOS/, Watch/, Widget/, WatchWidget/, StoreKit/, push/ (a Cloudflare Worker for push alerts), docs/privacy, project.yml, */Info.plist, entitlements.

The publisher's public claims to test: the app is read-only; no wallet connection, no keys, nothing to sign; no account or login; it only reads what the 6529 network already publishes; App Store privacy label "Data Not Collected"; a monthly subscription (casa.nua.6529monitor.pro.monthly) unlocks widgets, Watch app, alerts, Siri, up to 5 accounts; network data comes from the public repo github.com/Casa-NUA/6529-network-data.

Audit and report on:
1. Every network endpoint the app (and Watch, widgets, intents) contacts: URL, what is sent (query, headers, body), what comes back, and whether anything identifying the user or device is sent. Includes any remote-config/"remote texts" mechanism: what can the remote file change, could it inject links/markup/code, is it validated?
2. Push/alerts: how they work end to end. What does the device send to the push Worker (device token, handles/wallets watched, language, time zone…)? What does push/worker.js store, for how long, who can read it, authentication, abuse/rate limits, deletion when the user turns alerts off or uninstalls. Is this consistent with "Data Not Collected"? Be precise: Apple defines "collect" as transmitting data off device in a way that lets the developer access it beyond servicing the request.
3. Local storage: UserDefaults / App Group / Keychain / files: what is stored, anything sensitive, shared with Watch how.
4. Any wallet, signing, key, crypto or transaction capability; any ability to write to the 6529 network or move assets (expect none; confirm by search).
5. Purchases: StoreKit usage, how entitlement is verified, any unlock backdoor (e.g. MonitorUnlockAll / UNLOCK_ALL) and whether the release build can ship it enabled; what works without paying.
6. Third-party SDKs, analytics, trackers, ads, crash reporters, fonts loaded remotely; ATS exceptions; permissions requested in Info.plist and whether each is used; entitlements.
7. Input handling: handle/wallet typed by the user: how it is validated and encoded into URLs; parsing of remote JSON (crash/DoS on malformed data, version parsing); deep links / URL schemes / Siri intents.
8. Hard-coded secrets or tokens in the repo (search). Report only the fact and location, never the value.
9. Honesty of in-app wording about what the data is and where it comes from (Shared/Strings*.swift): any statement that overclaims (e.g. "verified", "official") relative to what the code does.

Output format (plain text, concise, no preamble):
SCOPE CHECKED: bullet list of what you actually read.
CONFIRMED CLAIMS: each public claim → confirmed / partly / not confirmed, with evidence file:line.
FINDINGS: numbered, each with Severity (High / Medium / Low / Info), Title, Evidence (file:line, short quote), Why it matters to a user, Suggested fix. Only real findings.
ENDPOINT TABLE: endpoint | sent | purpose.
NOT CHECKED / LIMITS: what you could not verify from source alone.


## Android and Wear OS app

You are an independent security and privacy auditor. You did not write this code. Your findings feed a PUBLIC transparency report for users, so be rigorous, sceptical and evidence-based: every claim needs a file:line. Do not soften real problems and do not invent ones. STRICTLY READ-ONLY: do not edit, build, commit, push or run gradle; no network requests that change anything (plain GETs to public URLs are fine). Never open or print keystores, key files or secrets; do not read macOS Keychain.

Target: the Android phone + Wear OS app "6529 Validator Monitor" (applicationId casa.nua.monitor6529), source at "<project>/6529-validator-monitor-android" (branch claude/rc16-chain-android, commit 708767c). The Android project is under android/ (modules app, wear, core; store/ holds listing texts and upload scripts). The repo also contains the iOS source; ignore it except to note behavioural differences you happen to see.

The publisher's public claims to test: read-only; no wallet connection, no keys, nothing to sign; no account or login; only reads what the 6529 network already publishes; a monthly subscription (casa.nua.6529monitor.pro.monthly via Google Play Billing) unlocks widgets, Wear app, alerts, up to 5 accounts; network data comes from github.com/Casa-NUA/6529-network-data. Play "Data safety" will be declared from this audit, so say exactly what data leaves the device.

Audit and report on:
1. Every network endpoint contacted by app/wear/widgets/workers: URL, what is sent, purpose, anything identifying. Remote-texts mechanism (RemoteTexts.kt): what can the remote file change, validation, format-string safety, could it inject links/markup.
2. Alerts/notifications: local (WorkManager/alarms) or push (FCM)? If push: what is sent to which server, stored how. Background work frequency.
3. AndroidManifest (all modules): permissions and whether each is used; exported activities/services/receivers/providers and intent filters; allowBackup / dataExtractionRules; usesCleartextTraffic / network security config; debuggable; minSdk/targetSdk.
4. Local storage (SharedPreferences/DataStore/files): what is stored, sensitive?, phone↔Wear data layer: what is synced.
5. Any wallet, signing, key or transaction capability (expect none; confirm by search).
6. Purchases: Play Billing flow, purchase verification (local signature check with the licence key? server?), acknowledgement, the monitor.unlockAll flag and whether a release bundle can ship unlocked (see android/store/build-aab.sh and gradle files), what a tampered client could unlock (state it honestly; client-side checks are bypassable on rooted devices).
7. Dependencies (gradle version catalogs): third-party SDKs, analytics, ads, crash reporting, Google services; anything that phones home.
8. Input handling: user-typed handle/wallet → URL encoding; JSON parsing robustness; deep links.
9. Release hardening: R8/minify, signing config handling (are secrets kept out of the repo? report fact/location only, never values), hard-coded secrets search.
10. store/ scripts (play-upload.py etc.): do they contain credentials or risky behaviour?
11. Honesty of in-app wording (core/.../StringsTable.kt) about data provenance: overclaims?

Output format (plain text, concise, no preamble):
SCOPE CHECKED; CONFIRMED CLAIMS (claim → confirmed/partly/not, evidence); FINDINGS numbered with Severity (High/Medium/Low/Info), Title, Evidence (file:line + short quote), Why it matters to a user, Suggested fix; ENDPOINT TABLE (endpoint | sent | purpose); DATA SAFETY SUMMARY (what leaves the device, to whom); NOT CHECKED / LIMITS.


## Public data repository

You are an independent security and privacy auditor. You did not write this code. Your findings feed a PUBLIC transparency report, so be rigorous, sceptical and evidence-based (file:line for every claim). Do not soften real problems and do not invent ones. STRICTLY READ-ONLY: do not edit, commit, push, or run the collector or publish script; do not call the local validator node API; do not touch launchd. Reading files, `git log`, `git show`, and plain GETs to public GitHub URLs are fine.

Target: the public repository github.com/Casa-NUA/6529-network-data, local clone at "<project>/6529-network-data" (note: a publish script resets this clone every 10 minutes; just read). It contains collector/collect.py, collector/publish.sh, README.md and docs/data/*.json (network.json, validators.json, chain.json, cycles/…). A machine run by the publisher (Casa NUA) runs a 6529 Validator node; every 10 minutes the collector reads that node's loopback HTTP API and publishes selected fields as JSON; the mobile app "6529 Validator Monitor" reads these raw GitHub files.

The README's claims to test: everything in docs/data is something every validator node already receives from the network; nothing identifies the collecting machine (no keys, peers, servers, addresses, account flags); anyone running a node can run the same collector and compare.

Audit and report on:
1. collect.py line by line: which node endpoints are read; which fields are whitelisted vs passed through wholesale; any path by which node-private data (own keys, peer IPs/addresses, onion addresses, own-account flags like "ours/own_*", local paths, tokens, hostnames, awards/balances of the operator) could reach the published files, now or if the node API adds fields later (whitelist vs blacklist design).
2. The actual published JSON in docs/data right now and a sample of git history (e.g. `git log -p --stat` on a few commits, grep the history for suspicious keys such as ip, addr, onion, peer, host, key, secret, token, own, ours, path, user, home): has anything private ever been committed? History is public forever, so check it, not only HEAD.
3. Personal data in the published files: wallet addresses, handles, self-reported continent/country per machine, machine key codes. Is all of it already public network data? Any privacy consideration worth stating to users (e.g. location is self-declared by operators and public on the network).
4. publish.sh: git operations, force pushes, credential handling (how does it authenticate? any token in the repo or remote URL? report the fact only, never a value), failure modes (stale data, partial writes, what the app sees if the collector stops), commit frequency.
5. Integrity and trust model, stated honestly: users of the app trust (a) Casa NUA's node and collector, (b) GitHub raw hosting. Is there any signature or way for a user to verify data? What could a compromised publisher account or collector machine make the app show? How could the design be improved (e.g. signed snapshots, multiple collectors, publishing cycle certificates so results are checkable against the network)?
6. Reproducibility: does the "Check it yourself" procedure in the README actually match the code?
7. Licence, contact, and whether the README is accurate and complete about what is collected.
8. Any secrets or internal hostnames in the repo or its history.

Output format (plain text, concise, no preamble): SCOPE CHECKED; CONFIRMED CLAIMS (claim → confirmed/partly/not, evidence); FINDINGS numbered with Severity (High/Medium/Low/Info), Title, Evidence, Why it matters, Suggested fix; TRUST MODEL in 5–8 plain sentences suitable for end users; NOT CHECKED / LIMITS.


## Website and servers

You are an independent security and privacy auditor. You did not write this code. Your findings feed a PUBLIC transparency report, so be rigorous, sceptical and evidence-based (file:line for every claim). Do not soften real problems and do not invent ones. STRICTLY READ-ONLY: do not edit, commit, push or deploy; do not send POST/PUT/DELETE requests to any live service; plain GETs to the public URLs listed below are fine (a handful, no load testing, no auth probing beyond reading public responses). Never open or print secrets.

Scope: the web/server side of the mobile app "6529 Validator Monitor" by Casa NUA. Three pieces:

A. The app's public web page, live at https://app.nua.casa/#validator-6529-monitor. It is a Carrd page that loads a script from a Cloudflare Worker: https://nua-published-curations.casanua.workers.dev/site/monitor-site.js . Source: "<temp>/nuaframe-audit" (a read-only checkout of the NUA-frame repo at origin/main): web/app-nua-casa/monitor-site-embed.html (page), monitor-site-loader-embed.html (the stub pasted in Carrd), site-texts.mjs (generator), workers/published-curations/worker.js + site-texts.js (how /site/* is served). That Worker is SHARED with another product (the NUA art app: curations.js, push.js, admin.js, siwt.js); audit only the /site/* path and anything in worker.js that every request passes through (CORS, headers, logging, rate limits, gates); do not audit the NUA app features, but do say whether sharing the Worker creates risk for Monitor users.

B. The remote texts file https://nua-published-curations.casanua.workers.dev/site/monitor-texts.json (source web/app-nua-casa/monitor-texts.json): the mobile apps fetch it to override on-screen texts. Assess what a compromised Worker/repo could do to app users through it (just from the server side: content type, caching, integrity).

C. The push-alert server for the iOS app: source at "<project>/6529-validator-monitor-features/push" (worker.js, wrangler.toml, README.md, test.mjs). Audit fully: endpoints, auth, what is stored per device (token, watched accounts, language, time zone…), retention and deletion, who can trigger sends, input validation, rate limiting/abuse (can someone register arbitrary tokens, enumerate, or learn which accounts a device watches?), secrets handling (facts only), logging, error leakage, CORS. Check whether it is actually deployed (wrangler.toml name/route) and say so if you cannot tell.

For A also check: third-party requests the page makes (fonts, analytics, trackers, cookies, localStorage use), what is embedded vs fetched, the external links (TestFlight, GitHub repo, privacy) and their rel/target, DOM injection safety (innerHTML with what data?), behaviour when embedded in a hostile page, HTTP response headers of the live script (content-type, cache-control, CORS, security headers) via a GET, and whether the page's factual claims are accurate: "An independent app. Read-only.", "No wallet to connect. No keys. Nothing to sign.", the data-source paragraph ("every 10 minutes, a 6529 Validator node run by Casa NUA reads what the network already publishes and posts it, as plain JSON, to a public repository. Anyone running a node can check it against their own."), prices (US$2/month; R$5 shown only for Brazilian time zone + pt browser), platform list, feature claims per plan. Flag any marketing statement that overclaims.

Output format (plain text, concise, no preamble): SCOPE CHECKED; CONFIRMED CLAIMS (claim → confirmed/partly/not, evidence); FINDINGS numbered with Severity (High/Medium/Low/Info), Title, Evidence (file:line or URL + header), Why it matters to a user, Suggested fix; DATA HELD BY THE PUBLISHER'S SERVERS (plain list); NOT CHECKED / LIMITS.


## Policies, terms and store texts

You are an independent privacy and consumer-terms reviewer (not a lawyer; say so). You did not write these documents or this code. Your findings feed a PUBLIC transparency report, so be rigorous and evidence-based (quote the sentence and give file:line). Do not soften real problems and do not invent ones. STRICTLY READ-ONLY: no edits, commits, pushes, deploys; plain GETs to public URLs only. Never open or print key files or secrets.

Subject: the mobile app "6529 Validator Monitor" by Casa NUA (São Paulo, Brazil): iPhone + Apple Watch (bundle casa.nua.monitor6529, submitted to the App Store as 1.0, free with a monthly auto-renewing subscription "6529 Monitor Pro", US$2/month, R$5 in Brazil) and Android + Wear OS (Google Play open testing). It is a read-only companion for people who run a validator on the 6529 network: the user types a public 6529 handle or wallet address and sees whether that account's validator signed today. No login.

Documents to review:
1. Privacy policy for the Monitor: live at https://app.nua.casa/?app=monitor#privacy ; source in the read-only checkout "<temp>/nuaframe-audit": web/app-nua-casa/privacy-monitor.js, privacy-apps.js, privacy-embed-*.html (find which parts apply to the Monitor vs the other NUA app), plus docs/privacy in "<project>/6529-validator-monitor-features/docs/privacy".
2. Terms of use: find what applies to the Monitor. The iOS code links Apple's standard EULA (https://www.apple.com/legal/internet-services/itunes/dev/stdeula/); check web/app-nua-casa/terms* and README-terms.txt in the checkout to see whether the NUA terms cover the Monitor or only the other app, and what Android users get.
3. Support page: web/app-nua-casa/support-embed.html (does it cover the Monitor? contact channel?).
4. Store listing texts: iOS in "<project>/6529-validator-monitor-features/appstore" and Android in "<project>/6529-validator-monitor-android/android/store/listing". The App Store privacy label declared is "Data Not Collected".
5. In-app subscription screen and legal wording: Shared/Strings*.swift, iOS paywall/"More" views in the features folder (search for subscribe, restore, terms, privacy, EULA), and the Android equivalents (android/core/.../StringsTable.kt, android/app/.../ui).

Ground truth to compare against (read the code enough to establish it yourself; key places: Shared/*.swift networking, Shared/RemoteTexts.swift, iOS/Alerts.swift, the push/ Cloudflare Worker folder in the features repo, android/app + core networking): which servers the apps contact, what they send (typed handle/wallet → api.6529.io profile lookup; raw.githubusercontent.com data files; 6529.io releases.json; the remote texts file on a Cloudflare Worker; any push registration with device token and watched accounts), what is stored on device.

Assess:
a. Accuracy: does each statement in the privacy policy match what the code does? List every mismatch or omission (e.g. third parties that see the user's IP and the handle typed: 6529.io API, GitHub, Cloudflare, Apple/Google push; device tokens; anything stored server-side; retention; deletion route).
b. Is "Data Not Collected" defensible under Apple's definition, given any push registration or server logs? Give a clear yes / no / depends, with the reasoning, and what Google Play Data safety should declare.
c. Completeness for the main regimes, at a practical checklist level: Brazil LGPD (controller identity and contact, legal bases, data subject rights, international transfer, DPO/encarregado contact), EU/UK GDPR (same plus representative questions; flag, don't resolve), California (CCPA "do not sell" statement), children (age rating 4+; any statement on children's data).
d. Subscription disclosures required by Apple (guideline 3.1.2: title, length, price, links to privacy policy and terms of use in the app AND in the store metadata) and by Google Play; cancellation/refund wording; whether the Brazil/elsewhere price difference is disclosed fairly.
e. Wording honesty in listings and app: "independent", "not made by 6529", no implication of official endorsement, no financial/earnings promises (note the app mentions the 6529 chain and the test currency $fake6529), no claim that data is "verified" beyond what is done; disclaimers about data accuracy and availability (the app depends on one collector node).
f. Languages: the app ships in 12 languages; in which languages do the policy and terms exist? Is that a problem?
g. Dates/versioning of the documents and whether changes are announced.

Output format (plain text, concise, no preamble): SCOPE CHECKED; WHAT IS GOOD (short, with evidence); FINDINGS numbered with Severity (High = likely store rejection or materially misleading to users; Medium; Low; Info), Title, Evidence (quote + file:line or URL), Why it matters, Suggested wording or fix; ANSWER TO (b) in one paragraph; NOT CHECKED / LIMITS (include "not legal advice").
