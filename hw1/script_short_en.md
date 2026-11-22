# PathFinding HCMC — Short Presentation Script (HW1)

**Group 7 · CC01**  
**Course:** Introduction to Artificial Intelligence (CO3061)  
**Advisor:** Dr. Nguyễn Quốc Minh  
**Topic:** Traffic-Aware A* Routing in Ho Chi Minh City

[Overleaf deck](https://www.overleaf.com/project/6b026f3ae12d6bb9251a6d25)

## Delivery plan (Combined 20-Minute Group Slot)

- **Combined timing:** HW1 (**9:00**) + speaker/deck transition (**0:40**) + HW2 (**9:20**) = **19:00**, leaving **1:00 of buffer** within the **20-minute** presentation limit.
- **Q&A:** A separate **10-minute period** for both topics combined. Do not read the Q&A section during the main talk.
- Speak only the paragraphs under **Script**. Bracketed directions are silent cues.
- Use the 11 main slides (`01`–`11`). Open the 5 backup slides (`Q1`–`Q5`) only when a question requires them.

---

## Slide 1 — PathFinding HCMC

**Target: 0:00–0:20**

**Script**

Good morning. We are Group Seven from CC01. Our first project builds a traffic-aware A-star router for Ho Chi Minh City. We focus on how to model travel time from historical traffic data and formally verify optimality.

## Slide 2 — Urban Routing as Graph Search

**Target: 0:20–0:55**

**Script**

In urban driving, the shortest road is not always the fastest road. We model a twenty-by-twenty kilometre area of Ho Chi Minh City from OpenStreetMap, containing forty-four thousand one hundred seventy-eight nodes, over one hundred and three thousand directed edges, and one hundred and fifty points of interest.

Because of one-way streets and parallel road segments, the network is a directed multigraph, where each edge cost is travel time in seconds.

[Point to the parallel edges `k=0` and `k=1` on the right diagram.]

## Slide 3 — System & Traffic-Cost Pipeline

**Target: 0:55–1:40**

**Script**

Our pipeline has three stages. First, we combine OpenStreetMap topology with Kaggle's historical Ho Chi Minh City traffic dataset, using a five-tier fallback when observations are sparse.

Second, we freeze a single historical snapshot—weekday eight to eight twenty-nine—so all algorithms are compared on identical edge costs.

Third, our web interface and evaluation suite call the same routing engine, while keeping point-of-interest access lines separate from driving cost.

## Slide 4 — Custom A* on Directed Multigraphs

**Target: 1:40–2:25**

**Script**

Our custom A-star search prioritizes nodes by path cost plus heuristic estimate. It tracks exact edge keys so parallel segments are reconstructed accurately, and allows node reopening when a cheaper path is found.

We evaluate three heuristics: Safe, Traffic, and Zero—where Zero is equivalent to Dijkstra's algorithm.

[Point to the `k=1` edge, the reopening arrow, and the three heuristic tags.]

## Slide 5 — Safe Heuristic: Proof & Edge Check

**Target: 2:25–3:25**

**Script**

To guarantee optimal routes, our Safe heuristic divides great-circle distance by a snapshot speed upper bound, V-upper, set just above the maximum edge speed at seventy-four point zero seven four kilometres per hour.

Because map lengths and rounding can still create subtle mismatches, both our CLI and server verify an edge-level condition on every traversable edge when loading the graph. Across all one hundred and three thousand eight hundred and ten edges, we found zero violations, proving consistency and admissibility.

[Point to the `0 / 103,810` card and the triangle-inequality line.]

## Slide 6 — Traffic Heuristic & Counterexample

**Target: 3:25–4:15**

**Script**

We also tested an experimental Traffic heuristic that scales the Safe estimate by local outgoing congestion.

However, multiplying by a factor above one can overestimate the true remaining cost. In this simple counterexample, Safe gives nine point nine nine seconds on a ten-second edge, while Traffic jumps to eleven point two four seconds—violating both admissibility and consistency. Traffic is therefore used only for empirical comparison.

[Trace the `u -> t` edge and the red cross.]

## Slide 7 — Safe A* Cuts Expansions by 46.1%

**Target: 4:15–5:10**

**Script**

We evaluated all three heuristics under seed forty-two across twenty origin-destination pairs and twelve audit goals using reverse Dijkstra.

All three matched independent NetworkX Dijkstra costs on twenty out of twenty pairs. Safe A-star expanded forty-six point one percent fewer nodes than Zero—one hundred and three thousand five hundred and ten versus one hundred and ninety-two thousand and ninety-nine—with zero admissibility or consistency violations.

[Point to the Safe bar, then the zero violations in the audit table.]

## Slide 8 — Why Local Congestion Did Not Win

**Target: 5:10–6:05**

**Script**

Interestingly, Traffic expanded slightly fewer nodes on seventeen of the twenty pairs, yet expanded one point three percent more nodes overall.

The main reason is this long cross-city query from POI one hundred eighteen to one hundred thirty-eight, where Traffic expanded two thousand three hundred and forty-five extra nodes. Across twelve goals, Traffic also produced one hundred and fifteen thousand six hundred and twelve consistency violations, showing that local congestion does not bound downstream corridors.

## Slide 9 — Route Case Study: Safe vs Traffic

**Target: 6:05–7:05**

**Script**

Here is a recorded UI comparison from Bách Hóa Xanh to Bến Thủ Thiêm on the same frozen profile. Both heuristics found the exact same fourteen point one kilometre route, taking thirty-three minutes and nineteen seconds in modeled time and matching Dijkstra.

Safe expanded nineteen thousand and three nodes, while Traffic expanded four point four nine percent more. We note that the displayed runtimes are single UI observations, and the "Real-time traffic" badge in the UI reflects a legacy label on frozen historical data.

[Point to pin A, pin B, and the zoomed Safe metrics card.]

## Slide 10 — Limitations and Future Works

**Target: 7:05–8:05**

**Script**

Our study has four main limitations. First, edge costs come from a frozen historical snapshot rather than live traffic, which motivates time-dependent arrival costs.

Second, only part of the network has exact street matches, and density is model-derived rather than measured.

Third, our benchmark covers twenty pairs and single UI screenshots, so multi-profile sweeps and repeated timing trials are needed. Finally, global V-upper weakens the Safe bound, so landmark-based ALT bounds and integrated access legs are natural next steps.

## Slide 11 — Conclusions

**Target: 8:05–9:00**

**Script**

In summary, on a forty-four thousand node Ho Chi Minh City road graph, Safe A-star cut node expansions by forty-six point one percent with zero edge-bound violations and twenty out of twenty Dijkstra matches.

Meanwhile, the experimental Traffic heuristic broke consistency over one hundred and fifteen thousand times and expanded more nodes overall. Validated Safe A-star therefore gives us both efficiency and a formal optimality guarantee.

Thank you. We will now transition to our second topic—or take questions after both talks.

---

## Q&A — Quick Reference Answers (20–35s each)

### 1. Why not use live real-time traffic?
Live feeds change continuously and require paid APIs or scraping, which makes controlled algorithm comparison non-reproducible. Freezing the historical Kaggle weekday 08:00–08:29 profile lets Safe, Traffic, and Dijkstra run on identical edge weights. (**Backup:** Slide 3, Q1)

### 2. Why is `h_safe` still traffic-aware if it uses global `V_upper`?
`V_upper` is computed from the maximum assigned speed in the active traffic snapshot (`74.074 km/h` for weekday 08:00–08:29), not a static constant. Switching snapshots updates both edge costs `g(n)` and `V_upper`, requiring re-validation. (**Backup:** Slide 5, Q1)

### 3. Why did `Traffic` match Dijkstra on 20/20 pairs despite 115,612 consistency violations?
Consistency is a sufficient condition for optimality on first pop, not a necessary condition on every instance. Our A* also allows node reopening, and `beta = 0.25` only perturbed priorities modestly—so it happened to recover the optimum on those 20 pairs without a general guarantee. (**Backup:** Slide 6, Slide 8)

### 4. Why did `Traffic` expand more nodes overall and run slower in the UI case study?
Local outgoing congestion `q_S(n)` can misguide search on long cross-city corridors (e.g., `+2,345` nodes on POI `118 -> 138`). In addition, `Traffic` scans outgoing traversable edges on every heuristic call, adding per-node overhead. (**Backup:** Slide 8, Slide 9, Q3)

### 5. How does this compare with OSRM / Contraction Hierarchies?
Contraction Hierarchies achieve millisecond queries on static weights, but changing traffic profiles invalidate shortcuts and require expensive preprocessing rebuilds. Profile-based Safe A* switches snapshots immediately and can be accelerated later using ALT landmarks. (**Backup:** Q4)

### 6. What about POI access distance and parallel/non-routable edges?
Non-routable edges are excluded from both A* and Dijkstra. Parallel segments between the same node pair are preserved via `(u, v, edge_key)` parent pointers. POI-to-road snapping distance (150 POIs to 147 nodes) is displayed separately and excluded from driving time. (**Backup:** Slide 3, Q2)

## If Rehearsal Runs Long

- On Slide 3, skip the detailed explanation of density/flow and access lines.
- On Slide 6, skip the numerical counterexample details—just state that multiplying by `> 1` breaks both admissibility and consistency.
- On Slide 10, read only the first sentence of each row and the UI transparency note.
