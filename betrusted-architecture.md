---
layout: page
title: Betrusted Architecture
permalink: /betrusted-architecture/
---

# Big Picture

betrusted.io aims to create a hardware system that features a secure
enclave tied directly to hardware input and output mechanisms that
eliminate, as much as possible, the possibility of a MITM between the
user and the secrets within the enclave. 

As a result, the system architecture prefers simple, minimalistic
input and output motifs that are easier to verify and more difficult
to MITM: physical keyboards (key switch matrix directly to enclave
pins) are preferred over captouch surfaces (electrode array driven by
a "controller" which is actually an MCU running opaque firmware);
black and white serial-interface displays (simple, visually
inspectible chip-on-glass controller IC) vs. full-color MIPI displays
(complex COG or PCB controllers with embedded frame buffers).

## Network Interface

An enclave should not include the attack surface of a full TCP/IP
stack.  Instead, the interface to the network is always delegated to
an unsecured, untrusted companion processor built using commodity
silicon. This companion processor is responsible for decapsulating
betrusted packets and dealing with stateful events such as
out-of-order arrivals or packet loss events.

Thus the sole interface exposed to the network is electrically an SDIO
port (which also supports SPI mode). betrusted is always the master of
the network port, although the network device can raise an interrupt
for attention. The packet format itself is TBD but it will feature
strict bounds information, mandatory intergity checking, and all
packets must arrive strictly in-order.

Removing the attack surface of the network interface from the enclave
also means it is easier to create variants of betrusted with your
favorite network interface, be it WiFi, Bluetooth, 3G, 4G, 5G,
Ethernet, carrier pigeon...

## Display and Keyboard

Please see the [HCI rationale](/hci-rationale/) for a discussion of
the display and keyboard choices.

## Custom SoC

### Driving Assumptions

F/OSS silicon proponents may argue for the use of cheaper chip
technologies, e.g. 130nm chips, which cost about an order of magnitude
less to produce. The primary motivation for this argument is greater
fab transparency, and ease of post-tapeout inspection.

**The problem is RAM**: to implement e.g. a secure enclave that's also
  capable of rendering a UI rich enough to handle the linguistic
  diversity of humans, several megabytes of RAM are required. As an
  initial rough estimate, about 4-8MiB of RAM can be realistically
  provisioned in a 40nm SoC (4MiB @ 0.242um2/bit => 8mm^2 silicon
  area, ~2.85mm/side) vs a 130nm SoC (4MiB @ 2.14um2/bit => 70mm^2
  silicon area => 8.4mm/side). For a chip to be sold for a few dollars
  a piece, the silicon area should be around 10mm^2.

Off-chip RAM is vulnerable to tampering and it's tough to encrypt
off-chip RAM as it adds latency and additional attack surface. So
ideally, we're picking a starting process where we can fit enough RAM
within the physical enclave to implement the entire UI layer.

Off-chip ROM is less concerning, assuming an eFuse + trusted root boot
ROM is available to validate the code on the ROM.

As a result, the initial target for the custom ASIC is a 40nm process.
Please see [ASIC hardening](https://github.com/betrusted-io/betrusted-wiki/wiki/ASIC-hardening)
for a discussion of how we plan to mitigate the issues of fab transparency
and post-tapeout inspection engendered by going with such a process.

## Developer FPGA System

The goal of the developer FPGA system is to validate the RTL code base
for the custom SoC. This, in essence, means also doing user testing
and application testing, along with adequate red-team analysis of the
RTL to ensure it meets minimum reasonable expectations. Of course, no
system can be perfectly secure, but ideally betrusted.io should pluck
as much low-hanging fruit before taping out silicon.

Creating a real-life usable FPGA development system is a challenge:
FPGAs consume orders of magnitude more power and perform orders of
magnitude slower than similarly specified secure enclaves.

Faced with this trade-off, there are basically two directions we can
go: low power, or high performance.

1. **Low power** The advantage of the low power route is we get a
  better physical exemplar of the final device. In other words, lower
  power means a thinner battery and longer standby runtime at a lower
  cost. However, this comes at the cost of computational
  performance. A RISC-V CPU implemented in the largest iCE40 FPGA (the
  HX8k) along with peripherals required for betrusted.io can run at a
  core speed of around 30MHz with a 32-cycle multiplier, 2kiB I-cache,
  no D-cache, and 10kiB of fast SRAM (RISC-V "lite" config) with a
  power of around 2mW. The rest of memory would require about 2-4
  cycles latency to access for any external SRAM that has a static
  current footprint under 1mA (SRAMs <30ns access time usually run at
  around 10ns Taa and consume at least 20mA static current).

    A quick review of the Signal messaging protocol reveals that 3 DH
    key generation events must happen per double-ratchet, and a single
    DH keygen event takes [about 300ms with a 16MHz Cortex
    M0](https://github.com/Emill/P256-cortex-ecdh) that presumably has
    a single cycle multiplier (don't know of many embedded M0's that
    don't feature that). So at a very minimum, the DH operations alone
    will take about 1 second of CPU time **if** it could all run out
    of fast RAM and we had a single cycle multiply, but the iCE40
    implementation doesn't; data RAM is about 2-4 cycles and
    multiplies take 32 cycles. So realistically, the low-power iCE40
    route will yield a per-message throughput rate of about one per
    every 2-3 seconds.

2. **High Performance** The advantage of the high performance route is
we get a better firmware exemplar of the final device. In other words,
the algorithms and memory size more closely matches what would be
expected from the full-custom SoC version of the chip. Initial
estimates show a Spartan7 implementation of a RISC-V CPU with 4kiB
I-cache and D-cache, single-cycle multiplier, and about 128kiB of fast
SRAM can run around 125MHz peak speed around 216mW, and can
clock-throttle to run around 10MHz at a power of around 90mW (of which
80mW is static).

    In this scenario, it seems that the CPU could crunch through the
    double-ratchet to yield a per-message throughput rate of about one
    every 60-100ms or so. This is a much more user-tolerable time for
    messaging. Furthermore, the CPU has sufficient performance to
    implement test feature such as voice calling that could be very
    compelling to end users. The downside is that the two orders
    magnitude increase in static power translates to a standby time of
    weeks shrinking to about a day, while requiring a battery on the
    iCE40 that's perhaps just over 1mm thick to a battery on the
    Spartan7 that's around 3mm thick.

In the end, it's our estimation that a couple extra mm of thickness
while achieving a full day of standby time is preferable to developers
over a system that's a bit thinner and can standby for a couple weeks
but can run only a very limited set of algorithms and very slowly at
that.

### Hybrid Solution

The final system architecture we've arrived at is actually a bit of
both the Spartan7 and the iCE40 options. The current architecture uses
a Spartan7 as the cryptographic enclave-oid-in-RTL-only, with an iCE40
as a power management co-processor. In this scenario, the Spartan7 is
powered up whenever UI manipulations and messages need to be
processed, but otherwise is shut down with a hard-power-down load
switch. The iCE40 is always-on, but it cannot modify the UI in any
way; it merely monitors the input sources (keyboard and wifi network
interrupt) for incoming events and sequences the orderly power-on of
the Spartan7 enclave.

This combo seems to present a reasonable compromise between extremely
long standby times and fast cryptographic processing performance, at
the expense of a higher BOM and slightly higher system
complexity. Recall that it is explicitly not a goal for the FPGA-based
dev system to be resistant or tolerant to an evil-maid style attack,
so splitting the power and computational responsibilities across two
cryptographically unlinked (but still trusted) CPUs should not
compromise the primary mission of the developer-oriented FPGA system.
