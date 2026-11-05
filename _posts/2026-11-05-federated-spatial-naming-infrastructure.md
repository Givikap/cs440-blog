---
layout: post
title: "OpenFLAME: A Federated Spatial Naming Infrastructure"
date: 2026-11-05
paper_authors: "S. Bharadwaj, Z. Ma, I. Liang, M. Farb, A. Rowe, S. Seshan"
paper_venue: "NINeS 2026"
paper_url: "https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.20"
week: 7
tags: [geographic naming system, augmented reality, robotics]
---

## Key Idea
Spatial applications such as AR and robotics need detailed indoor and outdoor maps, but centralized maps like Google and Apple Maps mostly cover outdoor spaces, and indoor maps are costly to survey and often private. OpenFLAME lets independent parties host their own map servers for a region, grouped into map zones that can overlap and delegate to sub-zones. Regions are expressed as S2 cells, converted to DNS names called geo-domains, and looked up through new MAPSERVER, MAPZONE, and MAPDS records wrapped in TXT records, so existing DNS servers and caches can be reused. Because clients stay in a small region, most of the roughly 40 geo-domain queries per discovery are answered from the DNS cache, so the authors find discovery manageable, and the federated routing and reverse geocoding are slower than a centralized map but still reasonable.

## Critique
The system runs on top of existing DNS, but wrapping its records in TXT records and allowing zones to overlap add complexity. An overlap means a geo-domain can have several MAPZONE records, so the client has to follow every delegation chain itself instead of one, which complicates its logic and increases query resolution time and network load. The evaluation reflects this in the roughly 2x median routing time and 1.67x reverse geocode time compared with a centralized map. The authors also do not discuss why large map providers would participate. Google and Apple maps support their ads and other revenue, so registering their data on an open platform could reduce that revenue, yet the design depends on them as root maps for geocoding and routing. Google does provide an API, which the authors' prototype uses for outdoor 3D tiles, but it requires an API key and charges once usage passes a free tier. It is unclear who would pay as the number of users grows, and whether paid root maps keep the barrier low for the smaller map operators the paper targets.

## Connections
OpenFLAME fits the HOT framework from [Misa et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.22) in [my Week 1 post on Internet Invariants](/cs440-blog/2026/09/29/internet-invariants/): the authors needed DNS-like behavior (hierarchy, delegation, caching, and a chain of trust), and DNS, which was built for it, already provides it. Like CATE in [my Week 6 post on Carbon-Aware Routing](/cs440-blog/2026/11/03/carbon-aware-routing/), OpenFLAME also extends an existing system (DNS here, OSPF there) with new information and gives up some path optimality for another goal: sharded routing has a 1.12 median distance ratio in exchange for federation, while CATE routes on carbon-based link costs and turns off links in exchange for lower emissions.
