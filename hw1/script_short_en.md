# PathFinding HCMC — Short Presentation Script (HW1)

**Group 7 · CC01**  
**Course:** Introduction to Artificial Intelligence (CO3061)  
**Advisor:** Dr. Nguyễn Quốc Minh  
**Topic:** Traffic-Aware A* & ALT Routing in Ho Chi Minh City

[Overleaf deck](https://www.overleaf.com/project/6b026f3ae12d6bb9251a6d25)

## Delivery plan (Combined 20-Minute Group Slot)

- **Combined timing:** HW1 (**9:20**) + speaker/deck transition (**0:20**) + HW2 (**9:20**) = **19:00**, leaving **1:00 of buffer** within the **20-minute** presentation limit (and within `10m/Topic`).
- **Q&A:** A separate **10-minute period** for both topics combined. Do not read the Q&A section during the main talk.
- Speak only the paragraphs under **Script**. Bracketed directions are silent cues.
- Use the 12 main slides (`01`–`12`). Open the 6 backup slides (`Q1`–`Q6`) only when a question requires them.

---

## Slide 1 — PathFinding HCMC

**Target: 0:00–0:20**

**Script**

Good morning. We are Group Seven from CC01. Our first project builds a traffic-aware A-star and ALT router for Ho Chi Minh City. We focus on modeling travel time from historical traffic data, formally verifying optimality, and tightening heuristic bounds.

## Slide 2 — Urban Routing as Directed Graph Search

**Target: 0:20–1:00**

**Script**

In urban driving, the shortest road is rarely the fastest road. We model a twenty-by-twenty kilometre area of Ho Chi Minh City from OpenStreetMap, containing forty-four thousand one hundred seventy-eight nodes, one hundred and three thousand eight hundred and ten directed edges, and one hundred and fifty points of interest across fifteen urban categories.

Because of one-way streets and parallel road segments between the same intersections, the road network is a directed multigraph, where each edge cost represents modeled travel time in seconds rather than raw distance.

[Point to the parallel edges `k=0` and `k=1` on the MultiDiGraph diagram.]

## Slide 3 — System Architecture & Traffic Pipeline

**Target: 1:00–1:45**

**Script**

Our traffic pipeline combines OpenStreetMap topology with Kaggle's historical Ho Chi Minh City traffic dataset, using a five-tier fallback whenever local observations are sparse. We freeze a single historical snapshot—weekday eight to eight twenty-nine—so every algorithm runs on identical edge costs.

On top of this snapshot, we implement five independent heuristics alongside a time-dependent FIFO Safe and Zero mode for dynamic departure times. Both our web interface and our NetworkX audit suite call the exact same routing engine, keeping point-of-interest access lines separate from driving cost, and verified end-to-end by forty-four unit tests.

[Point to the 5-tier fallback pipeline and the 44-test NetworkX audit block.]

## Slide 4 — Custom A* & Safe Heuristic Verification

**Target: 1:45–2:40**

**Script**

Our custom A-star search prioritizes nodes by path cost plus heuristic estimate. It tracks exact edge keys so parallel road segments are reconstructed accurately, and allows node reopening whenever a cheaper path is discovered, ensuring no valid shortcut is ever missed.

To guarantee optimal routes, our Safe heuristic divides great-circle distance by a snapshot speed upper bound, V-upper, set just above the maximum edge speed at seventy-four point zero seven four kilometres per hour.

Because map lengths and floating-point rounding can still create subtle mismatches, both our CLI and server verify an edge-level bound on every traversable edge when loading the graph. Across all one hundred and three thousand eight hundred and ten edges, we found zero violations, with a minimum margin of zero point zero six four eight nine two seconds—formally proving admissibility and consistency.

[Point to the `0 / 103,810` edge-check card and the `0.064892 s` minimum margin.]

## Slide 5 — Fixing Local Traffic: Edge-Based Lookahead

**Target: 2:40–3:40**

**Script**

We originally tested a legacy Traffic heuristic with beta equal to zero point two five that scales Safe by local outgoing congestion. However, multiplying by a factor above one can overestimate the true remaining travel time. In this simple counterexample, Safe gives nine point nine nine seconds on a ten-second edge, while Legacy Traffic jumps to eleven point two four seconds—violating admissibility and producing one hundred and fifteen thousand six hundred and twelve consistency violations across twelve audit goals.

To fix this, our Edge-Local heuristic replaces multiplicative scaling with an exact one-step outgoing-edge lookahead: taking the minimum of actual edge cost plus Safe at the neighbor. Because it evaluates real snapshot edges directly, it restores admissibility and consistency while strictly dominating Safe.

[Trace the `11.24 s > 10.00 s` counterexample, then point to the `h_edge` lookahead formula.]

## Slide 6 — Landmark Acceleration: Joint & Separate ALT

**Target: 3:40–4:45**

**Script**

Even with one-step lookahead, a global V-upper weakens the Safe bound on long corridors. To tighten heuristic estimates, we decompose each edge cost into free-flow travel time plus traffic delay, and apply A-star, Landmarks, and Triangle Inequality—known as ALT.

We compare Joint ALT, which applies the triangle inequality to total snapshot cost, against Separate ALT, which bounds free-flow time and traffic delay independently. On directed one-way streets, unreachable landmarks could cause infinity-minus-infinity errors, so we clamp bounds using finite forward and reverse caps, M-F and M-R.

Users can select Tan Son Nhat Airport as the default single landmark, pick one to thirty-two custom landmarks, or use four, eight, sixteen, or thirty-two auto-selected landmarks, all stored in a SHA-256 LRU cache for instant reuse.

[Point to the `c_free + c_delay` split, the `M_F, M_R` caps, and the Tan Son Nhat landmark selector.]

## Slide 7 — Full HCMC Graph: Safe Cuts Expansions by 46.1%

**Target: 4:45–5:35**

**Script**

On the archived forty-four point two thousand node full HCMC graph, we evaluated Zero, Safe, and Legacy Traffic under seed forty-two across twenty origin-destination pairs and twelve reverse-Dijkstra audit goals.

Safe A-star expanded forty-six point one percent fewer nodes than Zero—one hundred and three thousand five hundred and ten versus one hundred and ninety-two thousand and ninety-nine—with zero admissibility or consistency violations. Meanwhile, Legacy Traffic expanded one hundred and four thousand eight hundred and forty-six nodes overall, one point three percent worse than Safe, driven by cross-city outliers like POI one hundred eighteen to one hundred thirty-eight, which added two thousand three hundred and forty-five extra nodes.

[Point to the `103,510` vs `192,099` bar chart and the `POI 118 -> 138` outlier.]

## Slide 8 — 5-Method Benchmark: Expansions & Tightness

**Target: 5:35–6:30**

**Script**

To compare all five heuristics side by side, we benchmarked on a one hundred and fifty HCMC places fixture with nine hundred and ninety directed edges, eight landmarks, and seed forty-two.

Mean node expansions drop steadily from seventy-six point seven five for Zero, to forty-three point eight five for Safe, forty point nine five for Edge-Local, twenty-five point six zero for Separate ALT, and just fourteen point eight zero for Joint ALT—an eighty point seven percent reduction compared to Zero.

Joint ALT reaches a heuristic tightness of zero point nine five seven, with zero admissibility or consistency violations and twenty out of twenty NetworkX Dijkstra optimality matches across all evaluated pairs.

[Point to the 5-method expansion bars and the `0.957` tightness / `20/20` Dijkstra row.]

## Slide 9 — Computational Trade-Offs: Query vs Overhead

**Target: 6:30–7:20**

**Script**

Fewer node expansions do not always translate to faster wall-clock queries. Edge-Local cuts expansions by six point six percent on places and fifteen point three percent on the grid, yet runs at one point seven one eight milliseconds versus zero point five seven one milliseconds for Safe, because it must scan all outgoing edges on every node evaluation.

Comparing ALT variants, Joint beats Separate on ninety-five point zero percent of places pairs and sixty-three point three percent of grid pairs, while Separate still wins on two point seven percent and eight point nine percent. Joint also preprocesses faster and uses half the memory: seventeen point five milliseconds and twenty-six point six kilobytes versus twenty-seven point eight milliseconds and fifty-three kilobytes.

[Point to the Edge-Local runtime spike and the Joint vs Separate comparison table.]

## Slide 10 — Interactive Map UI & Route Case Study

**Target: 7:20–8:00**

**Script**

Here is a recorded UI case study from POI one hundred thirty-two, Bách Hóa Xanh, to POI one hundred twenty-one, Bến Thủ Thiêm. Both heuristics found the exact same fourteen point one kilometre route, taking thirty-three minutes and nineteen seconds across one hundred and seventy-six edges, with Safe expanding nineteen thousand and three nodes versus nineteen thousand eight hundred and fifty-seven for Legacy Traffic.

We upgraded the React and MapLibre interface to support all five heuristics, one to thirty-two custom landmarks, and a standalone demo mode, while noting that displayed runtimes are single UI observations on frozen historical data.

[Point to pin A, pin B, the 176-edge route, and the 5-method and landmark controls.]

## Slide 11 — Limitations and Future Works

**Target: 8:00–8:45**

**Script**

Our study has four honest limitations. First, the five-method benchmarks run on one hundred and fifty-place and one hundred and forty-four-grid fixtures rather than the full forty-four thousand node HCMC graph, because exhaustive reverse-Dijkstra audits across all landmarks are costly at full scale.

Second, both ALT and Edge-Local assume frozen traffic snapshots, whereas extending landmarks to time-dependent ALT remains future work.

Third, Edge-Local incurs outgoing-edge scan overhead, and Kaggle street coverage is partial with model-derived density rather than live sensors. Finally, UI timings are single-run observations and exclude point-of-interest snap legs from driving cost.

[Point to the four limitation cards in sequence.]

## Slide 12 — Conclusions

**Target: 8:45–9:20**

**Script**

In summary, Safe A-star cuts node expansions by forty-six point one percent on the full forty-four thousand node HCMC graph while preserving strict optimality. Across our evaluation, all five verified heuristics guarantee optimal routes with zero edge violations and twenty out of twenty Dijkstra matches.

Finally, Joint ALT achieves zero point nine five seven heuristic tightness and eighty point seven percent fewer expansions on the places benchmark, giving us both speed and formal guarantees. Thank you. We will now transition to our second topic.

[Point to the three headline summary cards: `46.1%`, `5 / 5` optimal, and `0.957` / `-80.7%`.]

---

## Q&A — Quick Reference Answers (20–35s each)

### 1. Why use frozen snapshots, and how does Time-Dependent FIFO work?
Live traffic feeds change continuously and require paid APIs, making controlled algorithm comparisons non-reproducible. Freezing the Kaggle weekday 08:00–08:29 profile lets all heuristics run on identical weights. For dynamic departures, our Time-Dependent FIFO Safe/Zero mode updates arrival-time costs while preserving the FIFO non-overtaking property so Safe remains admissible. (**Backup:** Slide 3, Q3)

### 2. Why is `h_safe` still traffic-aware if it uses global `V_upper`?
`V_upper` is computed from the maximum assigned speed in the active traffic snapshot (`74.074 km/h` for weekday 08:00–08:29), not a static constant. Switching snapshots updates both edge costs `g(n)` and `V_upper`, requiring edge-level re-validation across all 103,810 edges. (**Backup:** Slide 4, Q1)

### 3. Why did legacy `beta = 0.25` fail, and how does Edge-Local fix it?
Multiplying `h_safe` by `1 + beta * q_S(n)` can exceed true remaining travel time—shown by our `11.24 s > 10.00 s` counterexample and `115,612` consistency violations. Edge-Local instead computes `h_edge = min [c_S(e) + h_safe(v, t)]` over outgoing edges, which uses actual snapshot edge costs to guarantee admissibility, consistency, and strict Safe dominance. (**Backup:** Slide 5)

### 4. Why is Edge-Local slower in milliseconds despite expanding fewer nodes?
Edge-Local reduces expansions by `6.6%` on places and `15.3%` on the grid, but each heuristic call scans all outgoing edges of the expanded node. On small graphs or dense intersections, that per-node scan overhead outweighs the saved expansions (`1.718 ms` vs `0.571 ms` for Safe). (**Backup:** Slide 9)

### 5. Why doesn't Joint ALT always dominate Separate ALT?
Joint ALT applies the triangle inequality directly to total cost `c_S = c_free + c_delay`, which gives tighter bounds on `95.0%` of places pairs and `63.3%` of grid pairs with half the preprocessing memory. However, when free-flow and delay bottlenecks occur along different landmark directions, summing independent `c_free` and `c_delay` bounds in Separate ALT can occasionally be tighter (`2.7%` places, `8.9%` grid). (**Backup:** Slide 6, Slide 9)

### 6. How does ALT handle one-way streets and unreachable landmarks without `inf - inf`?
In directed graphs, some nodes cannot reach or be reached from a landmark, producing infinite shortest-path distances. Instead of subtracting `inf - inf`, we clamp unreachable landmark distances using finite forward and reverse caps (`M_F, M_R`), preserving valid, finite, and admissible lower bounds. (**Backup:** Q2)

### 7. How are landmarks selected and cached?
By default, our UI and single-landmark mode anchor at Tan Son Nhat Airport, while users can pick 1–32 custom landmarks or auto-select 4, 8, 16, or 32 landmarks (farthest-first). Landmark distance tables are stored in a SHA-256 keyed LRU cache so repeated queries on the same graph snapshot avoid recomputation. (**Backup:** Slide 6, Q5)

### 8. Why do the 5-method benchmarks use 150-place / 144-grid fixtures instead of the 44k HCMC graph?
Evaluating all five heuristics with full reverse-Dijkstra audits across multiple landmarks and goals is computationally heavy on 44,178 nodes. The 150-place (990-edge) and 144-grid fixtures preserve real HCMC topology and one-way constraints while enabling exhaustive 20-pair, 12-goal consistency audits and repeatable latency comparisons. (**Backup:** Slide 8, Slide 11, Q4)

## If Rehearsal Runs Long

- **Slide 3:** Skip the mention of 44 unit tests and POI access-line separation.
- **Slide 5:** Skip the `11.24 s > 10.00 s` numerical counterexample—state that multiplicative `beta` broke consistency and `h_edge` restores admissibility.
- **Slide 6:** Skip the SHA-256 LRU cache and custom 1–32 range details.
- **Slide 9:** State only the Edge-Local `1.718 ms` vs `0.571 ms` trade-off and Joint ALT's `95.0%` win rate with half the memory.
- **Slide 11:** Read only the first sentence of each of the four limitations.
