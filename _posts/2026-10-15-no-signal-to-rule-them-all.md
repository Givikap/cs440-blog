---
layout: post
title: "No Signal to Rule Them All: A Systematic Analysis of In-Network Congestion Signals"
date: 2026-10-15
paper_authors: "S. McClure, N. Dukkipati, S. Ratnasamy, S. Shenker"
paper_venue: "NINeS 2026"
paper_url: "https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.12"
week: 4
tags: [congestion control, in-network telemetry, data center networks]
---

## Key Idea
Datacenter switches nowadays can report In-Network Telemetry (INT), such as queue length or link utilization, to hosts through packet headers. Header space is limited and this logic lives in switch hardware, so vendors must choose which signals to support. To compare signals without tying the answer to one congestion control algorithm (CCA), the authors use R+, their extension of the Remy CCA generator, to train the best CCA it can find for each signal set and throughput/delay utility, then evaluate it in ns-3. The surprising result is that "any signal will do" on average: CCAs with INT score within 3% of ones using only end-to-end (E2E) signals like RTT and send/receive rates. INT only helps in extreme scenarios, such as very high or low load, where queue signals favor throughput and link signals favor delay.

## Critique
The main result depends on R+ being a good generator of optimal CCAs, so "any signal will do" could also mean R+ cannot utilize INT fully. To check this, the authors repeat their experiments with PCC-RL, a deep reinforcement learning CCA, and add INT to BBR. Neither settles it, since PCC-RL scores lower overall and the BBR thresholds were tuned by hand. The signal space is also narrow: only queue length and link utilization in four combinations, and the authors themselves say more signals need to be tested. Finally, all results come from simulation with a single bottleneck and at most 64 senders. Datacenters care about tail latency and incast, which are exactly the extreme cases where INT helps, so judging signals by average utility may undersell it.

## Connections
The results fit the HOT framework from [the Misa et al. paper](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.22) in [my Week 1 post on Internet Invariants](/cs440-blog/2026/09/29/internet-invariants/). Each signal leads to a CCA tuned for some cases at the cost of others: Link keeps delay low by staying just under full utilization but struggles with many senders, while Queue keeps the link busy at the cost of some delay and does best with few senders. Like [Arzani et al.](https://dl.acm.org/doi/10.1145/3718958.3754348) in [my Week 2 post on WAN Degradation](/cs440-blog/2026/10/06/wan-degradation/) showed, an average can hide the cases that matter most. Similar to P-ECMP from [Liu et al.](https://www.usenix.org/conference/nsdi25/presentation/liu-yadong) in [my Week 3 post on ECMP Programmability](/cs440-blog/2026/10/08/ecmp-programmability/), INT also relies on packet header fields for its functionality.
