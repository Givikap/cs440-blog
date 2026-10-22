---
layout: post
title: "Simulate Before Sending: Rethinking Transport in Datacenter Networks"
date: 2026-10-22
paper_authors: "D. Straussman, I. Keslassy, A. Shpiner, L. Liss"
paper_venue: "NINeS 2026"
paper_url: "https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.19"
week: 5
tags: [datacenter networks, transport protocols, AI training, lossless networks]
---

## Key Idea
AI training traffic is bursty, so commodity datacenter networks drop packets, and retransmissions delay the collective completion time (CCT). Existing solutions either drop packets, cannot see congestion inside the network, or need non-commodity switches. In dcSim, each Data packet (the actual payload) first reserves its path with a small Sim probe on a random ECMP path. Switches give Sims their own small, strict-priority queue, rate-limited so Sims and Datas take turns on the link. A Sim that hits a full queue is dropped, as its Data would have been. Otherwise, a Sim-Ack returns, and the host sends its next Data on that path a fixed time (RTT<sub>max</sub>) after the Sim. In simulation, dcSim has zero loss, up to 10% lower CCT than alternatives, and 45% of their CCT on an oversubscribed network.

## Critique
The paper never mentions [DCTCP](https://dl.acm.org/doi/10.1145/1851275.1851192), even though it also runs on commodity switches, keeps queues short with ECN, and was built to handle many short messages next to bulk flows, much like the Sims here. The "commodity switches" claim also hides some cost: every port and host needs four priority queues plus a token bucket, and hosts track Datas in four states. The packet types are marked with IP ToS bits, which are meant for QoS. Sim-Acks also take an unrelated ECMP path through small buffers, so a lost Sim-Ack wastes a successful reservation until a timeout.

## Connections
dcSim gives each Sim a random path using ECMP rehashing, which I discussed in [my Week 3 post on ECMP Programmability](/cs440-blog/2026/10/08/ecmp-programmability/). There, [Liu et al.](https://www.usenix.org/conference/nsdi25/presentation/liu-yadong) replaced this rehash-and-hope approach with P-ECMP, which dcSim could use to steer a retried Sim away from paths it already knows are congested. A dropped Sim is also a congestion signal, which ties to [my Week 4 post on congestion signals](/cs440-blog/2026/10/15/no-signal-to-rule-them-all/). [McClure et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.12) found that In-Network Telemetry (INT) mostly helps in extreme cases, and dcSim likewise gains most in oversubscribed networks. Finally, dcSim fits the HOT framework from [Misa et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.22) in [my Week 1 post on Internet Invariants](/cs440-blog/2026/09/29/internet-invariants/), since it is tuned for large, symmetric AI collectives and may be fragile for short or one-way flows.
