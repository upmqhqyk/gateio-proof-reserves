# Gate.io Proof of Reserves: The 117% Cover Ratio, the Merkle Tree Self-Check, and What the Report Doesn't Prove

Searching that phrase usually means one of three things: you want the current number and the date it was calculated, you want to know whether a Merkle tree actually proves anything, or you want to check your own balance and can't find the button. All three are answerable, and none of them require trusting a marketing page.

Here's what Gate has published, what a third party has independently confirmed, and where the limits of the whole exercise sit.

## The current snapshot, in numbers

Gate's reserve page shows a snapshot dated **July 27, 2026**, published in early August. The headline figures:

| Item | Figure |
| --- | --- |
| Total reserves | $7,050,142,070 |
| Customer net balance | $5,981,321,006 |
| Excess reserve value | $1,068,821,063 |
| Total reserve ratio | 117% |
| Algorithm | Merkle Tree + zk-SNARKs |
| Merkle root hash | `1931bc95cdae9950ec9b7d89629ebdb5a40d700959fee252fcd76694c870e93f` |

The page lists coverage across close to 500 asset types. The three assets most people check first:

| Asset | User balances | Gate wallets | Coverage |
| --- | --- | --- | --- |
| BTC | 21,557 | 26,775 | 124.2% |
| ETH | 374,348 | 456,798 | 122.02% |
| USDT | 940,298,868 | 1,003,061,403 | ~106.7% |

The BTC and ETH coverage figures are Gate's own published percentages. The USDT figure is the ratio of the two balances on the same page — Gate shows the token counts for it but no percentage, which is worth noticing if you hold USDT rather than BTC. Gate's announcement for this snapshot also states a combined stablecoin coverage of 118.97%, covering USDT, USDC, USD1 and GUSD together.

For context, the published ratio has moved like this: 122% on March 16, 2026, 115% on June 22, 2026, and 117% on July 27, 2026. Earlier points in Gate's own history include 128% in May 2025 and 115% in June 2024. The ratio bounces around with asset prices and user deposit flows, so a single reading tells you less than the trend.

## Why 117% is not the same claim as "100% reserves"

This is the part most summaries skip. A total reserve ratio is one number covering hundreds of assets, and an aggregate can look healthy while an individual asset sits below full backing. If coin X is 60% covered and everything else is over-covered, the total can still print above 100%.

So the ratio you should read is the per-asset one, not the headline. Gate reports per-asset coverage separately for the major assets, which is why the BTC and ETH lines matter more than the 117%. Two other things about the aggregate number:

- It is a snapshot, not a live feed. Your balance one hour after the cutoff is not covered by that report.
- It says nothing about the exchange's own corporate liabilities, debt, or off-chain obligations. Reserve proofs cover customer assets versus exchange-held assets. They are not an audit of the company's balance sheet, and they are not insurance.

> A reserve attestation answers "did the exchange hold enough of each asset at this timestamp?" It does not answer "will it hold enough next month?" That distinction matters more than the ratio itself.

## How the Merkle tree check actually works

Gate builds a Merkle tree from user liability data. Each account's hashed ID and balances get hashed into a leaf node; leaf hashes are combined in pairs up the tree until a single root hash remains. Because every change in any leaf changes the root, the root is a fingerprint of the whole liability set at that moment.

The zk-SNARK layer exists to close a loophole that plain Merkle trees have. Gate's own documentation describes three constraints the proof enforces:

1. The total net user balance includes all users' asset balances.
2. Every user's net balance is greater than or equal to zero — no negative-balance entries used to fake the total.
3. Any change to a user's information changes the Merkle root hash.

The asset side is handled separately. Gate transfers a randomly designated amount from each hot and cold wallet to addresses specified by the audit firm, which proves the exchange controls those wallets rather than merely observing them on a block explorer. The audit firm then sums the balances of the confirmed addresses and compares the total to the liability snapshot.

Gate has been publishing a user-verifiable version of this since May 2020, when it engaged Armanino LLP for the audit, and the tooling sits in a public GitHub repository so that anyone can rebuild the tree rather than take the exported text file on faith.

You can run the check yourself, but it needs an account — the leaf data is per-account.

👉 Create a free Gate account and pull your own Merkle leaf

## What the independent auditor verified

The strongest external evidence is CertiK's proof-of-reserves verification for Gate Technology FZE, the Dubai entity licensed by VARA. The attestation covers **as of December 31, 2025**, and it is worth reading closely because the methodology is stricter than the typical exchange press release:

- Scope: 10 assets — ADA, ATOM, BNB, BTC, DOGE, DOT, ETH, LTC, XRP, SOL — across nine blockchain networks.
- Every in-scope asset met or exceeded 100% collateralization **on a per-token basis**. CertiK deliberately avoided converting everything to a single dollar figure, which removes reliance on price feeds that move between snapshot and publication.
- CertiK rebuilt the Merkle tree itself from Gate's open-source repository instead of accepting Gate's output, and the independently computed root matched Gate's published root exactly, with no discrepancy in tree structure, leaf hashing, or proof validation.
- Wallet ownership was proven by on-chain transactions initiated from each address under CertiK's direction — proof of control, not just visibility on a block explorer. 22 reserve addresses were audited across all networks.

CertiK also states plainly what this is: a point-in-time attestation of on-chain assets against user liabilities as of that date. One caveat worth holding onto: those per-token confirmations cover 10 assets, while Gate's own report covers close to 500. The remaining assets rely on Gate's framework and its own published data.

## Verifying your own balance, step by step

Gate's own tutorial estimates about three minutes for this on desktop, and a browser is genuinely easier than mobile here because you'll be copying a hash.

1. Open Gate's reserve page — the one reachable from the site footer under "100% Proof of Reserves". You don't need to be logged in to see the total ratio, the Merkle root, or to download the audit report.
2. Log in. Your account ID appears as a hashed string, and the page shows your BTC and ETH balances as captured at the audit snapshot.
3. Copy your hashed ID and the Merkle leaf data the page gives you.
4. Open the verification tool — either the web-based verification area, the downloadable tool, or the GitHub repository. The repository exists so you can check the math instead of trusting a UI.
5. Run the input through it. The tool computes a Merkle root from your leaf and the sibling hashes.
6. Compare that computed root to the published root hash for the same report. If they match, your balance was inside the tree that produced that root. If they don't, either you copied the wrong report ID or you're feeding it data from a different snapshot date than you think.

If you hold assets other than BTC and ETH, the tool verifies inclusion of your account in the tree, while the per-asset coverage table is where you check whether the specific token you hold is fully backed. Those are two different checks and people conflate them constantly.

## What the report doesn't prove

Worth stating flatly, because this genre of coverage rarely does:

- No independent proof that the liability list is complete. The tree proves the balances *in it* are correctly aggregated. If an account were omitted, the tree wouldn't know.
- The exchange controls the keys. Reserve proofs show control, not that assets are segregated in a bankruptcy-remote structure.
- Aggregate ratios can mask per-asset shortfalls, which is why the per-asset table is the one to screenshot.
- Snapshots age. A clean 117% in July says nothing definitive about October.
- None of this validates the exchange's off-chain finances.

None of that is an argument against checking. It's the difference between knowing what you verified and assuming you verified more.

👉 Open an account and run the check yourself instead of taking the ratio on faith

## What the account itself costs

Exchanges don't sell packages, so the pricing question becomes fee tiers. Gate runs 17 spot tiers, VIP 0 through VIP 16, and your tier is set on whichever of three tracks is most favourable: 30-day trading volume, average GT holdings over 14 days, or account asset value. The volume figure is weighted rather than raw — spot and stock volume count in full, USDT and BTC perpetuals plus USDT delivery futures count at 40%, USD1 contracts and options at 20%, CFDs at 10%.

| Tier | 30-day volume (USD) | Asset value (USD) | VIP maker/taker | Maker/taker with GT |
| --- | --- | --- | --- | --- |
| VIP 0 | 0 | 0 | 0.1% / 0.1% | 0.09% / 0.09% |
| VIP 1 | 60,000 | 2,000 | 0.099% / 0.099% | 0.089% / 0.089% |
| VIP 2 | 120,000 | 4,000 | 0.098% / 0.098% | 0.088% / 0.088% |
| VIP 3 | 240,000 | 10,000 | 0.097% / 0.097% | 0.087% / 0.087% |
| VIP 4 | 500,000 | 20,000 | 0.095% / 0.096% | 0.086% / 0.086% |
| VIP 5 | 1,000,000 | 40,000 | 0.09% / 0.095% | 0.081% / 0.085% |
| VIP 6 | 3,000,000 | 100,000 | 0.085% / 0.09% | 0.076% / 0.081% |
| VIP 7 | 8,000,000 | 200,000 | 0.08% / 0.085% | 0.07% / 0.076% |
| VIP 8 | 20,000,000 | 400,000 | 0.075% / 0.08% | 0.06% / 0.072% |
| VIP 9 | 50,000,000 | — | 0.07% / 0.075% | 0.05% / 0.068% |
| VIP 10 | 100,000,000 | 2,000,000 | 0.04% / 0.058% | same as VIP rate |
| VIP 11 | 120,000,000 | 4,000,000 | 0.03% / 0.045% | same as VIP rate |
| VIP 12 | 240,000,000 | 8,000,000 | 0.02% / 0.037% | same as VIP rate |
| VIP 13 | 440,000,000 | 16,000,000 | 0.01% / 0.03% | same as VIP rate |
| VIP 14 | 800,000,000 | 30,000,000 | 0.008% / 0.023% | same as VIP rate |
| VIP 15 | 1,600,000,000 | 60,000,000 | 0% / 0.02% | same as VIP rate |
| VIP 16 | 3,000,000,000 | — | 0% / 0.0175% | same as VIP rate |

*" — " means the requirement isn't shown in the published tier table. [👉 Open an account and check your tier](https://bit.ly/GateVIP)*

Three things in that table deserve a second look. Maker and taker rates are identical from VIP 0 through VIP 3, so resting an order costs the same as crossing the spread — the maker discount only opens up at VIP 4. Paying fees in GT gives roughly a tenth off at VIP 0, but the separate GT column disappears from VIP 10 upward, so the token stops buying a lower rate exactly where fees start to matter most. And zero maker fees arrive at VIP 15, which is 15 tiers away from a new account.

The same page also lists a flat 0.8% Alpha trading fee and a 24-hour withdrawal limit that moves with your tier rather than your verification level — the values shown run from $3,000,000 at VIP 0 up to $50,000,000 at VIP 16, with $5,000,000 at VIP 5, $8,000,000 at VIP 9 and $10,000,000 at VIP 12.

For someone whose main interest is holding assets and checking reserves rather than churning volume, VIP 0 or VIP 1 is the realistic landing spot, and the GT deduction is the only discount that shows up at that level.

## How to read the next report in five minutes

Ignore the headline ratio first. Check these in order:

1. **The snapshot date.** If it's more than a month old, you're reading history.
2. **The per-asset coverage lines.** BTC, ETH and the stablecoins are where most balances sit. BTC at 124.2% and ETH at 122.02% is a meaningful buffer; a figure drifting toward 100% is a signal worth tracking.
3. **Whether the root hash changed** from the previous report. It should. An unchanged root across snapshots would mean no user balances moved, which is not a thing that happens.
4. **Who verified it, and at what granularity.** "Audited" means little without the asset list and the networks covered. CertiK's Dubai attestation names 10 assets and 9 chains, and lists 22 addresses. That's the level of specificity to look for.
5. **Published or verifiable.** A downloadable report you can rebuild from open-source code is a different product from a blog post with a percentage in it.

## FAQ

**Does Gate's proof of reserves have a third-party audit?**
Yes, for the Dubai entity: CertiK completed an independent verification for Gate Technology FZE as of December 31, 2025, covering 10 assets across nine networks with per-token collateralization at or above 100%, including an independent Merkle root rebuild that matched Gate's published root.

**Does 117% mean every coin is over-backed?**
No. 117% is the aggregate across close to 500 assets. Individual assets carry their own ratios, and that's why Gate publishes BTC, ETH and stablecoin coverage separately.

**Can I verify my balance without an account?**
No. The aggregate figures, the root hash and the audit report are public, but the Merkle leaf tied to your balances requires you to log in.

**How often is a new report published?**
Gate's recent cadence has been roughly monthly or bi-monthly — March 16, June 22 and July 27, 2026 for the last three snapshots I could confirm. There's no fixed public schedule, so treat the snapshot date on the page as the thing to check rather than assuming a regular drop.

**Is a high reserve ratio a reason to move funds onto an exchange?**
That's a different question from the one the report answers. Reserves cover whether customer assets were fully backed at a timestamp; they don't cover custody design, jurisdictional exposure, or what happens to your access if the platform changes its terms for your region. Use the ratio as one input, not the deciding one.
