---
layout: post
title: "BISCAY: Practical Radio KPI Driven Congestion Control for Mobile Networks"
date: 2026-11-17
paper_authors: "J. Larrea, T. Shreedhar, A. Niemi, A. Sefiane, M. K. Marina"
paper_venue: "NINeS 2026"
paper_url: "https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.15"
week: 8
tags: [cellular networks, congestion control, LTE/5G]
---

## Key Idea
On a phone, the cellular link is usually the bottleneck, and its bandwidth changes rapidly. End-to-end congestion control algorithms (CCAs) like CUBIC and BBR have to estimate the bandwidth from loss or delay, so they either build up a queue at the base station or leave bandwidth unused. The phone's modem, however, already knows how much it may send, because the base station tells it through scheduling grants. BISCAY reads these radio key performance indicators (KPIs) from the modem, sets the congestion window from the granted bandwidth and the RTT, splits it evenly across the phone's flows, and falls back to BBR when the bottleneck moves to the wired path. To get the KPIs in time, the authors build OpenDiag, a kernel module that makes Qualcomm modems report KPIs about 100 times more often than existing tools. In emulation and on real 4G/5G networks, BISCAY gets similar or higher throughput than other CCAs with much lower delay.

## Critique
BISCAY is designed and tested only for 4G and 5G, and 3G and older networks are left out. The design is also described mainly for 5G, even though much of the world still relies on 4G. OpenDiag relies on Diag, the diagnostic interface built into Qualcomm modems, and nearly all tested devices use Qualcomm chips, so phones with MediaTek or Samsung modems may not provide the same KPIs. The paper also never clearly states which KPIs BISCAY uses or why they are useful for congestion control. The design offers two ways to compute bandwidth from different KPIs, and the granularity test of OpenDiag uses RSRP, a signal strength KPI that neither method uses. Finally, most results come from Pantheon, where BISCAY is given bandwidth computed from KPIs recorded with the same trace that sets the link's capacity, so it has close to perfect knowledge of the link. The real-world tests use one phone, compare only against CUBIC and BBR, and the private network had no other users.

## Connections
[McClure et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.12) in [my Week 4 post on congestion signals](/cs440-blog/2026/10/15/no-signal-to-rule-them-all/) found that no single signal works best everywhere, but some signals do better in specific cases, such as In-Network Telemetry (INT) under very high or low load. Radio KPIs are another such case: they help when the cellular link is the bottleneck, and when it is not, BISCAY falls back to BBR's end-to-end estimate. Both BISCAY and [LeoCC](/cs440-blog/2026/11/10/leo-satellite-dynamics/) from my Week 7 post rely on link information that end-to-end CCAs do not see. Starlink terminals do not report this information, so the LeoCC authors infer reconfigurations from ping gaps and suggest that terminals report them in the future. The BISCAY authors instead query the phone's modem, which already holds this information. Both fall back to BBR or BBR-like behavior when the bottleneck is on the wired path.
