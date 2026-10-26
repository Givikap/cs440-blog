---
layout: post
title: "Revisiting RDMA Reliability for Lossy Fabrics"
date: 2026-10-27
paper_authors: "W. Li, X. Liu, Y. Zhang et al."
paper_venue: "SIGCOMM 2025"
paper_url: "https://dl.acm.org/doi/10.1145/3718958.3750480"
week: 5
tags: [RDMA NICs, reliability, lossy fabrics, lossless control plane]
---

## Key Idea
Traditional RDMA NICs (RNICs) rely on Priority Flow Control (PFC) to make Ethernet lossless, which brings head-of-line blocking, deadlocks, and short distance limits. Newer RNICs like IRN work without PFC, but they can't tell out-of-order arrivals caused by packet-level load balancing apart from genuine losses, and they still fall back to timeouts. DCP changes both the switch and the RNIC: a congested switch trims the packet's payload and sends the header-only (HO) packet through a prioritized queue, so the sender learns of each loss and retransmits exactly that packet. Since every packet then arrives exactly once, the receiver can simply count packets per message instead of tracking them in bitmaps. DCP recovers from loss up to 72x better than a Mellanox CX5 and cuts AI job completion time by about 40% compared to IRN and MP-RDMA.

## Critique
DCP needs both custom switches and custom NICs, yet it still requires changes on the host: every queue pair (QP) gets a retransmission queue (RetransQ) in host memory. The NIC manages this queue without the CPU, but it adds complexity, and DCP still keeps a coarse timeout as a fallback for lost HO packets or failed links. The authors admit the queue cannot live in hardware: "since the number of HO packets can be large, this queue must be implemented in software rather than hardware." This contradicts their own goal of a hardware-oriented design. Finally, DCP marks its four packet types with two bits of the IP ToS field, which is meant for QoS and may clash with how operators already use it.

## Connections
DCP fits the HOT framework from [Misa et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.22) in [my Week 1 post on Internet Invariants](/cs440-blog/2026/09/29/internet-invariants/): bursty AI workloads exposed the weak spots of datacenter networks and called for a more complex design, which ironically also helps regular workloads like WebSearch. Its signaling mirrors dcSim from [my Week 5 post on Simulate Before Sending](/cs440-blog/2026/10/22/simulate-before-sending/), as both give small control packets their own prioritized queue marked with ToS bits, and an HO packet is an in-network signal like those in [my Week 4 post on congestion signals](/cs440-blog/2026/10/15/no-signal-to-rule-them-all/). DCP claims to work with ECMP but is tested almost only with adaptive routing, so it says little about the issues from [my Week 3 post on ECMP Programmability](/cs440-blog/2026/10/08/ecmp-programmability/). Finally, DCP uses [DCQCN](https://dl.acm.org/doi/10.1145/2829988.2787484), the background paper for the same week, as its congestion control.
