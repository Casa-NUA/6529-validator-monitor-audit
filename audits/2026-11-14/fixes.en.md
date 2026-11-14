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

What is still open here:

- The earlier files stay in the repository's public history.
- The GitHub credential used for publishing is not yet limited to this one repository, and the main branch is not protected.
- A valid signature shows who published a file, not that it is recent: someone able to change files on GitHub could put back an older signed file. The apps check the day inside each file and the collection time.
- The signing key is on the collecting machine. Signing protects against changes made on GitHub, not against that machine being taken over.

## 14 November 2026: apps (written, not yet released)

**Findings addressed:** H3, H4, M1 (app side), M2, M6, and several low findings.
**Where:** the app's private repositories: iPhone and Apple Watch branch `claude/audit-fixes`, Android and Wear OS branch `claude/audit-fixes-android`. **None of this has reached users yet.**

What changed:

- **M1, app side.** Both apps read only the signed files and verify the signature, the file name and the day before showing anything. There is no fallback to unsigned files: a missing or wrongly signed file is treated like a failed download, and the last good day stays on screen.
- **M2.** The wording file can no longer replace texts about privacy, about where the data comes from, or about the subscription.
- **H3.** The App Store description gains links to the Terms of Use and the Privacy Policy in all 13 languages, and loses a sentence about instant alerts, which this version cannot show.
- **H4.** The Android app links to its privacy policy and to Casa NUA's terms from the subscription screen and from its menu.
- **M6.** The Android listing and the setup screen now say that the app downloads a small file of on-screen texts from Casa NUA.
- **Low findings.** Both apps refuse input shaped like a private key or a recovery phrase and send nothing; version names and day numbers from outside sources are checked strictly; Android sends a fixed generic identifier instead of device details, excludes its data from backups, and ignores its test shortcuts in release builds; stale comments and an outdated privacy draft are corrected or removed.

How it was checked so far:

- Automated tests on real signed files: valid files verify; a changed byte, a file served under another name, another key, an unknown key and an older format are all refused. iPhone: 33 tests passed. Android: 22 tests passed.
- Both apps build, including the Android release minification step.
- **Not yet done:** review by a fresh instance, a run on a simulator and an emulator against the live signed files, and release. This entry will be updated when each is done.

## Drafted, awaiting the publisher's review

- **M3.** A revised privacy policy (who the publisher is, legal bases, rights, international transfers, the website).
- **M4.** Terms of use written for this app, and a support section for it.
- Website wording for the low findings (Android "coming soon", price by country, sample day labelled).
