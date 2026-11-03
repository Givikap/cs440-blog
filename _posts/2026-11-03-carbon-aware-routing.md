---
layout: post
title: "Exploring the Benefits of Carbon-Aware Routing"
date: 2026-11-03
paper_authors: "S. El-Zahr, P. Gunning, N. Zilberman"
paper_venue: "CoNEXT 2023"
paper_url: "https://dl.acm.org/doi/10.1145/3629165"
week: 6
tags: [green networking, internet carbon emissions, routing, sustainability]
---

## Key Idea
Routers in different regions run on grids with different carbon intensity (gCO2/kWh), which also changes over the day, but link-state routing only considers delay, hop count, or bandwidth. The paper defines OSPF link costs from energy metrics (typical power, an energy label, incremental dynamic power per Mbps), carbon intensity, and combinations of them, and evaluates them in ns-3 on the BT (UK) and GEANT (Europe) topologies with historical carbon intensity data. Combining carbon intensity with incremental dynamic power works best, saving 3.71% carbon on BT during the day. The authors then propose CATE, a centralized offline algorithm that uses the traffic matrix to also shut down low-utilization links, which saves the idle power of their ports, achieving 14.49% carbon reduction during the afternoon. Gains shrink to 1.81% in the evening because downstream traffic from nearby caches has short paths and fills most links, and with routers that have a high idle-to-dynamic power ratio. The authors conclude that routing alone is not enough and that vendors must lower idle power consumption.

## Critique
All routers are assumed to be powered by the regional grid, even though a site may use a different source, such as only gas while the grid combines several. The authors acknowledge this but do not evaluate it, and renewables also have manufacturing emissions, which the paper does not consider. The simulated routers have an idle-to-dynamic power ratio of 1:7, but [Barroso and Hölzle](https://ieeexplore.ieee.org/document/4404806), the background paper for this week, report that network switches have a dynamic range of only about 15% of peak power, so most of their power is idle. The authors' own sensitivity analysis shows that CATE's carbon savings drop to about 1% at a 4:1 idle-to-dynamic ratio, so real gains are likely much smaller than reported. The scheme also needs extra hardware (smart PDUs), more traffic to advertise link state, and a predicted traffic matrix, all with their own carbon cost, and it ignores QoS. CATE is a greedy algorithm, and the results come from a simulation with only two seasons for BT and one for GEANT. Lastly, since the authors conclude that replacing routers with low-idle-power ones is the best approach, it is unclear that the added routing complexity is worth it.

## Connections
Raha from [my Week 2 post on WAN Degradation](/cs440-blog/2026/10/06/wan-degradation/) looks for the failures that hurt a WAN the most so operators can prevent them, while CATE deliberately turns off up to 32% of BT's links, which removes redundancy. CATE only handles unexpected failures with an online step that re-enables links, and running Raha on the topology CATE produces would show how much risk the carbon savings add. CATE also relates to [my Week 2 post on Partial Reachability](/cs440-blog/2026/10/01/partial-reachability/), since it only checks that the graph stays connected, but a failure or stale link state could still leave some routers reachable from only part of the network, like a peninsula. Finally, the paper rounds the energy label into 7 buckets so that more paths have equal cost and ECMP can split traffic, as in [my Week 3 post on ECMP Programmability](/cs440-blog/2026/10/08/ecmp-programmability/). But CATE computes its paths with Dijkstra and sends each flow along one path, so despite the ECMP-friendly labels, ECMP is not actually used.
