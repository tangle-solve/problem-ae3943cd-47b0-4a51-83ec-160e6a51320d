<!-- solve-problem:ae3943cd-47b0-4a51-83ec-160e6a51320d -->
# Fair rewards across heterogeneous propagation

A [Tangle Solve problem](https://solve.tangle.tools/problems/ae3943cd-47b0-4a51-83ec-160e6a51320d).

## What is the problem?

In a Nakamoto-style proof-of-work blockchain, blocks propagate through a peer-to-peer network at heterogeneous speeds: well-connected miners learn about new blocks faster than poorly connected ("slow") miners, so slow miners' blocks are more likely to become stale/orphaned. This creates a reward unfairness: expected reward per unit hashpower is increasing in network connectivity, which (a) biases the equilibrium toward centralized, well-connected mining, and (b) creates an incentive to deliberately delay block publication (withholding, selfish-mining-style attacks) if delay is ever rewarded. The problem, as stated for DAG-based ledgers (e.g., StrongChain, Tailstorm):

Find a reward scheme R for a DAG ledger, together with protocol parameters (block-creation rate, block size / bandwidth budget, depth/confirmation parameters), such that:

1. **Fairness bound:** for honest miners i, j with hashpower fractions α_i, α_j but different effective propagation delays δ_i ≤ δ_j, the expected reward ratio satisfies E[R_i]/α_i ≥ E[R_j]/α_j − ε(δ_j − δ_i, parameters), with ε minimized — quantify how quickly fairness degrades with delay disparity.
2. **No-reward-for-delay:** any unilateral strategy that delays block publication (including selfish/withholding strategies) yields expected reward at most (1 + o(1)) times honest reward, i.e., delay-based attacks are not profitable.
3. **Joint bounds:** derive the achievable trade-off frontier between throughput (block rate × size, limited by bandwidth), latency (time to confirmation), and ε.

The precise formalization (reward function on the DAG, network model — typically a synchronizer or Δ-synchronous gossip network with per-miner delays) is part of the problem; the census leaves it vague.

## Why does it matter?

DAG ledgers raise block rates, which increases the fraction of blocks that race during propagation windows and thus amplifies connectivity-based unfairness — so the centralization pressure worsens exactly as throughput improves. A solution would let high-throughput protocols (Tailstorm-class cash systems) guarantee proportional rewards for small/mining-pool participants, deterring both centralization and selfish-mining-style withholding. It also connects to classical fair-queueing / heterogeneous-service scheduling theory, potentially importing mature tools.

## What's already known?

- In Bitcoin's chain, stale rates and connectivity unfairness are empirically documented; rewards are proportional to *blockchain-share*, which penalizes slow miners (verify exact attribution).
- **StrongChain** (Szalachowski et al., 2019) mitigates waste by counting weak blocks and rewarding collaborative mining, improving inclusiveness of smaller miners, but does not give delay-strategy-robust fairness guarantees.
- **Tailstorm** (Keller et al., 2023) uses a DAG with sub-blocks ("storms") to raise throughput and analyzes fairness/security against withholding attacks, but its fairness guarantees are for a homogeneous-delay model; the heterogeneous-propagation regime is open.
- Selfish-mining literature (Eyal–Sirer and successors) shows reward rules that credit any published block invite withholding; this is the main tension: crediting stale blocks helps slow miners but pays delayers.
- Related analytical tools: fair queueing bounds and scheduling in heterogeneous-service systems, but no known transfer to DAG reward design exists.

No joint bandwidth–latency–fairness frontier is known for any DAG ledger.

## What would count as solving it?

- **(a)** A formal network model (per-miner propagation delays, bandwidth limits) plus a reward function on the DAG with a theorem bounding reward skew ε as a function of delay disparity, block rate, and block size — ideally ε → 0 as the scheme's parameters are tuned, with the throughput cost of that tuning quantified.
- **(b)** A proof that the scheme is (approximately) incentive-compatible against delay strategies: no unilateral deviation involving delayed publication earns more than honest play.
- **(c)** A matching lower bound showing no reward scheme can simultaneously achieve ε below some threshold, throughput above some level, and delay-robustness — i.e., a formal trade-off frontier.
- Simulations alone would be a partial result; the census marks this as theory.

## How to start

1. Read Tailstorm (arXiv:2306.12206) Sections on reward distribution and attack analysis — extract its exact reward rule and extend its selfish-mining analysis to heterogeneous delays.
2. Read StrongChain's fairness claims and identify what "fairness" it measures; formalize the ε-skew metric both papers lack.
3. Model the problem as a race/queueing system: block propagation as competing Poisson arrivals with per-miner delay; compute stale-rate skew analytically (this gives the achievable ε for naive proportional reward).
4. Attempt the impossibility side first: a lower bound showing any reward that credits late-arriving blocks pays delayers likely exists and constrains all designs; this shapes the frontier.

Main obstacle: the tension between (1) and (2) — any tolerance for slow blocks is exploitable by strategic delay, so exact fairness and delay-robustness may be incompatible; the interesting result is quantitative.

## Key references

- P. Szalachowski, D. Reijnspergen, A. Gervais, X. Sun. *StrongChain: Transparent and Collaborative Proof-of-Work Consensus.* Cryptology ePrint Archive (2019?) — introduces weak blocks and rewards that credit collaborative mining to reduce waste and favor weaker miners. (verify venue/year)
- R. Keller, B. Roger, C. Cachin, K. Wüst. *Tailstorm: A Secure and Fair Blockchain for Cash Transactions.* arXiv:2306.12206 (2023). https://arxiv.org/abs/2306.12206 — DAG ledger with sub-block "storms" raising throughput; analyzes security and fairness/withholding attacks in a homogeneous network model.
- (Background, add yourself) Eyal & Sirer, *Majority is not Enough* (2014) — the canonical delay/withholding attack framework any reward scheme must withstand. (verify which variant is most relevant)

## Warnings

- The census record is a research target, not a precisely stated problem: no formal reward function or network model is fixed, so your formalization choices materially affect provability — state them explicitly.
- Do not assume fairness in the reward *rule* implies fairness in *expected* reward; expectation depends on stale probabilities, which depend on delays and hashpower shares.
- Beware double-counting: schemes rewarding "in-time" publication inherently punish slow-but-honest miners; verify any candidate scheme against both honest-slow and strategic-delay deviations.
- The "fair queueing / heterogeneous-service scheduling" analogy is suggestive but not proven transferable — propagation is gossip, not a controlled scheduler.
- Confirm citation details for StrongChain (venue/year) before relying on it.

---
*`BCGT-000260` · flagged for review*

## Acceptance guidance

- **(a)** A formal network model (per-miner propagation delays, bandwidth limits) plus a reward function on the DAG with a theorem bounding reward skew ε as a function of delay disparity, block rate, and block size — ideally ε → 0 as the scheme's parameters are tuned, with the throughput cost of that tuning quantified.
- **(b)** A proof that the scheme is (approximately) incentive-compatible against delay strategies: no unilateral deviation involving delayed publication earns more than honest play.
- **(c)** A matching lower bound showing no reward scheme can simultaneously achieve ε below some threshold, throughput above some level, and delay-robustness — i.e., a formal trade-off frontier.
- Simulations alone would be a partial result; the census marks this as theory.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/ae3943cd-47b0-4a51-83ec-160e6a51320d?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000260.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000260)
