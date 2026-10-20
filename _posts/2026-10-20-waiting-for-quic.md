---
layout: post
title: "Waiting for QUIC: Passive Measurements to Understand QUIC Deployments"
date: 2026-10-20
paper_authors: "J. Mücke, M. Nawrocki, R. Hiesgen et al."
paper_venue: "CoNEXT 2025"
paper_url: "https://dl.acm.org/doi/10.1145/3768988"
week: 4
tags: [deployment analysis, hypergiant infrastructure, quic]
---

## Key Idea
Hypergiants like Google, Meta, and Cloudflare set up QUIC in their own ways but rarely share the details, and scanning their servers to find out is intrusive and often unwelcomed. The authors instead use backscatter: replies that QUIC servers send to fake source addresses that happen to belong to a network telescope. QUIC encrypts most of the handshake, but the unencrypted headers still show how each provider configures things like retransmissions and packet sizes. The most useful field is the server connection ID, which providers fill with routing information for their load balancers. From it, the authors can spot provider servers hosted in other networks and, for Meta, count the load balancers behind each virtual IP (VIP) address.

## Critique
The method is presented as fully passive, but it still needs active steps. Confirming which servers belong to which provider requires connecting to them and checking their certificates. Counting load balancers only works for Meta, because Meta shared what its connection IDs mean. For other providers, the passive data can show that the IDs have a pattern but not what it encodes. I was also surprised how little attention the security risk gets. Attackers could use connection IDs to send traffic to a single load balancer, and the load balancer count could help them estimate how much traffic it takes to overload a site. That seems serious, yet the paper gives it one short paragraph, with no call to action and no mention of notifying the operators.

## Connections
Like [Baltra et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.4) in [my Week 2 post on Partial Reachability](/cs440-blog/2026/10/01/partial-reachability/), this paper sees the Internet from a limited vantage point, so its numbers are only estimates. The provider choices also fit the HOT framework from [Misa et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.22) in [my Week 1 post on Internet Invariants](/cs440-blog/2026/09/29/internet-invariants/): they tune QUIC for fast connections, like Meta's quick retransmissions and the rare use of Retry packets, at the cost of being more exposed to DoS attacks. Finally, both this paper and [McClure et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.12) from [my Week 4 post on congestion signals](/cs440-blog/2026/10/15/no-signal-to-rule-them-all/) rely on information carried in packet headers. In-Network Telemetry (INT) puts it there on purpose to help congestion control, while QUIC is designed to hide it, yet the connection IDs still give some away.
