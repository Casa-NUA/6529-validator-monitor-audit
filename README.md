# 6529 Validator Monitor: transparency audits

Public audit reports for the [6529 Validator Monitor](https://app.nua.casa/#validator-6529-monitor) app
(iPhone, Apple Watch, Android, Wear OS), published by Casa NUA.

*Em português: [LEIAME.md](LEIAME.md).*

## Start here

| | |
|---|---|
| **Current status of every finding** | [STATUS.md](STATUS.md) |
| **Latest audit** (no. 1, 14 November 2026) | [Report](audits/2026-11-14/report.en.md) · [Fixes made since](audits/2026-11-14/fixes.en.md) · [Reviewers' full output](audits/2026-11-14/raw/) |

## What these audits are

- They are **not third-party audits.** Casa NUA, the app's publisher, commissions them. They are carried out by Claude, an AI model made by Anthropic, which was also used to write the app.
- Each audit is done by separate, fresh instances of the model that have not seen how the app was built. They get written instructions and read-only access, and are told to be sceptical and to cite a file and line for every claim.
- Each report names the exact model, the exact versions of the code that were read, the full instructions given, the reviewers' full output, and what was **not** checked.
- No human security professional reviews them. Treat them as a careful, documented self-examination, not as a certificate.

## How this repository works

- **A report is never edited after publication.** It says what was true on the audit date.
- **Fixes are logged separately**, in the audit's `fixes` file, each with its date, what changed, and how the fix was checked (fixes to anything in production are reviewed by another fresh instance before they go live).
- **[STATUS.md](STATUS.md)** always shows where every finding stands today.
- **Each new audit gets a new dated folder.** The git history of this repository shows that nothing was rewritten.
- A finding that could be used against the publisher's systems is fixed before the report that describes it is published.

## What is audited

The app's code (iPhone, Apple Watch, Android, Wear OS), the public data repository
[Casa-NUA/6529-network-data](https://github.com/Casa-NUA/6529-network-data) and its collector, the app's web page
and the servers behind it, and the privacy policy, terms and store texts compared with what the code does. The
app's source code is private; the data repository and collector are public.

## Data signing keys

Since the fixes that followed audit no. 1, the network data is published signed, and the app versions released after that audit accept it only if it is signed by one of these keys (key id = first 16 hex digits of the
SHA-256 of the public key in DER form). They are listed here as a second place to check them, besides the data
repository itself:

- `9fc874c0f34c3d51`: the key in use.
- `04f3b06b89f126a7`: the reserve key.

## Contact

Questions, corrections and security reports: bugs@nua.casa
