# Current status of every finding

**Last updated:** 14 November 2026 · *Em português: [STATUS.pt.md](STATUS.pt.md)*

What each status means:

- **Fixed**: the fix is live and was checked.
- **Fix written, not yet released**: the code or text change exists, passed its tests and was reviewed, but users do not have it yet (it is waiting for a store's review, or held for the next version).
- **Open**: not fixed.
- **Not active**: concerns something that is not switched on.

Details and evidence for each fix: [audits/2026-11-14/fixes.en.md](audits/2026-11-14/fixes.en.md).

## Audit no. 1 (14 November 2026)

### High

| | Finding | Status |
|---|---|---|
| H1 | The published data lets anyone work out which account the collector node belongs to | **Fixed** on 14 Nov 2026 for all new publications. The earlier files stay in the data repository's public history and cannot be removed. |
| H2 | Anyone able to write to the data repository could run code on the machine that hosts the validator | **Fixed** on 14 Nov 2026. The main branch is protected against history rewrites, and the collector now publishes with a credential limited to this one repository. |
| H3 | The App Store listing has no link to the Terms of Use | **Fixed in the listing** on 14 Nov 2026: the App Store description now carries the Terms of Use and Privacy Policy links in all 13 languages. It becomes visible to the public when Apple approves version 1.0. |
| H4 | The Android app has no privacy policy link, and its Terms link leads to Google's terms | **Fix written, not yet released.** In Android version 4, built and held until Google finishes reviewing the first version and the terms and support pages are published. |

### Medium

| | Finding | Status |
|---|---|---|
| M1 | The network data is not signed | **Publisher side fixed** on 14 Nov 2026: every data file is now published signed. **App side: fix written, not yet released**: iPhone build 8 (submitted for App Store review on 14 Nov 2026) and Android version 4 (held) accept only signed files. |
| M2 | A wording file on Casa NUA's server can replace almost any text in the app | **Fix written, not yet released.** Privacy, data-source and subscription texts can no longer be replaced. |
| M3 | The privacy policy does not say who the legal publisher is or list users' rights | **Open.** A revised policy is drafted and awaits the publisher's review. |
| M4 | No terms written for this app; the support page is about another app | **Open.** Terms and a support section are drafted and await the publisher's review. |
| M5 | The instant-alerts server, if switched on as written, would not match the policy | **Not active**, and **open**. Its records no longer keep the device's notification address after switching off (code fixed 14 Nov 2026). The server stays off until it also verifies that requests come from the real app. |
| M6 | The Android listing says "nothing is sent to us", but the app fetches a wording file from Casa NUA | **Fix written, not yet released.** |
| M7 | The collector filters only the top level of what it copies | **Fixed** on 14 Nov 2026. |

### Low

| Finding | Status |
|---|---|
| A private key pasted into the handle field would be sent to 6529's server | **Fix written, not yet released** (both apps refuse it and send nothing). |
| Text from outside sources is not checked strictly enough | **Fix written, not yet released** (strict version format, day range checks). |
| Android refreshes in the background every 15 minutes for all users | **Open.** |
| Hidden test shortcuts remain in release builds | **Fix written, not yet released** on Android. **Open** on iPhone. |
| Data saved before signatures were checked could still be shown after an update (found while reviewing the fixes) | **Fix written, not yet released**: the new versions discard it. |
| The website loads fonts from Google | **Open.** |
| Website wording: Android listed without "coming soon", one price shown to all, sample day not labelled | **Open.** Replacement wording is drafted. |
| Published files carry no age stamp of their own | **Open.** |
| The data repository's README does not list every field and gives no security contact | **Fixed** on 14 Nov 2026. |
| Stale internal comments and privacy file | **Fix written, not yet released.** |
| The subscription check runs on the device | **Open**, accepted: it affects the publisher's income, not users. |
