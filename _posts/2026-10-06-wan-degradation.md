---
layout: post
title: "Raha: A General Tool to Analyze WAN Degradation"
date: 2026-10-06
paper_authors: "B. Arzani, S. Taheri, P. Namyar, R. Beckett, S. K. Kakarla, E. Jalilipour"
paper_venue: "SIGCOMM 2025"
paper_url: "https://dl.acm.org/doi/10.1145/3718958.3754348"
week: 2
tags: [traffic engineering, network performance analysis, network reliability]
---

## Key Idea
WAN operators need to know how much the network can degrade under failures. Existing tools limit the number of failures, assume a fixed demand, or only work for specific TE schemes, and none of them measure the drop relative to the healthy network, so they can miss the scenarios that hurt most. Raha instead searches over both at once: it looks for the likely failure scenario and traffic demand that together cause the biggest drop compared to the healthy network. It limits the search to failures above a probability threshold, so the results are scenarios an operator should expect to see. Microsoft used it on its own WANs to find risky scenarios and decide where to add capacity.

## Critique
Searching failures and demands together is convincing, because a failure that looks harmless under average demand can be the worst case under a different one. A tool that holds either one constant can miss the worst case and report the network as safe when it is not. But the only production network in the evaluation is Microsoft's own WAN in Africa. The authors designed Raha to be general, but the tests on public topologies borrow failure probabilities from Microsoft's data, and objectives other than their production TE get only limited experiments. So it is unclear how well it works for operators with different routing logic or less complete failure history.

## Connections
Raha somewhat reminds me of the HOT framework from [the Misa et al. paper](https://drops.dagstuhl.de/storage/01oasics/oasics-vol139-nines2026/OASIcs.NINeS.2026.22/OASIcs.NINeS.2026.22.pdf). Operators optimize a WAN for common failures and demands, since spare capacity is expensive. HOT says a system tuned like that performs well in the expected cases but can break badly outside them. Raha searches for those cases: combinations of failures and demands that are still likely but that the network was not planned for. I also see a similarity with [the Baltra et al. paper](https://drops.dagstuhl.de/storage/01oasics/oasics-vol139-nines2026/OASIcs.NINeS.2026.4/OASIcs.NINeS.2026.4.pdf) in how it views failure: the Internet is not just fully up or fully down, and neither is a WAN. [Baltra et al.](https://drops.dagstuhl.de/storage/01oasics/oasics-vol139-nines2026/OASIcs.NINeS.2026.4/OASIcs.NINeS.2026.4.pdf) measure that middle ground as peninsulas, and Raha measures it as partial degradation.
