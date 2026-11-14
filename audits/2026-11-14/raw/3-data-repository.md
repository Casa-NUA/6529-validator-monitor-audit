SCOPE CHECKED
- Local clone at HEAD f3f6f81 (equal to origin/main, 2026-11-14 12:25Z): `collector/collect.py` (339 lines), `collector/publish.sh`, `collector/casa.nua.6529-network-data.plist`, `README.md`, `LICENSE`, `.gitignore`, all of `docs/data`.
- Full history: 120 commits, 421 unique blobs, every JSON blob parsed, every key path enumerated, and a regex sweep for IPs, `.onion`, `/Users/`, `/home/`, tokens, private keys, hostnames and own/peer/addr/host/path/metro/release keys.
- GitHub public API (plain GETs): repo metadata, branches, tags, PRs, forks, commit verification, raw cache headers.
- Cross-check against the independent upstream collector `nothingbugger/6529scan` (cycles 20765–20771 and `validators.json`).
- Local clone git config, for the authentication method only; no values were read.
- Nothing was edited or run, and the node API was not called.

CONFIRMED CLAIMS
- "Everything in docs/data is something every node already receives" → PARTLY.
  - Confirmed: cycle roots and headers for 20765–20771 are identical to 6529scan's; the wallet set (212), machine codes (224), weights and TDH are identical.
  - Not network facts: `certificate.strong_unix` and `finalized_unix` (differ by 7–820 s between the two collectors), `signer_weights` (see F1), and by their names and the collector's own docstring (collect.py:12-13), `compare` and `dual_run`. `chain.json` is labelled "as the collecting node sees it" (README:16).
- "Nothing identifies the collecting machine (no keys, peers, servers, addresses, account flags)" → NOT CONFIRMED.
  - True literally: no keys, IPs, onion addresses, peers, paths, hostnames, tokens or `own` flag appear in any of the 421 blobs.
  - False in substance: the collecting node's own account is derivable by arithmetic (F1).
- "Anyone running a node can run the same collector and compare" → PARTLY. It works on macOS; auto-detect is macOS-only, and a whole-file diff will not match exactly (F6, F7).
- "Reads only the loopback API (`status`, `network`, `cycles/<day>`, `chain`), stdlib only" → CONFIRMED (collect.py:20-28, 263-264, 286, 323). It also runs `pgrep`, `lsof` and `netstat` locally for port discovery (collect.py:58-80); their output is never written out.
- "Continent and country only, not state or metro" → CONFIRMED (collect.py:157). The history never contains `state` or `metro` under `report`; 6529scan publishes both.
- "No `release` in network.json" → CONFIRMED in code (collect.py:307-319) and in all history.
- No secrets or internal hostnames in the repo or history → CONFIRMED. The only matches are `127.0.0.1` in code, the word "token" in `token_count`, and "home"/"OWN_NODE" as report enum values.

FINDINGS

1. Severity: High — The published certificates reveal the collecting node's own wallet.
   - Evidence:
     - `signer_weights` is passed through wholesale (collect.py:96-101, 123-124).
     - In every cycle file, `len(signer_weights) = signing_accounts − 1`, and `signed_weight − sum(signer_weights)` equals one account's weight: 8862076, 8875683, 8889278, 8902865, 8916507, 8930140, 8943789 for days 20765–20771.
     - On day 20771 that value matches exactly one account in `validators.json`: one wallet [address withheld by the publisher], one machine, `country: BR`, `infrastructure: home`, software 0.1.0-rc.16.
     - The same wallet falls out of all 88 historical snapshots.
     - 6529scan's files for the same days omit a different single weight (2151068 on 20769, where this repo omits 8916507), so each node's API evidently leaves its own account out of the list.
     - Caveat: this is an inference; I could not confirm it against the node API or confirm that the wallet is Casa NUA's.
   - Why it matters:
     - It contradicts README:7-8 and collect.py:13-14 ("never writes this node's … location, account").
     - It links publisher, wallet, machine key code, country and home hosting.
     - It is permanent in git history.
     - Anyone who follows "Check it yourself" and publishes the output exposes themselves the same way.
     - 6529scan has the same leak.
   - Suggested fix:
     - Stop publishing `signer_weights` and `unsigned_weights`, or reinsert the missing weight in sorted position when the list is one short.
     - Then choose: disclose plainly in the README which account the collector runs under (simplest, since the publisher is already named), or recreate the repo to purge history.
     - Correct the README either way.
     - Tell 6529scan's maintainer privately.

2. Severity: High — Anyone with push access to the repo gets code execution on the validator host.
   - Evidence:
     - publish.sh:21-24 hard-resets the clone to origin/main and then runs `collector/collect.py` from that same clone.
     - The plist (lines 12-14) runs `publish.sh` from the clone too.
     - The `main` branch is unprotected (GitHub API `protected: false`) and commits are unsigned.
   - Why it matters: a compromised GitHub account or token with write access can replace `collect.py` and have it run within 10 minutes, as the logged-in user, on the machine that holds validator keys.
   - Suggested fix:
     - Run the collector and publish script from a pinned copy outside the publishing clone.
     - Let the clone hold only `docs/data`.
     - Protect `main` and require reviewed changes for `collector/`.

3. Severity: Medium — No integrity mechanism; the app trusts one node, one account and GitHub.
   - Evidence:
     - All 120 commits are unsigned (`git log %G?` = N; API `verified: false`).
     - There are no signatures or hashes over snapshots.
     - Cycle files carry certificate summaries (weights, counts, roots) but not the validator signatures, so a certificate cannot be verified from the published data alone.
   - Why it matters: a compromised publisher account or collector machine can make the app show anything: fake validators, wrong signed/missed status, wrong roots, altered past cycles.
   - What limits it today: tampering is detectable only by comparing with your own node or with 6529scan, which currently agrees on roots and headers.
   - Suggested fix:
     - Sign each snapshot with a key whose public half is pinned in the app.
     - Publish the full cycle certificates (signatures and roster) so results are checkable against roster keys.
     - Have the app cross-check `cycle_root` against a second independent collector and flag disagreement.
     - Sign commits.

4. Severity: Medium — Push credential is a general `gh` credential.
   - Evidence:
     - The local clone's config uses `gh auth git-credential` as helper.
     - The remote URL contains no token, and nothing credential-related is in the repo.
     - Commits are attributed by GitHub to a personal user account rather than a bot.
   - Why it matters: a `gh` login token typically covers every repo that user can reach, so a compromise of the collector machine is not limited to this repo. I did not inspect the token's scope.
   - Suggested fix: use a deploy key or fine-grained token limited to contents-write on this one repo, ideally under a machine account.

5. Severity: Medium — The whitelist is top-level only; nested values pass through unvalidated.
   - Evidence, passed wholesale:
     - chain `head`, `finalized`, `currency`, `switch`, `rules`, `fees`, `reason` (collect.py:196-200, 211)
     - `continents`, `places` (191-192)
     - `next_keys`, `next_accounts` (317-318)
     - `compare.totals` (150-151)
     - `dual_run.implementations` (144)
     - every certificate and header value (96-107)
     - `day`, `state` and roots (130-137)
   - Why it matters:
     - Top-level keys are whitelisted (`pick`, 117-120), so a new sibling field cannot leak; that part is sound.
     - A new sub-field inside any object above would be published automatically. F1 is exactly this class.
     - `reason`, `switch` and `finalized` have been null in all 63 chain snapshots, so their real shape is unknown. The docstring (206-207) says "why not" is dropped, yet `state` and `reason` are kept.
     - Report strings (`software`, `country`, etc.) are written by other operators and reach the app with no type, length or pattern check.
   - Suggested fix: whitelist recursively with type and length checks (`country` as two capital letters, enums for continent, infrastructure and `today`, a length cap on `software`, hex patterns for roots). Drop or enum-check `reason`.

6. Severity: Low — "Check it yourself" overstates exact equality.
   - Evidence:
     - README:25 says roots and certificates "must match exactly".
     - Against 6529scan, all seven cycle files differ in `certificate.strong_unix`, `finalized_unix` and `signer_weights`.
     - Day 20771 also differed in signed counts, as expected mid-day.
     - `cycle_root`, `roster_root` and `header` do match.
   - Why it matters: the documented `diff` (README:31-32) reports differences for honest data, which undermines the check.
   - Suggested fix: compare only `cycle_root`, `header` and `roster_root`, or drop the node-local fields, and say which fields are local observations.

7. Severity: Low — Auto-detect is macOS-only and the README does not say so.
   - Evidence:
     - Paths are hard-coded: `/usr/sbin/lsof`, `/usr/sbin/netstat -anv`, the macOS app path and the `_validator6529` service user (collect.py:31-33, 58-80).
     - Elsewhere it falls back to port 6529 (collect.py:94), which per the docstring (collect.py:46) is not what rc.8+ uses.
     - README:30 says it "finds your node's loopback API" and never mentions `--api`.
   - Suggested fix: document `--api` and the platform limit.

8. Severity: Low — Staleness and mixed-age files are not visible per file.
   - Evidence:
     - `validators.json` and the cycle files have no timestamp of their own.
     - `network.json` is still written fresh when a cycle fetch fails silently (collect.py:287-288) or when members are empty (collect.py:170-172, 329-331).
     - If the collector or node stops, publish.sh exits (set -e, line 8) and the last data stays up indefinitely.
     - Normal lag is up to about an hour by design (publish.sh:30-36; observed gaps 142–3619 s, median 605 s), plus 5 minutes of raw-CDN cache (`max-age=300`).
   - What is sound: writes are atomic per file (collect.py:242-248), there is no force push (publish.sh:39), a failed push self-heals on the next run, and a not-ready node publishes nothing (collect.py:270-274).
   - Suggested fix: add `generated_unix` to every file and have the app show data age and warn past a threshold.

9. Severity: Low — Commit metadata carries a personal identity despite the script's stated intent.
   - Evidence:
     - publish.sh:6-7 says identity is set "so nothing personal goes into the history".
     - All 120 commits use a GitHub noreply address that embeds a personal account's numeric id and login, and GitHub links them to that user.
     - Commit cbc7b09 carries a −03:00 timezone.
   - Suggested fix: push from an organisation machine account, or reword the comment.

10. Severity: Low — README is incomplete about what is published.
    - Evidence, published but not listed in README:12-18:
      - `next_members` (wallet, key count, TDH for tomorrow's roster)
      - `continents` and `places` aggregates
      - `infrastructure` (home/rack) and `source_class`
      - `first_signed_day`, `last_signed_day` and per-machine weight
      - `compare`, `dual_run`, `state`, `other_roots`, `history_*`
      - `next_keys`, `next_accounts`, `network_id`
    - Also: collect.py:165 says `next_members` keeps "public keys", but the values are integer counts (221 of 221).
    - There is no contact address, security contact or `SECURITY.md`; the org profile does list a public email and site.
    - The MIT licence is present with upstream attribution, but GitHub detects it as "Other" because of the extra copyright line, and the README states no licence for the data.
    - Suggested fix: list every field, add a contact and security address, and state the licence in the README.

11. Severity: Info — Personal data and privacy.
    - What is published per wallet: country (only 29 of 224 machines report one; 13 countries have exactly one machine), continent, home/rack, software version, and signing status every 10 minutes.
    - There are no handles, IPs or names.
    - All of it is broadcast on the network and already republished by 6529scan in finer detail (state, metro).
    - Git history makes it a permanent time series: an operator who later removes their location cannot retract past snapshots, and an outdated software version (rc.9–rc.16 are visible) is attributable to a wallet.
    - Suggested fix: state this in the README and in the app's privacy text: location is self-declared, optional, public on the network, and archived here.

12. Severity: Info — Repo growth. Roughly 100 commits a day at about 94 KB for `validators.json` means tens of thousands of commits a year, on a raw endpoint with no service guarantee. Consider a single orphan data branch or GitHub Pages.

TRUST MODEL (for end users)
The app shows what one validator node, run by Casa NUA, reported in the last 10 to 70 minutes, copied to a public GitHub repository. Nothing in the data is cryptographically signed by the publisher, and the files do not include the validators' own signatures, so the app cannot prove the data is genuine. You are trusting Casa NUA's node, its collector machine, its GitHub account, and GitHub's hosting. If any of those were compromised or faulty, the app could show wrong validators, wrong signing status or wrong cycle results; it could not touch your wallet or keys, because the app only reads public files. You can check the data by running the same open-source collector against your own node, or by comparing with the independent 6529scan site, which today shows the same cycle roots and validator list. The location shown for a validator is whatever its operator chose to declare; it is optional, unverified, public on the network, and kept in this repository's history. Despite the README's wording, the published figures do allow the collecting node's own wallet to be worked out. Every change is recorded in the public git history, so past publications can be audited but not erased.

NOT CHECKED / LIMITS
- The node API itself, by instruction. So the meaning of `source_class: OWN_NODE` (one machine, in the highest-weight account; it looks like a self-reported data-source class, not a collector flag), of chain `state: stalled`, and the shape of `reason`, `switch` and `finalized` are unverified. F1 is an inference from published data.
- The mobile app: how it handles stale, malformed or hostile JSON, and whether it shows data age.
- The scope and storage of the push credential, the launchd installation, the log file, and GitHub org membership and 2FA.
- Whether `cutoff_hash` and `cutoff_number` match Ethereum, and whether roots can be recomputed from chain data.
- Whether the network validates operator-declared report strings.
- A line-by-line diff against 6529scan's collector; only the outputs were compared.
- Deleted or force-pushed commits no longer reachable on GitHub; none are indicated (single branch, no tags, PRs or forks).