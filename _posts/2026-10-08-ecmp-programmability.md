---
layout: post
title: "Unlocking ECMP Programmability for Precise Traffic Control"
date: 2026-10-08
paper_authors: "Y. Liu, Y. Xiao, X. Zhang et al."
paper_venue: "NSDI 2025"
paper_url: "https://www.usenix.org/conference/nsdi25/presentation/liu-yadong"
week: 3
tags: [data center networks, equal-cost routing, network reliability]
---

## Key Idea
Datacenters use ECMP to distribute flows over equal-cost paths by hashing each flow's five-tuple. That works well in aggregate, but Precise Traffic Control (PTC) tasks, like moving a flow off a failed link or probing every path, conflict with its randomness. With basic ECMP, existing solutions change a header field to force a rehash and hope it gives a new path, which can take many tries. P-ECMP instead uses ECMP groups, an often-ignored feature that commodity switches already support. Each switch stores several port lists, and a selector in the packet header picks one. By changing the selector, a host can move its flow to a different path in one step, or choose the exact next hop.

## Critique
Failover is the only use case tested in production. The other five are evaluated mostly in ns-3 simulation, and segment routing gets just a header size comparison. The design also leaves the selector field to each operator and assumes there are enough unused header bits. The authors use the 6-bit DSCP field, which is meant for QoS, but exact next-hop control needs up to 24 bits on their production topology. That is more than DSCP or the 20-bit IPv6 Flow Label can hold, and the Flow Label does not exist for IPv4 traffic. Finally, the failover experiments all use a dual-homed topology, which favors P-ECMP. With only two ToRs per server, a random re-path often lands on the failed one again, so ToR failures are the worst case for the random re-pathing baseline. A single-homed network has no second ToR to move to, and the paper shows no failover results for that setup.

## Connections
ECMP fits the HOT framework that I discussed in [my Week 1 post on Internet Invariants](/2026/09/29/internet-invariants/), where [Misa et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.22) explain that a system optimized for common cases breaks in rare ones. Random hashing is tuned to spread load over equal-cost paths, but it breaks when a flow is stuck on a bad link. P-ECMP also handles failures differently from the approach in [my Week 2 post on WAN Degradation](/2026/10/06/wan-degradation/), where [Arzani et al.](https://dl.acm.org/doi/10.1145/3718958.3754348) build Raha, a passive tool that an operator runs ahead of time to find likely failures and decide where to add capacity. P-ECMP is active instead: each host moves its own flows within milliseconds of a failure, with no central entity involved.
