# Privacy Policy — 6529 Validator Monitor

*Last updated: 14 November 2026*

This policy covers 6529 Validator Monitor for iPhone and Apple Watch, and for Android phones and Wear OS watches, and the app's web page. In short: no account, no wallet connection, no ads, no tracking. The app reads public network data, what you follow stays on your devices, and the app sends Casa NUA nothing about you.

## Who is responsible

6529 Validator Monitor is provided by CASA NUA MUSEU DE ARTE EM REDE LTDA (“Casa NUA”, “we”), a company registered in Brazil under CNPJ 19.035.440/0001-02, with its registered office at Rua Maria Paula, 78, apto. 1010, Bela Vista, São Paulo/SP, CEP 01319-000, Brazil. For the purposes of Brazil's General Data Protection Law (LGPD) and the EU and UK GDPR, Casa NUA is the controller of the limited processing described here.

Privacy questions and requests go to 6529monitor@nua.casa. This is the channel for data-protection matters under the LGPD.

The app is independent. It is not made by, affiliated with or endorsed by 6529.

## No account, no wallet, no tracking

There is no account to create and nothing to sign. The app never connects to a wallet, never asks for keys or a recovery phrase, and never asks for a signature or a transaction. It shows no ads, contains no advertising, analytics or crash-reporting software, and does not track you across apps or websites.

On Android, the subscription uses Google's Play Billing library, which is Google's own software and has its own diagnostics, sent to Google under Google's privacy policy. We do not receive them.

What this policy says about Android describes version 1.0 (4) and later; earlier test versions sent the system's standard request header, which includes the device model and Android version, and did not switch off device-to-device transfer.

## What the app sends, and to whom

Every request the app makes uses HTTPS. This is the complete list:

1\. GitHub (raw.githubusercontent.com, repository Casa-NUA/6529-network-data, folder docs/data/signed): the app downloads the day's network data. The collector signs the files. The app checks the signature before showing the data from iPhone version 1.0 (build 8) and Android version 1.0 (4) onward; earlier test builds did not. The request carries nothing about you or the accounts you follow: the whole validator list is downloaded and matched on your device.

2\. 6529 (6529.io/validator/releases.json): the app downloads the list of validator releases, to know the latest version. The request carries nothing about you.

3\. 6529 (api.6529.io): when you add an account, and only then, the app sends the handle or wallet address you typed to 6529's public profile service, to find that account's public profile and wallets. Type only a public handle or a public wallet address; never type a private key or a recovery phrase.

4\. Casa NUA's own server, a Cloudflare Worker (nua-published-curations.casanua.workers.dev): the app downloads a file of on-screen texts (explanations, glossary and similar wording, so they can be corrected without an app update), at most once an hour. The request carries nothing about you or the accounts you follow, and request logging is switched off on this server.

5\. Apple or Google: when you subscribe, restore a purchase or the app checks your subscription, the purchase is handled by the App Store or Google Play on your device, under your Apple Account or Google Account.

As with any internet request, each of these services necessarily sees your device's IP address and the standard request headers. On iPhone and Apple Watch these are the ones the system adds, such as the app's name and version and your language. On Android the app's own requests carry a fixed app name, with no device model and no Android version. GitHub, 6529, Cloudflare, Apple and Google each handle that information under their own privacy policies. Casa NUA does not receive it from them.

## What stays on your devices

These are kept only on your device: the accounts you follow (what you typed, the handle and its public wallets), a 60-day history of what the app saw, your alert settings, the date your subscription runs to, and a copy of the public data and of the texts file. Nothing secret is stored. We cannot see any of it.

Your phone shares the account you follow and the date your subscription runs to with the app's own widgets and with your own watch (your paired Apple Watch, or your Wear OS watch through Google's own phone-to-watch link (Google Play services)), so they can show the same information.

Deleting the app from your phone and your watch deletes this data.

## Device backups

On iPhone, the data described above may be included in your device backups, including iCloud Backup if you use it. Those backups are yours and are handled by Apple under your Apple Account; we have no access to them. On Android, backup and device-to-device transfer of the app's data are switched off.

## Alerts

The app's reminders and alerts are made on your device by the app itself, from the public data above. Nothing is sent to us to make them work.

Instant alerts (alerts sent from a server) are not available in the current version. If they become available, this policy will be updated first.

## Subscription

On iPhone, the subscription is sold by Apple through the App Store. On Android, it is sold by Google through Google Play. Apple or Google handles the payment; we never receive your name, card or payment details. The app asks the store on your device whether your account holds the subscription, keeps the end date on the device, and passes it only to your own watch.

Apple and Google never tell us who bought; we see sales totals. As with every app, they also show us download counts and crash and usage figures that do not identify anyone, from people who agreed to share them and from TestFlight testers.

## Siri, widgets, shortcuts and watches

Siri answers and Shortcuts on iPhone, widgets, the watch apps and complications and, on Android, the Status shortcut (which speaks its answer with your phone's own text-to-speech) read the same public data and the same on-device information described above. Nothing about them is sent to us. When you speak to Siri, Apple processes your request under Apple's privacy policy.

## What the app does not use

The app does not use your location, contacts, photos, microphone or camera. On Android, the only permission it asks you for is showing notifications.

## Why this processing is allowed (legal bases)

In plain words: to show you what you ask for, the app has to make the requests listed above, and the internet cannot deliver a reply without an IP address. We rely on:

Performing the service you ask for (LGPD art. 7, V; GDPR art. 6(1)(b)): downloading the data, looking up the handle or wallet you type, delivering the app's texts, and checking your subscription.

Legitimate interest in keeping the service secure and working (LGPD art. 7, IX; GDPR art. 6(1)(f)): our hosting provider, Cloudflare, briefly processes IP addresses to deliver the texts file and to protect the service against abuse. We keep no log of these requests.

We do not ask for consent because we do not use your data for advertising, profiling or analytics. Notifications are shown only if you allow them in your device's settings.

## How long data is kept

Casa NUA keeps no data about the app's users on its servers, so there is nothing there for us to retain or delete. On your device, the history covers 60 days and the rest stays until you remove an account or delete the app.

If you write to us, we keep your e-mail only as long as needed to answer you, and we delete it when you ask.

## Your rights

Under the LGPD (art. 18) you may ask us to confirm whether we process your data, to give you access to it, to correct it, to anonymise, block or delete it, to port it, to tell you with whom it is shared, and you may object to processing and withdraw consent where consent is used. Under the GDPR you have the rights of access, rectification, erasure, restriction, portability and objection.

To exercise any of them, write to 6529monitor@nua.casa. We answer free of charge, within the time the law sets.

In practice: Casa NUA has no accounts and holds no data on its servers about the people who use the app. For most requests our answer will therefore be that we hold nothing about you: the data is on your device, where you can see it in the app and delete it by removing an account or deleting the app. The only personal data we hold is any e-mail you send us, which you can ask us to delete; the figures the stores show us, described under “Subscription”, identify nobody. For data held by GitHub, 6529, Cloudflare, Apple or Google, please use those companies' own channels.

You may also complain to a data protection authority: in Brazil, the Autoridade Nacional de Proteção de Dados (ANPD, gov.br/anpd); in the EU or the UK, the supervisory authority of the place where you live.

## International transfers

Casa NUA is in Brazil. The services the app talks to (GitHub, 6529, Cloudflare, Apple and Google) are operated outside Brazil. Using them is necessary to provide what you ask the app for: the requests listed above cannot be answered in any other way.

## California

We do not sell personal information, and we do not share it for cross-context behavioural advertising. We have not done so in the past 12 months.

## Children

The app is not directed to children, and we do not knowingly hold data about them. The app has no accounts, and Casa NUA collects no personal data through it from anyone, of any age.

## The website

The app's web page (the 6529 Validator Monitor section of app.nua.casa, and this policy, the Terms and the Support page) uses no analytics and no advertising. It loads its fonts from Google Fonts, so Google sees the visitor's IP address and standard request headers. Its texts are loaded from Casa NUA's Cloudflare Worker, the same server the app uses, with request logging switched off. The site sets one short-lived Cloudflare cookie (__cf_bm, about 30 minutes), which Cloudflare uses to tell people from bots. The page remembers the language you chose in your browser's local storage; that never leaves your browser. The site is built with Carrd and served through Cloudflare, which process requests under their own policies.

The same site also carries pages of NUA, Casa NUA's other app. Those pages are covered by NUA's own privacy policy.

## Languages

This policy is published in English and Portuguese. For users in Brazil, the Portuguese text prevails; elsewhere, the English text prevails, to the extent the law allows.

## Changes

When this policy changes, this page changes with it and the date at the top is updated. Previous versions are kept in the public audit repository, in the folder “policies/”:

https://github.com/Casa-NUA/6529-validator-monitor-audit

If a change adds a new use of data, such as instant alerts, the policy is updated before the feature is switched on.

## Contact

Casa NUA: CASA NUA MUSEU DE ARTE EM REDE LTDA, CNPJ 19.035.440/0001-02, Rua Maria Paula, 78, apto. 1010, Bela Vista, São Paulo/SP, CEP 01319-000, Brazil.

Questions about this policy and privacy requests: 6529monitor@nua.casa

---

Published at https://app.nua.casa/?app=monitor#privacy · archived 14 November 2026.
