---
layout: page
title: HCI Rationale
permalink: /hci-rationale/
---

# HCI Rationale

The betrusted HCI spec needs to thread a needle between usability
and feature creep. Too many features, and the system becomes
impossible to audit; too few features, and the system becomes
unusable.

The purpose of this page is thus to lay out the core principles
and assumptions that drive the HCI rationale, so as to provide
a razor for shaving off unnecessary features.

## Principles and assumptions

The core requirement that drives the HCI target rationale is the
facilitation of secure communication between individuals in their
native tongues. An English-only ASCII interface, while simple, would
impair the adoption of betrusted in a significant fraction of
important use cases.

A Signal-style chat client as of early 2019 serves as an exemplar of
what would be nice to achieve. A layer providing asynchronous voice
messages or possibly fully synchronous voice calling helps to
accommodate languages that lack
[IME](https://en.wikipedia.org/wiki/Input_method) support as well as
individuals who are illiterate. Voice support also helps provide
a minimum viable product for users speaking languages that have
yet to receive developer support for a fully-functional input method.

Bitmap images are considered a "nice to have" feature that can most
likely be included without introducing significant additional attack
surface. However, the camera which takes the original image is likely
in an unsecured domain, so until a camera can be included within the
enclave's footprint image sharing can not be hermetically secure.

Video formats are initially ruled out due to the complexity of the
codecs, as well as the sheer computational and memory demands of
implementing streaming video; the enclave would end up more a video
processor than an enclave! This does not mean to rule out video ever
being a feature of the system, but at least for the FPGA dev board and
initial SoC implementation, video support is definitely out of scope.

To sum up the "why voice, but not video" question: voice is
computationally easy, works asynchronously, has a low bit rate, and
provides a backstop for users that lack a high-functioning input
method. The effort-benefit tradeoff is quite favorable. Video is a
couple orders of magnitude more computationally difficult than voice,
requires synchronous, high-bitrate connections, and provides
incremental communication benefits over voice.

## Physical keyboard

betrusted calls for a physical keyboard. The main driving reason
is ease of verification. Only simple wires go between the switches and
the enclave, allowing everyday users to compare against a photograph
of a properly constructed unit to find irregularities.

A usable virtual keyboard will require a high-quality multitouch
surface. All known multitouch surfaces of sufficient quality employ
an embedded CPU which is entirely closed source.

Developing a high quality, open-source multitouch surface is outside
the scope of betrusted. However, should one become available, it would
influence the decision to go with a physical keyboard.

A secondary reason for a physical keyboard is simplicity of supply
chain logistics. High quality captouch surfaces are largely
proprietary parts with few sources. This makes the parts difficult
for independent builders to obtain, and also greatly increases the
difficulty of building customized derivatives of betrusted.

## Black and white memory LCD

An over-arching requirement for a secondary device meant
to be carried along side your existing mobile device is to make
it as thin and hassle-free as possible.

Ideally, betrusted is just a few mm thin, and it should run for days
without requiring a charge. The envisioned place for betrusted is
integrated into the screen protector of a case for your existing
phone, so that it doesn't feel like you are carrying a second device
around.

This user experience assumption drives display selection.

Based on this target experience here is a list of candidates and why
they were not selected:

* Retina-style IPS displays. LCDs from this generation almost
universally employ MIPI, a proprietary link protocol. The displays
themselves must embody a frame buffer and significant black-box
silicon. Furthermore, they consume a significant amount of power.
Finally, the enhanced resolution of retina displays greatly increases
the computational burden placed on the enclave.

* AMOLED displays. Has all the problems of retina-style IPS displays,
but with a highly proprietary supply chain.

* EPD, or E-ink. EPD is low power, lends itself to extremely thin
hardware implementations, and has good readability. It is ideal for a
secondary companion-type device to a phone. On the downside, the
innate persistence of the screen is a security problem. Furthermore,
the EPD supply chain is even more proprietary than most LCDs.

* Monochrome LCD display modules; OLED modules. Various websites
  feature simple, easy-to-use, and fairly open display modules.
  However, all modules surveyed with sufficient resolution to be
  usable for displaying more than a couple lines of text are fairly
  thick or awkwardly-sized; and all modules that are thin and suitable
  for a stow-away enclave lack a usable resolution.

* TFT displays. The [Winstar
  WF35UTYAIDNN0](https://www.winstar.com.tw/products/tft-lcd/module/wf35utyaidnn0.html)
  features high-contrast IPS technology, and is a good balance between
  openness and user experience. As of writing, a comprehensive
  controller IC spec
  ([ILI9488](https://focuslcds.com/content/ILI9488.pdf)) is available
  for download. However, the power consumption is quite high, and the
  additional processing burden of handling this display's resolution
  may be beyond the capability of the enclave.

The final solution picked for the initial betrusted display is the
[Sharp Black and White Memory LCD part number
LS032B7DD02](https://www.sharpsma.com/products?sharpCategory=Memory%20LCD&p_p_parallel=0#).
This black and white display features a 200ppi resolution, an
extremely thin (0.71mm) profile, extremely low "display on" power
(250uw), goes completely blank when power is lost, and has an
extremely simple SPI interface with a trivial programming
model. Although the main spec sheet for the display is closed, the
spec is simple enough that a C header file will easily encapsulate all
the nuance of the device, and the display controller is a
chip-on-glass item that can be pattern-verified with an optical
microscope. This display blends the best of EPD and TFT. Furthermore,
the black-and-white display economizes on RAM space, which is a precious
commodity inside a cryptographic enclave.


## HCI Key Open Questions

* How do we implement multi-lingual IMEs and unicode font rendering?
  In other words, how do we accommodate the linguistic diversity of a
  global userbase without introducing unecessary attack surfaces?
* How do we implement the secure rendering of bitmap images? In other
  words, what's the trade-off between a sufficiently simple image
  format with a small attack surface and a sufficiently rich image
  format for interoperability?
* How to implement a secure chat? In other words, how do we get Signal to fit inside a secure enclave?
* How to implement a peer to peer secure voice call and/or audio snippets in chat?

#### Navigation

* [Betrusted](/)
  * **HCI Rationale**
  * [Betrusted Architecture](/betrusted-architecture/)
  * [Development Plan](/dev-plan/)
  * [Avalanche Noise Source Design](/avalanche-noise/)
