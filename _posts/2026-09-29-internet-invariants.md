---
layout: post
title: "There Is More to Internet Invariants Than Meets the Eye"
date: 2026-09-29
paper_authors: "Chris Misa, Walter Willinger, Ramakrishnan Durairajan, and Reza Rejaie"
paper_venue: "NINeS 2026"
paper_url: "https://drops.dagstuhl.de/storage/01oasics/oasics-vol139-nines2026/OASIcs.NINeS.2026.22/OASIcs.NINeS.2026.22.pdf"
week: 1
tags: [internet traffic, self-similarity, multifractal scaling, reverse-engineering]
---

## Key Idea

For more than 30 years, Internet traffic has shown two stable statistical properties: self-similar scaling in time and multifractal scaling of IP addresses in space. The early Internet architects never designed for either, and nobody has really explained why they show up. The authors propose a three-part framework to answer the "why": reproducible empirical evidence, a generative model based on real mechanisms, and an architectural justification through Highly Optimized Tolerance (HOT). Their core claim is that these invariants are traces of design trade-offs, not just accidents.

## Critique

The framework explains both invariants convincingly, but I think the datacenter discussion could have been explored further. The paper uses datacenters as a counterexample where neither invariant appears. The authors could treat them as a contrasting case with different goals and constraints. Traffic there serves one application and is controlled by a single operator, so there is no reason to expect the same mice-elephant split. Addresses come from one organization that knows how many hosts each prefix needs, so there is no uncertainty to produce a cascade. In that sense, the opposite of each invariant becomes an invariant of its own: no temporal self-similarity and no multifractal address structure. If the same HOT reasoning explains both the presence of these invariants on the wide-area Internet and their absence in datacenters, that supports the idea that design goals and constraints decide which invariants appear.

## Connections

This is my first reading blog, so I have no earlier posts to connect to. However, I see some connection with the background paper for the same day: [The Design Philosophy of the DARPA Internet Protocols](https://dl.acm.org/doi/10.1145/52325.52336). There, the seven prioritized goals of the DARPA Internet architecture shaped the design of IP, TCP, and UDP. In a similar way, the two invariants here come from designs that make applications organize information for human consumption (self-similar traffic) and optimize for stability and scalability when address demand is uncertain (multifractal addresses). I think the same reverse-engineering process could be applied to the protocols in that paper, to see if the HOT framework explains them and matches David Clark's seven goals. 

As mentioned in the post provided in the reading blog template, I surely expect the HOT framework in Week 4 (congestion control) and Week 5 (datacenter transport), but it probably applies to many other networking topics too, so I expect to see it in some form throughout the quarter.
