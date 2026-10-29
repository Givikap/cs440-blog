---
layout: post
title: "DistTrain: Addressing Model and Data Heterogeneity with Disaggregated Training for Multimodal Large Language Models"
date: 2026-10-29
paper_authors: "Z. Zhang et al."
paper_venue: "SIGCOMM 2025"
paper_url: "https://dl.acm.org/doi/10.1145/3718958.3750472"
week: 6
tags: [large language models, multimodal models, distributed training]
---

## Key Idea
Multimodal LLMs chain three modules: a modality encoder (e.g., ViT for images), an LLM backbone, and a modality generator (e.g., Stable Diffusion). Frameworks like Megatron-LM train them with monolithic model orchestration: all three modules share the same parallelism settings, and data preprocessing runs on the training machines. Since the modules take very different amounts of compute time, and samples vary widely in size and preprocessing cost, GPUs sit idle in pipeline bubbles and some become stragglers. DistTrain gives each module its own GPUs and parallelism settings, chosen by a solver, and moves preprocessing to separate CPU nodes that also reorder samples to balance the load, achieving 54.7% Model FLOPs Utilization (MFU) on 1172 GPUs and up to 2.2x the throughput of Megatron-LM.

## Critique
The solver optimizes a cost model built from a sample of the training data and linear interpolation. The paper never reports how accurate this model is or compares its choice with the best setup measured on real GPUs. Preprocessing and reordering run on extra CPU nodes that are not counted in the GPU comparisons, yet these changes improve MFU and throughput by just 1.03–1.11x. The communication brokers between modules may also become a bottleneck: in Megatron-LM, each GPU sends directly to its counterpart in the next module, while DistTrain funnels this traffic through brokers, ironically aggregating communication in order to disaggregate the modules. Finally, only text and images are tested, so it is unclear how DistTrain handles more modalities.

## Connections
DistTrain fits the HOT framework from [Misa et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.22) in [my Week 1 post on Internet Invariants](/cs440-blog/2026/09/29/internet-invariants/): Megatron-LM is tuned for text LLMs built from identical transformer layers and becomes an inefficient monolith when other modalities are introduced. DistTrain also runs on a RoCEv2 RDMA network and sends preprocessed data to the GPUs over RDMA, the kind of network that [Li et al.](https://dl.acm.org/doi/10.1145/3718958.3750480) make more reliable under bursty AI traffic in [my Week 5 post on RDMA Reliability](/cs440-blog/2026/10/27/rdma-reliability/). Finally, DistTrain builds on and compares against [Megatron-LM](https://dl.acm.org/doi/epdf/10.1145/3458817.3476209), the background paper for this week.
