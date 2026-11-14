# Fixes made after audit no. 1

*Em português: [fixes.pt.md](fixes.pt.md).* Newest entries first. The [report](report.en.md) itself is not edited.

Each entry says what changed, where, and how the change was checked. "Reviewed" means a separate, fresh instance of the model (`claude-fable-5-2`) that had not written the change examined it and tried to break it.

## 14 November 2026: data repository and collector

**Findings addressed:** H1, H2, M1 (publisher side), M7, and the README low finding.
**Where:** public repository [Casa-NUA/6529-network-data](https://github.com/Casa-NUA/6529-network-data), commit [`2172b2f`](https://github.com/Casa-NUA/6529-network-data/commit/2172b2f). Live from the publication of 15:48 UTC.

What changed:

- **H1.** The two lists from which the collecting node's own account could be worked out (`certificate.signer_weights`, `certificate.unsigned_weights`) are no longer published. The README now says plainly that they were published until this date, that they remain in the repository's history, and that its earlier claim was wrong.
- **H2.** The collector no longer runs from the repository copy. It runs from a fixed copy outside it, collects and signs in its own folder that nothing from the repository enters, and only then copies the result into the repository copy. It refuses to start from inside a repository, refuses a repository tree containing links, submodules or git control files, and checks that what it is about to publish is exactly what it collected.
- **M7.** The collector now checks every value at every level against a strict schema (type and form). Anything not in the schema is dropped; anything new and unknown is reported in the collector's log.
- **M1, publisher side.** Every data file is also published as a signed envelope under `docs/data/signed/` (ECDSA P-256, SHA-256). The file's name is part of what is signed, so a signed file cannot be passed off as another. Key ids: `9fc874c0f34c3d51` (in use), `04f3b06b89f126a7` (reserve).
- **README.** Lists every published field, says which values are one node's own observations, describes the personal-data aspect, corrects the "check it yourself" instructions, and gives a contact.

How it was checked:

- **Reviewed twice before going live.** The first review found three serious problems in the first version of the fix itself: a link planted in the repository could have made the signer publish a local file, delete files outside the repository, or sign files the collector had not written. The fix was redesigned (collect and sign outside the repository; sign the file name). The second review re-ran its attacks against the new version, found them closed, and asked for two further one-line hardenings, which were applied.
- **Tested end to end** in an isolated copy with a throwaway key: normal publication, a second run making no needless commit, refusal to run from inside the repository, a planted rogue file not signed and removed, a planted link refused, a missing key refused, and all 11 published envelopes verifying against the public key.
- **Checked live** after activation: the signed files are served, and the cycle files no longer contain the two lists.

Later the same day: the main branch of the data repository was protected against history rewrites and deletion (a GitHub ruleset with no bypass), and the reserve signing key was moved off the collecting machine.

What is still open here:

- The earlier files stay in the repository's public history.
- (Closed later on 14 Nov 2026: the collector now publishes with a GitHub credential that can write only to this one repository.)
- A valid signature shows who published a file, not that it is recent: someone able to change files on GitHub could put back an older signed file. The apps check the day inside each file and the collection time.
- The signing key is on the collecting machine. Signing protects against changes made on GitHub, not against that machine being taken over.

## 14 November 2026: apps (written and reviewed; in the stores' hands or held)

**Findings addressed:** H3, H4, M1 (app side), M2, M5 (code), M6, and several low findings.
**Where:** the app's private repositories: iPhone and Apple Watch branch `claude/audit-fixes` (commit `4f2a396`, build 8 of version 1.0), Android and Wear OS branch `claude/audit-fixes-android` (commit `aa6c67a`, later `48c98c0` with the low findings; version code 4).

Where each stands:

- **iPhone and Apple Watch:** build 8 was uploaded to Apple on 14 Nov 2026. The earlier submission of version 1.0 (build 7) was withdrawn before review. The App Store description now carries the links in all 13 languages. Build 8 is in TestFlight for internal testers and awaits Apple's beta review for public testers. It was submitted for App Store review, together with the subscription, on 14 Nov 2026 at 16:13 UTC. **Public users do not have it yet.**
- **Android and Wear OS:** version 4 is built and held. It will be sent once Google finishes reviewing the first version. **Users do not have it yet.**

What changed:

- **M1, app side.** Both apps read only the signed files and verify the signature, the file name and the day before showing anything. There is no fallback to unsigned files: a missing or wrongly signed file is treated like a failed download, and the last good day stays on screen.
- **M2.** The wording file can no longer replace texts about privacy, about where the data comes from, or about the subscription.
- **H3.** The App Store description gains links to the Terms of Use and the Privacy Policy in all 13 languages, and loses a sentence about instant alerts, which this version cannot show.
- **H4.** The Android app links to its privacy policy and to Casa NUA's terms from the subscription screen and from its menu.
- **M6.** The Android listing and the setup screen now say that the app downloads a small file of on-screen texts from Casa NUA.
- **M5 (code only; the server stays off).** The instant-alerts server's records of "alert already sent" no longer contain the device's notification address, only a salted hash of it, so nothing left after switching off can be turned back into the address. Requests are size- and shape-checked, version names are checked strictly, and a rate limit can be switched on. Its documentation now says it must not be deployed until it verifies that requests come from the real app and reads the signed data. M5 stays open.
- **Low findings.** Both apps refuse input shaped like a private key or a recovery phrase and send nothing; version names and day numbers from outside sources are checked strictly; data saved by earlier versions, from before signatures were checked, is discarded; Android sends a fixed generic identifier instead of device details, excludes its data from backups, and ignores its test shortcuts in release builds; stale comments and an outdated privacy draft are corrected or removed.

How it was checked:

- **Automated tests on real signed files:** valid files verify; a changed byte, a file served under another name, another key, an unknown key and an older format are all refused. iPhone: 33 tests passed. Android: 24 tests passed.
- **Reviewed.** A fresh instance reviewed each app's changes and found no blocking defect. It checked the exact bytes that are signed, that every part of each app (app, watch, widgets, voice shortcut, background refresh) goes through the verified path, that no address of an unsigned file remains, and that the live signed files verify with the shipped code. It pointed out wording differences between the two platforms and a broken text generator; both were then aligned and fixed.
- **Run against the live signed data:** the iPhone app in a simulator and the Android app in an emulator (fresh install) both loaded the current day through the verified path. On Android the private-key refusal was also tried by hand: the message appeared and no request was sent.
- Both apps build, including the Android release minification step. The iPhone build that was uploaded was checked to carry no test unlock and no instant-alerts address.

Known limits, stated by the reviews: a correctly signed older file could be replayed (the apps check the day inside each file and the collection time, not more); the final store binaries were not inspected; translations other than English and Portuguese were not reviewed.

## 14 November 2026: website

- A section "Audited regularly" was added to the app's page, before the download screen, linking this repository's report and status page. It says the audit is commissioned by the publisher and carried out by AI reviewers, not by an outside firm. Reviewed by a fresh instance before publication.

## 14 November 2026: the collector runs every 5 minutes

Not an audit finding: a change made the same day, recorded here because it changes published text. The collector now runs every 5 minutes instead of 10 (data repository commit [`748b58f`](https://github.com/Casa-NUA/6529-network-data/commit/748b58f)). Its README explains why not less: GitHub serves the raw files through a cache that keeps each one for 5 minutes, so a shorter interval would gain little while adding commits. The app's page and the support text were updated ([policies/2026-11-14b/](../../policies/2026-11-14b/)); the texts inside the apps follow in their next versions. Reviewed by a fresh instance before going live, which corrected an overstatement in the first wording of that explanation.

## 14 November 2026: low findings

- **Age of the published files (fixed, live).** `network.json` now carries `validators_unix` and `cycle_unix`: when the validator list and the day's file were last written. Data repository commit [`fe00e77`](https://github.com/Casa-NUA/6529-network-data/commit/fe00e77). Reviewed before going live: a fresh instance ran the collector through every failure path (empty folder, node with no members, failed fetch, day change, damaged previous file) and compared the output with what was published; only the two new fields were added.
- **Android background reads (written, in the held version 4; commits `894e1a0`, `48c98c0`).** The 15-minute background refresh now runs only for subscribers, on phone and watch. The review of the first version of this fix found that it could strand a paying subscriber: someone whose subscription came back outside the app (a declined card charged later, a paused plan resumed) would have got no alerts until they opened the app. It was corrected before any release: for 90 days after a subscription ends, the app asks Google Play once a day whether it is back, without contacting the data servers. 36 automated tests pass.
- **Test shortcuts on iPhone (written; commit `ae42f6e`).** The launch options used for screenshots are compiled only into development builds. This ships in the build after build 8, which was already with Apple.
- **Fonts from Google on the website (open, by decision).** The page that hosts the app's section loads the same fonts itself, so removing them from the section would change nothing for a visitor. The privacy policy now says so.

## 14 November 2026: privacy policy, terms, support and page wording (published)

**Findings addressed:** M3, M4, and the website-wording low finding.
**Where:** https://app.nua.casa/?app=monitor#privacy, `#terms` and `#support`; the texts are archived in [policies/2026-11-14/](../../policies/2026-11-14/), and the policy they replace in [policies/2026-11-13/](../../policies/2026-11-13/).

What changed:

- **M3.** The privacy policy now names the publisher (legal name, registration number, address), lists every request the app makes and to whom, the legal bases, users' rights under Brazilian and European law, international transfers, what the website loads, and a contact address for this app.
- **M4.** The app has Terms of Use of its own (independent, read-only, unofficial data from one node that can be late or wrong, the subscription and how to cancel, refunds and the 7-day withdrawal right in Brazil) and a support page of its own.
- **Website.** Android is shown as "coming soon" until its store link exists; the day shown in the ring is labelled as an example; the price note says the store shows your country's price.

How it was checked:

- **Reviewed twice by fresh instances** against the app's code. The first pass, done while finalising the texts, removed draft statements that were not true of the app (a subscription name the app does not use; a Lock Screen widget and Siri on Android, which do not exist there). The second found four more points, all fixed before publication: the texts said the app checks the data's signature without saying from which version (the versions in the stores' hands on that date did not all do so); three Android statements were true only of the version being held; the folder of earlier versions that the policy pointed to did not exist yet; and a sentence about what the stores tell the publisher left out download and crash figures.
- **Automated checks (92):** English and Portuguese match section by section; the other app's pages on the same site render identically with and without the new code in 88 address and language combinations; no address-bar value is written into the page.
- **Checked live** after publication: the served files are byte-identical to the reviewed ones.

Known limit, stated in the texts themselves: the signature check and some Android protections apply from iPhone version 1.0 (build 8) and Android version 1.0 (4) onward. That sentence will be removed once earlier test versions are no longer in use.
