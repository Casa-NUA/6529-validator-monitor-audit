# Current status of every finding

**Last updated:** 16 November 2026 · *Em português: [STATUS.pt.md](STATUS.pt.md)*

What each status means:

- **Fixed**: the fix is live and was checked.
- **Fix written, not yet released**: the code or text change exists, passed its tests and was reviewed, but users do not have it yet (it is waiting for a store's review, or held for the next version).
- **Open**: not fixed.
- **Not active**: concerns something that is not switched on.

Details and evidence for each fix: [audits/2026-11-14/fixes.en.md](audits/2026-11-14/fixes.en.md).

**Releases:** iPhone and Apple Watch version 1.0 (build 8) is live on the App Store since 16 Nov 2026. Version 1.1 (build 15) is live on the App Store since 16 Nov 2026. Version 1.2 (build 18) is live on the App Store since 17 Nov 2026. Android version 4 is still held until Google finishes reviewing the first version.

## Audit no. 1 (14 November 2026)

### High

| | Finding | Status |
|---|---|---|
| H1 | The published data lets anyone work out which account the collector node belongs to | **Fixed** on 14 Nov 2026 for all new publications. The earlier files stay in the data repository's public history and cannot be removed. |
| H2 | Anyone able to write to the data repository could run code on the machine that hosts the validator | **Fixed** on 14 Nov 2026. The main branch is protected against history rewrites, and the collector now publishes with a credential limited to this one repository. |
| H3 | The App Store listing has no link to the Terms of Use | **Fixed.** The App Store description carries the Terms of Use and Privacy Policy links in all 13 languages (added on 14 Nov 2026). It is public since Apple approved version 1.0 on 16 Nov 2026. |
| H4 | The Android app has no privacy policy link, and its Terms link leads to Google's terms | **Fix written, not yet released.** In Android version 4, built and held until Google finishes reviewing the first version. |

### Medium

| | Finding | Status |
|---|---|---|
| M1 | The network data is not signed | **Publisher side fixed** on 14 Nov 2026: every data file is now published signed. **App side: fixed on iPhone** on 16 Nov 2026: version 1.0 (build 8), live on the App Store since that day, accepts only signed files. **Android: fix written, not yet released** (version 4, held). |
| M2 | A wording file on Casa NUA's server can replace almost any text in the app | **Fixed on iPhone** on 16 Nov 2026 (version 1.0 on the App Store); **Android: fix written, not yet released** (version 4, held). Privacy, data-source and subscription texts can no longer be replaced. |
| M3 | The privacy policy does not say who the legal publisher is or list users' rights | **Fixed** on 14 Nov 2026: the revised policy is published. Every version is kept in [policies/](policies/). |
| M4 | No terms written for this app; the support page is about another app | **Fixed** on 14 Nov 2026: Terms of Use and a support page for this app are published. |
| M5 | The instant-alerts server, if switched on as written, would not match the policy | **Not active**, and **open**. Its records no longer keep the device's notification address after switching off (code fixed 14 Nov 2026). The server stays off until it also verifies that requests come from the real app. |
| M6 | The Android listing says "nothing is sent to us", but the app fetches a wording file from Casa NUA | **Fix written, not yet released.** |
| M7 | The collector filters only the top level of what it copies | **Fixed** on 14 Nov 2026. |

### Low

| Finding | Status |
|---|---|
| A private key pasted into the handle field would be sent to 6529's server | **Fixed on iPhone** on 16 Nov 2026 (version 1.0 on the App Store); **Android: fix written, not yet released** (version 4, held). Both apps refuse it and send nothing. |
| Text from outside sources is not checked strictly enough | **Fixed on iPhone** on 16 Nov 2026 (version 1.0 on the App Store); **Android: fix written, not yet released** (version 4, held). Strict version format, day range checks. |
| Android refreshes in the background every 15 minutes for all users | **Fix written, not yet released** (Android version 4, held): background reads only for subscribers. |
| Hidden test shortcuts remain in release builds | **Fixed on iPhone** in version 1.1 (build 15), live on the App Store since 16 Nov 2026; on Android, fix written in version 4, not yet released. |
| Data saved before signatures were checked could still be shown after an update (found while reviewing the fixes) | **Fixed on iPhone** on 16 Nov 2026 (version 1.0 on the App Store discards it); **Android: fix written, not yet released** (version 4, held). |
| The website loads fonts from Google | **Open**, and now disclosed in the privacy policy. The page that hosts the app's section loads the same fonts itself, so removing them from the section alone would change nothing for a visitor. |
| Website wording: Android listed without "coming soon", one price shown to all, sample day not labelled | **Fixed** on 14 Nov 2026. |
| Published files carry no age stamp of their own | **Fixed** on 14 Nov 2026: `network.json` now says when the validator list and the day's file were last written. |
| The data repository's README does not list every field and gives no security contact | **Fixed** on 14 Nov 2026. |
| Stale internal comments and privacy file | **Fix written, not yet released.** |
| The subscription check runs on the device | **Open**, accepted: it affects the publisher's income, not users. |
