---
layout: post
title: "Understanding Partial Reachability in the Internet Core"
date: 2026-10-01
paper_authors: "G. Baltra, T. Saluja, Y. Pradkin, J. Heidemann"
paper_venue: "NINeS 2026"
paper_url: "https://drops.dagstuhl.de/storage/01oasics/oasics-vol139-nines2026/OASIcs.NINeS.2026.4/OASIcs.NINeS.2026.4.pdf"
week: 2
tags: [internet, internet reliability, network outages, active measurements]
---

## Key Idea
The Internet is supposed to be one huge connected network, but disputes between ISPs, firewalls, CGNATs, and politicians leave some networks reachable from only part of it. The authors define the Internet core as the set of all active addresses that can bidirectionally reach more than 50% of the potentially reachable Internet, which guarantees at most one core. Using that definition, they also defined peninsulas (persistent partial reachability) and islands (cut off from the core), and developed algorithms to detect them: Taitao and Chiloe respectevely. They run both on Trinocular (6 vantage points, 5M /24 blocks) and RIPE Atlas (13k vantage points, 13 DNS root targets). Their result is that peninsulas are about as common as full outages, so they deserve more attention.

## Critique
The definition is clean and the work is impressive, especially given how hard this is to measure. But the data comes from two limited sources: one has few observers, the other has few targets. Both also work like snapshots, since each observer probes in rounds at different times instead of continuously and simultaneously, so brief events can be missed or misread. Because of that, the results are rough bounds, not firm numbers. The authors admit this in places and mention tightening the bounds as future work. Still, they never frame the paper as an early step, and there is no future work section or clear call to action on peninsulas. It also mentions partial outages but never explained how they differ from peninsulas, islands, and full outages.

## Connections
Partial connectivity looks like an Internet invariant. It has been observed for decades, and the paper also finds it inside ASes and ISPs, not only between them (Sections 5.3 and 5.4). It would be great to see the HOT framework from [the Misa et al. paper](https://drops.dagstuhl.de/storage/01oasics/oasics-vol139-nines2026/OASIcs.NINeS.2026.22/OASIcs.NINeS.2026.22.pdf) applied here. Peninsulas and islands may be a trade-off baked into [DARPA internet protocols design](https://dl.acm.org/doi/10.1145/52325.52336), which put survivability first and distributed management fourth. That made the network resilient to failures but left no authority to guarantee every network can reach every other. The Cogent and Hurricane Electric dispute shows this: each ISP is free to set its own peering policy, so a deliberate choice, not a fault.
