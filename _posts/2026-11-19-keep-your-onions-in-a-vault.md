---
layout: post
title: "Don’t Get Caught, Keep Your Onions in a Vault"
date: 2026-11-19
paper_authors: "H. Ikram, R. Habib, M. Ali, Z. A. Uzmi"
paper_venue: "NINeS 2026"
paper_url: "https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.17"
week: 9
tags: [tor anonymity, hidden services, trusted execution environments]
---

## Key Idea
Tor hidden services (onion services) hide where a server is, but the person running one, the HS provider, has to stay online all the time, and anyone can connect to the service whenever they want. Many deanonymization attacks, such as clock skew, guard node discovery, and traffic fingerprinting, depend on exactly that. VaulTor moves the serving to a volunteer called a vault, which runs the provider's host program inside a Trusted Execution Environment (TEE) such as Intel SGX. The provider uses remote attestation to check that the vault runs its program unmodified, uploads the content over an encrypted channel, and then goes offline, coming back only now and then, possibly from different places, to update it. The TEE stops the vault owner from reading or changing the content or the service's private key, and firewall rules that force all enclave traffic through Tor stop the provider from learning the vault's IP. Clients reach the vault like any other onion service and only add a check of a certificate hash the provider advertises. On t he live Tor network, serving from a TEE adds little delay compared with a normal hidden service.

## Critique
The TEE keeps the vault owner from seeing the content, but the authors acknowledge that the deanonymization risk moves from the HS provider to the vault, and that a vault operator could still face legal risk for content they cannot inspect. In the old model, the person who published illegal content was the one exposed. With VaulTor, the vault is the target that stays online and reachable, so attacks on hidden services, including ones not discovered yet, would expose the vault owner instead. The paper suggests crypto payments or altruism as incentives, but it is unclear why anyone would take on that risk without knowing what they host. Onion services are used for illegal activity more often than the rest of Tor, so a vault is likely to end up serving such content. The paper compares vaults to Tor relays, but a relay only forwards traffic, while a vault stores and serves one service for as long as it is hosted. This ethical issue applies to Tor in general, but vaults make it more direct, and the paper does not address it in depth.

## Connections
[Baltra et al.](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.NINeS.2026.4) in [my Week 2 post on partial reachability](/cs440-blog/2026/10/01/partial-reachability/) found some addresses that are active but do not answer probes or show up in traces. Some of these could be Tor hidden service hosts, which only need to make outgoing connections to Tor and can block everything else. They could not be islands, though, since a host cut off from the Internet core cannot reach Tor either. VaulTor also helps in the situation Baltra et al. measure: if a provider's network loses part of its reachability, for example when a government cuts or restricts Internet access during political unrest, content already uploaded to a vault outside that network stays available.
