---
layout: page
title: Development Plan
permalink: /dev-plan/
---

# Development Plan

## Big Picture

betrusted aims to create a hardware system that features a secure
enclave tied directly to hardware input and output mechanisms that
eliminate, as much as possible, the possibility of a MITM between the
user and the secrets within the enclave. Signal's double-ratchet
protocol complicates the lives of network-snooping interlopers, but is
still vulnerable to keyboard loggers and screen scrapers. betrusted
should provide a simple solution for individuals in high-risk
situations to not only secure their private keys, but also to secure
the flow of information between the users and their private keys.

## Plan

The final vision of betrusted is a much larger project than can be
accomplished in a single product generation. In an ideal world, the
project would encompass technologies that facilitate end-user
verification of custom silicon. However, this vision might be a
few years out; until then, our strategy is develop a system that
operates inside an FPGA that can be self-sealed via on-chip eFuses.

Thus the general strategy is to take a three-step approach to system development:

1. **Alpha** Developer-oriented FPGA-based system. "Almost looks-like,
works-like" prototypes in the form factor of a phone case. Priced in
the few hundred dollar range, produced in small (100's)
quantities.

    Primary focus is on vetting the code base and validating the ASIC
    design parameters.  Concurrent variations testing several keyboard
    designs will also be evaluated. This phase is estimated to last
    roughly 2 years assuming resources afforded by a modestly
    successful crowdfunding campaign and adequate developer interest in
    the OS and UX aspects.

    Phase 1 prototypes will be able to provide some measure of
    physical tamper resistance, to the level that the off-the-shelf
    FPGAs used are resistant to efforts to read out their security
    keys. The software should offer hermetic remote-exploit resistance
    at the software level even against APTs.

    Phase 1 will iterate until the user experience is _just right_
    before moving to phase 2, because there is no second chance at
    phase 2.  It will cost a couple million dollars to spin the custom
    SoC.

2. **Beta** Custom SoC-based system for early adopters. Priced in the
$200-300 range, produced in sub-10k quantities. Tamper resistant,
usable battery life, thin form factor that is easy to carry around in
the form factor of a "phone case".

    Phase 2 validates HCI assumptions and physical form factor
    assumptions derived in phase 1 across a larger user base. As a
    result phase 2 aims to get into the hands of "early adopter" users
    and not just developers.  This phase will either need an
    astonishingly successful crowdfunding campaign or the assistance
    of grants from foundations to complete.  This phase is estimated
    to last roughly 2 years: 1 year for ASIC and product development;
    6 months for post-tapeout ASIC validation; 6mos - 1 year for
    incorporating user feedback, depending on the scope of changes.

    Phase 2 should also attempt to implement any physical mitigations
    required by the threat model for release hardware.

3. **Release** Consumer-ready system. Pricing will depend on demand.
Production in 100k quantities should drive a shelf price in the
$120-$180 range, with a potential for sub-$120 in 500k or higher
quantities. This system takes the learnings from the first two phases
and incorporates them into a truly "user-ready" experience.

### Threat model for release hardware

The device itself should be hardened against evil maid attacks by a
well-prepared adversary with temporary access to the hardware, but may
not be hardened against an adversary with unlimited physical access to
the device. In other words, should an attacker have access to a device
for several hours in a well-equipped lab, but the device needs to be
returned to the owner in a functional state (but perhaps with
significant evidence of tampering), no secrets should be divulged as a
result of said analysis. In other words, with high likelihood any
successful attempt to recover secrets from a device with direct
physical access should leave the device in a state where naked-eye
visual inspection can detect evidence of tampering.

However, should an attacker have unlimited access to the device and
can perform destructive measurements on the device, secrets may be
divulged (such as attacks that require direct access to the silicon
die). However, in this scenario the device owner should at least be
aware of the compromise once the device is returned.

The RTL of the ASIC will be open for inspection, but due to the
secrecy restrictions imposed by ASIC vendors the entire chip will not
be inspectable as a mask-level blueprint (besides, there is a TOCTOU
on the mask design -- no guarantee that the mask design submitted for
inspection matches the mask design that was actually printed by the
fab). Instead, the RTL shall be hardened against potential "hard-IP"
exploits with hardware ASLR. See [ASIC
hardening](https://github.com/betrusted-io/betrusted-wiki/wiki/ASIC-hardening)
for more details.

#### Navigation

* [Betrusted](/)
  * [HCI Rationale](/hci-rationale)
  * [Betrusted Architecture](/betrusted-architecture/)
  * **Development Plan**
  * [Avalanche Noise Source Design](/avalanche-noise/)
