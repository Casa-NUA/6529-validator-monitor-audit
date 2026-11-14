# 6529 Validator Monitor: transparency audit no. 1

**Audit date:** 14 November 2026 · **Published:** 14 November 2026 · **Next audit:** with each major release

> This report describes what was found on the audit date and is not edited afterwards.
> For what has been fixed since, see [STATUS.md](../../STATUS.md) and [fixes.en.md](fixes.en.md).

This report says what the 6529 Validator Monitor app does with your data, what was checked, what was found wrong, and what is being done about it. It is written for people who use the app, and it lists the problems as plainly as the good news.

## In short

- **The app cannot touch your wallet.** It has no wallet connection, holds no keys and signs nothing. It only reads public data. All five reviews confirmed this in the code.
- **The app has no login, no ads, no analytics and no trackers.** It contains no third-party code other than Apple's and Google's own system libraries.
- **Casa NUA's servers hold no data about you** in the versions reviewed. The optional "instant alerts" server described in the privacy policy is not switched on.
- **What you see is one node's view.** The network data comes from a single 6529 Validator node run by Casa NUA, republished on GitHub. It is not signed, so the app cannot prove it is genuine. You can check it against your own node.
- **The audit found 4 high, 7 medium and several low findings.** None lets anyone reach a user's wallet or personal data. The high ones concern the publisher's own data repository and missing legal links in the stores. They are listed below with their status.

## Who did this audit, and how independent it is

Read this before trusting the rest.

- **This is not a third-party audit.** Casa NUA, the app's publisher, asked for it. It was carried out by Claude, an AI model made by Anthropic. The app itself was also written with Claude.
- **What "independent" means here:** each review was done by a separate, fresh instance of the model that had never seen the conversation in which the app was built. It received only written instructions and read-only access to the code. It was told to be sceptical, to give a file and line for every claim, and not to soften problems.
- **Model:** `claude-fable-5-2` (Claude Fable 5.2) for all five reviews and for the verification and writing. The model name was read from the run logs.
- **Verification:** after the reviews, the high findings were re-checked directly against the code, the published data and App Store Connect before being written here.
- **No human security professional has reviewed this report.** Treat it as a careful, documented self-examination, not as a certificate.

### The five reviews

| Review | What was read | Version |
|---|---|---|
| iPhone and Apple Watch app | App, Watch, widgets, Siri, purchases, alert server source | commit `03e128d` (the build sent to the App Store as 1.0) |
| Android and Wear OS app | App, Wear, widgets, purchases, build scripts | commit `708767c` |
| Public data repository | Collector code, publish script, every published file, all 120 commits of history | `Casa-NUA/6529-network-data` at `f3f6f81` |
| Website and servers | The app's web page, the server that serves it and the app's wording file, the alert server | NUA-frame commit `24d0d72` |
| Policies and store texts | Privacy policy, terms, support page, store listings, subscription screen, compared with what the code does | live pages on 14 Nov 2026 |

The full instructions given to each review are published with this report ([raw/0-instructions-to-reviewers.md](raw/0-instructions-to-reviewers.md)), and so is each review's full output ([raw/](raw/)), so anyone can repeat the audit or check that nothing was left out. Local folder paths were shortened, and one wallet address was withheld (see finding H1).

### What was not checked

- The final binaries distributed by Apple and Google. The audit read source code and assumes the store builds come from it.
- The settings inside Casa NUA's Cloudflare, GitHub, Apple and Google accounts (who has access, logging switches, two-factor authentication).
- Runtime behaviour. Nothing was run or intercepted; conclusions come from reading code.
- The practices of the third parties the app talks to: 6529.io, GitHub, Cloudflare, Apple and Google.
- Translations other than English and Portuguese.
- This is not legal advice. The policy review is a practical checklist, not a lawyer's opinion.

## What the app sends, and to whom

Every request is HTTPS. This table is the complete list found in the code.

| Goes to | What is sent | When |
|---|---|---|
| GitHub (`raw.githubusercontent.com`, repository `Casa-NUA/6529-network-data`) | Nothing about you. The whole validator list is downloaded and searched on your device. | Each refresh, and in the background |
| 6529 (`6529.io/validator/releases.json`) | Nothing about you. | Each refresh |
| 6529 (`api.6529.io/api/profiles/…`) | The handle or wallet address you typed. | Only when you add an account |
| Casa NUA (`…casanua.workers.dev/site/monitor-texts.json`) | Nothing about you. The app downloads a file of replacement wording. | At most once an hour |
| Apple or Google | Purchase handling, done by the system. | When you subscribe |

As with any internet request, each of these services sees your IP address and basic device information in the request headers.

**Stored on your device only:** the accounts you follow, a 60-day history of what the app saw, your alert settings, and the date your subscription runs to. Nothing secret is stored.

## Claims checked

| Claim | Result |
|---|---|
| Read-only: no wallet connection, no keys, nothing to sign | **Confirmed** on iPhone, Watch, Android and Wear OS |
| No account or login | **Confirmed** |
| No ads, analytics or trackers | **Confirmed.** Note: Google's billing library on Android includes Google's own diagnostics |
| App Store label "Data Not Collected" | **Holds for version 1.0.** It would stop being true if the instant-alerts server were switched on |
| Network data comes from the public repository | **Confirmed,** plus the two 6529 addresses in the table above |
| "Only reads what the network already publishes" | **Partly.** The app also downloads the wording file from Casa NUA, and the data is one node's view, not an official 6529 publication. The app does say "unofficial" |
| Subscription unlocks widgets, Watch, alerts, Siri, 5 accounts | **Confirmed.** Purchases are verified with Apple's and Google's signatures |
| A test switch that unlocks everything cannot ship in a store build | **Confirmed** in the build scripts |
| No passwords or secret keys in the repositories or their history | **Confirmed** |
| The data repository "identifies nothing about the collecting machine" | **Not confirmed.** See finding H1 |

## Findings

Severity is about the effect on users and on the integrity of what the app shows. "Open" means not yet fixed on the date of this report.

### High

**H1. The published data lets anyone work out which account the collector node belongs to.** *Open.*
The node leaves its own weight out of one list in each day's file. Subtracting the list from the total gives that weight, which matches one account. The repository's README says nothing identifies the collecting machine; that is wrong. This exposes the publisher, not users. The data cannot be withdrawn from the public history.
*Planned:* stop publishing those lists, and state openly in the README which account runs the collector.

**H2. Anyone able to write to the data repository could run code on the machine that hosts the validator.** *Open.*
The publishing script downloads the repository and then runs the collector from that same copy, every 10 minutes. A stolen GitHub login would therefore give control of that machine, and of the data the app shows.
*Planned:* run the collector from a fixed copy outside the repository, protect the main branch, and use a credential limited to this one repository.

**H3. The App Store listing has no link to the Terms of Use.** *Open.*
Apple requires this link for apps with subscriptions. The link exists inside the app but is missing from the store description in all 13 languages.
*Planned:* add the links to the description.

**H4. The Android app has no link to its privacy policy, and its Terms link leads to Google's terms, not Casa NUA's.** *Open.*
Found in the Android code reviewed. The privacy policy itself exists and is public.
*Planned:* add the links before the Android app leaves testing.

### Medium

**M1. The network data is not signed.** *Open, by design for now.*
The app trusts Casa NUA's node, its collector machine, its GitHub account and GitHub's hosting. If any were compromised, the app could show wrong validators or wrong "signed" status, or send a false alert. It could not reach a wallet. Today the data can be checked by running the open-source collector on your own node, or against the independent site 6529scan, whose cycle results matched on all seven days compared.
*Planned:* sign each publication and have the app verify it; compare results with a second source.

**M2. A wording file on Casa NUA's server can replace almost any text in the app after store review.** *Open.*
It cannot add links, run code or change prices, and the subscription's legal text is protected. But it could change what the app says about privacy or about the data source. The file is empty today.
*Planned:* protect the privacy and subscription texts as well, or sign the file.

**M3. The privacy policy does not say who the legal publisher is or list users' rights.** *Open.*
It names every server the app contacts, which is good. It lacks the company's legal name and address, the legal basis for processing, how to exercise rights under Brazilian (LGPD) and European (GDPR) law, and a statement on international transfers. It exists only in English and Portuguese, while the app ships in 12 languages.

**M4. There are no terms written for this app, and the support page is about another app.** *Open.*
On iPhone the app relies on Apple's standard licence. Nothing states, in terms a user accepts, that the data may be late or wrong and should not be the only check that a validator signed. The support link leads to help for Casa NUA's other app.

**M5. The instant-alerts server, if switched on as written, would not match what the policy says.** *Not active.*
The server is not deployed and the app's switch for it is hidden. As written, it would accept registrations from anyone, and it would keep a device's notification address for up to 60 days after alerts are turned off, while the policy says it is deleted at once.
*Planned:* fix both before it is ever switched on, and update the App Store label at that time.

**M6. The Android listing says "nothing is sent to us", but the app does fetch the wording file from Casa NUA.** *Open.*
The request carries no personal data, but the statement should mention it.

**M7. The collector filters only the top level of what it copies.** *Open.*
New details added inside an existing section by a future node version would be published automatically. H1 is an example of this.

### Low

- A private key pasted by mistake into the handle field would be sent to 6529's server as if it were a handle. The app should refuse anything shaped like a key.
- Text from outside sources (a version name, a day number) is not checked strictly enough. A corrupted source could put odd text in a notification or crash the app. It could not run code.
- On Android, the app refreshes in the background every 15 minutes for all users, including those with no alerts or widgets.
- Hidden test shortcuts used for screenshots are still present in release builds. They unlock nothing.
- The website loads fonts from Google, so Google sees visitors' IP addresses. The page itself uses no cookies or analytics.
- The website lists Android without saying it is not yet available, shows US$2 to visitors whose local price is lower, and uses a sample day without labelling it as an example.
- The published files carry no age stamp of their own, so stale data is not obvious.
- The data repository's README does not list every field it publishes, and gives no security contact.
- Some internal comments and one privacy file still describe an older design.
- The subscription check runs on the device. A modified phone could unlock paid features. This affects the publisher's income, not users.

## What happens next

1. Each fix is recorded in [fixes.en.md](fixes.en.md) with its date and how it was checked, and the finding's line in [STATUS.md](../../STATUS.md) is updated. This report itself stays as it is.
2. The audit is repeated with each major release, as a new dated folder. Earlier reports stay available, unchanged.

Questions about this report: bugs@nua.casa
