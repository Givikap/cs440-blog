---
layout: post
title: "Tight Loops, Smooth Streams: Responsive Congestion Control for Real-Time Video"
date: 2026-11-12
paper_authors: "P. Karimi, S. Fouladi, V. Sivaraman, M. Alizadeh"
paper_venue: "NINeS 2026"
paper_url: "https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.9"
week: 8
tags: [real-time video congestion control transport protocols video rate control low-latency video communication tight feedback loop]
---

## Key Idea
Real-time video, like video calls and cloud gaming, needs a rate controller that quickly matches the video bitrate to the network. The one in WebRTC, Google Congestion Control (GCC), adapts slowly because the encoder cannot hit its target bitrate on every frame. GCC therefore keeps a lot of headroom below the link rate, which wastes bandwidth, and when the link rate suddenly drops, frames already encoded for the old rate take longer to send, so latency jumps. Delay-based congestion control algorithms (CCAs) like Copa adapt within a few round trips, but they assume a backlogged flow that always has data to send, while an encoder produces one frame per interval and often has nothing to send. Vidaptive sits between an unmodified encoder and the CCA. It sends video at the CCA's rate and fills any gap with low-priority dummy packets so the flow looks backlogged, then sets the encoder's target bitrate to a fraction of the CCA's rate, chosen from recent frame send times. On cellular traces this gives higher quality and lower tail latency than GCC, with slightly higher median latency and no codec changes.

## Critique
The evaluation uses mostly cellular traces, plus three emulated Internet paths with constant capacity, one of them Wi-Fi. It seems that no wired path is tested, so it is unclear whether Vidaptive improves anything on a stable link or only adds extra traffic. The design also has a cost: dummy packets keep the CCA's feedback loop running, but they keep a small standing queue, which is why median latency is slightly higher than GCC's. On variable links a large share of the traffic is dummy packets that carry no video, and they exist only so the flow looks backlogged. The authors' test with the dummy packets removed shows they raise the video bitrate, but the paper does not measure how a padded flow shares a bottleneck with other flows, and it only claims fairness through the underlying CCA. The only baseline is one version of GCC, so the comparison with other deployed systems is limited. Lastly, many figures sit pages away from the text that discusses them, which is annoying.

## Connections
This relates to [McClure et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.12) in [my Week 4 post on congestion signals](/cs440-blog/2026/10/15/no-signal-to-rule-them-all/), which found that end-to-end signals like RTT work about as well as in-network ones. Vidaptive does not add a new signal either. It changes the sender's setup, by padding with dummy packets, so the delay signal that CCAs already use keeps working for real-time video. It also fits the HOT framework from [Misa et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.22) in [my Week 1 post on Internet Invariants](/cs440-blog/2026/09/29/internet-invariants/): GCC is tuned for low latency at the cost of utilization, while Vidaptive lets users choose the tradeoff. Finally, like [LeoCC](/cs440-blog/2026/11/10/leo-satellite-dynamics/) in my Week 7 post, it makes Copa work in a setting where Copa's assumptions break, and both do this by adding probing traffic: LeoCC pings the PoP and Vidaptive sends dummy packets.
