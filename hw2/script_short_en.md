# Wizard’s Chess — Short Presentation Script

**Group 7 · CC01**  
**Course:** Introduction to Artificial Intelligence (CO3061)  
**Advisor:** Dr. Nguyễn Quốc Minh  
**Topic:** Alpha-Beta vs Monte Carlo Tree Search on Chess

[Figma deck](https://www.figma.com/slides/5P2NteCDJSz2qDw7ItJzpC)

## Delivery plan

- Main talk: aim to finish at **9:20**, leaving **40 seconds of buffer** within the 10-minute slot.
- Q&A: a separate **5-minute period**. Do not read the Q&A section as part of the talk.
- Speak only the paragraphs under **Script**. Bracketed directions are silent cues.
- Use the 12 main slides. Open the six backup slides only when a question requires them.
- The time marks are rehearsal targets, not guarantees. They include pointing, pauses, and slide changes.

## Slide 1 — Wizard’s Chess

**Target: 0:00–0:20**

**Script**

Good morning. We are Group Seven from CC01. Our project compares Alpha-Beta and Monte Carlo Tree Search for chess. We focus on how they use limited thinking time, and why their results differ.

## Slide 2 — Chess as a Search Problem

**Target: 0:20–0:50**

**Script**

In chess, a good move must survive the opponent’s best reply. With about thirty-five choices per position in our branching-factor estimate, the search tree grows quickly.

Searching everything is impractical. We therefore compare two ways to explore only part of the tree within a small budget.

[Point from a move to the opponent’s replies.]

## Slide 3 — Shared Search Architecture

**Target: 0:50–1:30**

**Script**

Both agents receive a position and a budget, then return a move and search statistics.

We use python-chess for the rules and legal moves. Our project implements the search algorithms, evaluation function, and experiments.

The main comparison uses the same handcrafted evaluation for Alpha-Beta and MCTS-Eval. This helps us study search behaviour while controlling their evaluation knowledge.

## Slide 4 — Alpha-Beta Search

**Target: 1:30–2:35**

**Script**

Alpha-Beta assumes both players choose their best responses.

In this example, the left branch gives the root a value of five. On the right, the opponent can force a value of three or less. That branch cannot improve our choice, so we skip its remaining replies.

Our implementation adds iterative deepening, move ordering, cached positions, and quiescence search. These help find useful cutoffs and avoid stopping during tactical exchanges.

Standard Alpha-Beta preserves the fixed-tree minimax result. Some additional selective heuristics trade exactness for speed.

[Trace the left branch, then the crossed-out right branch. Pause before continuing.]

## Slide 5 — Monte Carlo Tree Search

**Target: 2:35–3:35**

**Script**

MCTS repeats four steps: select a path, expand the tree, evaluate a leaf, and back up the result.

Selection balances promising moves with less-explored alternatives. The highlighted path illustrates where more visits are allocated.

Our variants use random rollouts, heuristic evaluation, proven-result backup, or shallow Alpha-Beta searches at the leaves.

The main difference is how values are combined: Alpha-Beta uses min and max, while ordinary MCTS averages sampled values. With limited time, a critical tactical reply may receive too little attention.

[Point to each step, then follow the highlighted path.]

## Slide 6 — Experimental Design

**Target: 3:35–4:15**

**Script**

We ran five experiments: tournament strength, time-budget scaling, search efficiency, tactical accuracy, and agreement with master moves.

The tournament uses one second per move. Each pair plays four openings with both colours, giving eight games per pairing. With seven agents, this produces one hundred and sixty-eight games.

The experiments measure both performance and the mechanisms behind it.

## Slide 7 — Alpha-Beta Won 48 / 48 Games

**Target: 4:15–5:05**

**Script**

Alpha-Beta won all forty-eight of its tournament games, all by checkmate. These games cover six opponents, not only MCTS-Eval.

MCTS-Eval achieved a tournament score of seventy-three percent. Score counts each draw as half a win, so it differs from win rate.

Alpha-Beta clearly led this run. However, the samples are small, so this is evidence about our tested agents and conditions, not a universal ranking.

## Slide 8 — 191× Fewer Nodes at Depth 4

**Target: 5:05–5:50**

**Script**

At depth four, minimax used about one point five million nodes. Alpha-Beta with ordering and a transposition table used about seven thousand eight hundred.

That is approximately one hundred and ninety-one times fewer nodes. It shows how pruning, ordering, and caching reduce search work.

This is a node-count comparison, not a runtime speedup. The experiment uses six positions with quiescence disabled.

[Compare Minimax with AB-Ordered. Do not read the other values.]

## Slide 9 — Fewer Traps, Lower Game Scores

**Target: 5:50–6:40**

**Script**

The hybrid results show a trade-off. In eighteen depth-two trap positions, MCTS-Eval made three blunders, while the depth-two hybrid made none.

However, the hybrid’s tournament score fell to thirty-four percent, compared with seventy-three percent for MCTS-Eval.

Deeper leaf searches improve tactical coverage, but cost more computation. Under the same time budget, fewer simulations remain to compare moves.

Fixing these traps therefore did not produce a stronger tournament player.

## Slide 10 — Search Trap: A Case Study

**Target: 6:40–7:45**

**Script**

This recorded example shows both agents analysing the same position with one second each.

Alpha-Beta plays queen takes d-seven, check, and proves a forced mate. MCTS-Eval chooses knight to d-five and misses the mating sequence.

A deeper path does not guarantee that the critical reply was examined. Alpha-Beta reports its completed iteration depth; MCTS reports its deepest tree path. These numbers are not directly comparable.

The image comes from the repository’s Search Lab, rather than a new live measurement in the Wizard’s Chess HUD.

[Allow 15–20 seconds to point out Qxd7+, Nd5, and the trace. Use the prepared screenshot. Do not wait for a full live game or recite the entire mating line.]

## Slide 11 — Limitations and Next Steps

**Target: 7:45–8:40**

**Script**

Our study has several limitations. We need more games, openings, and seeds, as well as deeper tactical tests.

Time budgets also introduce machine-load noise. Fixed node budgets would improve reproducibility.

There is a memory difference: Alpha-Beta keeps cached information within a game, while MCTS rebuilds its tree. Tree reuse is therefore a useful improvement.

Finally, this project uses handcrafted evaluation. The results do not establish how neural MCTS would perform, and our saturated scaling experiment leaves the long-term budget question unresolved.

## Slide 12 — Conclusions

**Target: 8:40–9:20**

**Script**

Our main findings are straightforward. Alpha-Beta led the tournament. Ordering and caching greatly reduced search work. Hybrid search avoided some traps but sacrificed simulations and tournament strength.

Under our shared handcrafted evaluation and small budgets, Alpha-Beta performed better than the tested MCTS variants.

Thank you. We are ready for your questions.

---

## Q&A — Separate Five-Minute Session

**Do not present these answers in sequence.** Listen to the question, answer directly, and give one supporting fact. Aim for **20–35 seconds per initial answer**; expand only if requested. Five minutes must also accommodate the questions and follow-ups.

### 1. Is Alpha-Beta always better than MCTS?

No. Our result applies to these implementations, handcrafted evaluation, and small budgets. It does not compare Alpha-Beta with neural MCTS. We would need broader experiments to generalise the conclusion.

**Backup:** Slide 11 or 17.

### 2. Why can MCTS search deeper and still lose?

Its depth is the deepest sampled path, while Alpha-Beta reports a completed iteration. A long selective path can miss an important reply elsewhere. Depth alone therefore does not measure tactical coverage.

**Backup:** Slide 10.

### 3. Why did the hybrid become weaker?

Each leaf search became more expensive. Although the hybrid avoided the tested traps, it completed fewer simulations within the same time. Better leaf evaluation did not compensate for reduced sampling in the tournament.

**Backup:** Slide 9.

### 4. Was the comparison fair?

We controlled the main time budget, evaluation, openings, and colours. However, Alpha-Beta retains cached information while MCTS rebuilds its tree. We acknowledge that asymmetry and propose tree reuse and fixed-node experiments as follow-ups.

**Backup:** Slide 17.

### 5. What did your group implement?

We implemented the search algorithms, evaluation function, experimental pipeline, statistics, and interfaces. Python-chess provides the rules and legal move generation. The master-game data provide test positions, not neural-network training.

**Backup:** Slide 3 or 18.

### 6. What do 48/48 and 191× actually mean?

Forty-eight wins cover six opponents, with eight games against each. The one-hundred-and-ninety-one factor compares node counts at depth four between minimax and Alpha-Beta with ordering and caching. It is not a measured runtime speedup.

**Backup:** Slide 7 or 8.

## If Rehearsal Runs Long

- Keep the case-study explanation, limitations, and conclusion.
- On Slide 4, omit the final sentence about selective heuristics; retain it for Q&A.
- On Slide 5, omit the sentence listing the variants.
- On Slide 7, omit the explanation of draws and define score only if asked.
- Avoid reading names, every chart label, or the full mating sequence.
- The earlier long script is a study reference. Use this short version for delivery.

## Evidence

Content follows the existing Figma deck and the repository snapshot at commit `c5304d5`:

- [Saved experiment results](https://github.com/HatakekkSheeshh/hw2_introAI/blob/c5304d564d4d3be7ea7c83a7973bbc200208813e/results/report/summary.md)
- [Report discussion and limitations](https://github.com/HatakekkSheeshh/hw2_introAI/blob/c5304d564d4d3be7ea7c83a7973bbc200208813e/report/chapters/main/06-discussion.tex)

All results describe the saved run, not a new benchmark.
