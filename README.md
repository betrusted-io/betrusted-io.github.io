# Our Core Principles

Betrusted is designed around the principles of transparency,
simplicity, completeness, and self-sealing.

### Transparency

Transparency is the bedrock of trust. Understanding what makes a thing
tick gives us an evidence-based reason to trust that it works as
intended. Betrusted is unique in that instead of a black-box CPU
chip, we start with inspectable, open-RTL CPU cores, delivered in the form of an
FPGA or an IRIS-inspectable chip. This means you no longer have
to accept on faith that a black epoxy rectangle contains precisely the
circuits it advertises.

### Simplicity

Simplicity addresses the reality of only having 24 hours in a
day. Even though the full source code for the Linux kernel and Firefox
is published, nobody has the time to personally review every release
for potential security problems; we simply trust that others have done
a good job, because we have no other choice. Betrusted is a platform
powerful enough to be useful for single tasks, while simple enough that
individuals or small teams could build it from scratch. But don't worry,
despite its simplicity, Betrusted’s computational capability is sufficient
for core security tasks such as authentication, instant messaging,
and even end-to-end encrypted voice calls.

### Completeness

Trust should begin at your fingertips and end at your eyes. Screen
grabbers and keyboard loggers mean that chip-only trust solutions,
such as TPMs, TEEs, and secure enclaves are insufficient; we need a
complete, end-to-end solution. Private keys are not the same as our
private matters, and until we can encrypt data directly to and from
our brains, the "analog hole" will be a real problem. Betrusted includes
the complete loop of components from the keyboard to the display. They
are curated for transparency and simplicity, thus minimizing the
attack surfaces that cannot be cryptographically secured.

### Self-sealing

“Three can keep a secret, if two of them are dead”. As Benjamin
Franklin acutely observed, relying on trusted third parties to
provision our keys is imprudent: self-sealing is the only way to keep
a secret. Betrusted requires no special tools, NDAs, or third-party
expertise to provision the system with secrets: it can generate and
seal its own private keys on-chip in a transparent and open
manner.

# Documentation & Support

* The [betrusted wiki](https://github.com/betrusted-io/betrusted-wiki/wiki) aggregates all the technical documentation for the platform.
* The [Precursor Matrix Space](https://matrix.to/#/#precursor.dev:matrix.org) is the channel to reach live humans for questions & answers.
* The [Xous Book](https://betrusted.io/xous-book/) is a deep-dive into the kernel architecture.
* The main [betrusted github repository](https://github.com/betrusted-io) is the place to hunt for anything you didn't find at the link above.
* The [IRIS link tree](https://bunnie.org/iris) aggregates all the published information on IRIS.

## Media

* Video: [39C3](https://media.ccc.de/v/39c3-xous-a-pure-rust-rethink-of-the-embedded-operating-system) Xous and Baochip
* Video: [38C3](https://media.ccc.de/v/38c3-iris-non-destructive-inspection-of-silicon) Introduction to IRIS
* Video: [36C3](https://www.youtube.com/watch?v=Hzb37RyagCQ) Introducing the core principles that motivate the hardware architecture
* Video: [LCA20](https://www.youtube.com/watch?v=_pIr3Q7gqNI) Evolution and planning of the overall project, including software layers
* Video: [FOSSNORTH20](https://www.youtube.com/watch?v=w8BA6_9HCzk) Precursor overview talk
* Blog: [Precursor SoC Tour](https://www.bunniestudios.com/blog/?p=5971)
* Blog: [Precursor Mainboard tour](https://www.bunniestudios.com/blog/?p=5942)
* Blog: [Other Precursor posts](https://www.bunniestudios.com/blog/?cat=71) hardware security evaluation, braille keyboard, hackability, mechanical and PCB design aspects.
* Blog: [Precursor + Renode](https://xobs.io/precursor-and-renode/)

## Other Tech Morsels

* [Avalanche Noise Source](/avalanche-noise) design notes
* [Curve25519 Engine](https://ci.betrusted.io/betrusted-soc/doc/engine.html) documentation

## Who is behind betrusted?

The betrusted-io github repository's people page [lists the
developers](https://github.com/orgs/betrusted-io/people) that have [elected to reveal their
participation
publicly](https://help.github.com/en/articles/publicizing-or-hiding-organization-membership).

The administrative contact for the betrusted.io project is [Andrew
'bunnie' Huang](https://en.wikipedia.org/wiki/Andrew_Huang_(hacker))
([@bunnie.org](https://bsky.app/profile/bunnie.org) bluesky/[@bunnie](https://social.treehouse.systems/@bunnie) masto/[blog](https://bunniestudios.com)).

<center><img src="https://nlnet.nl/logo/banner.png" width="20%"> <img src="https://nlnet.nl/image/logos/NGI0_tag.png" width="20%"></center>

The Betrusted team is funded in part through the [NGI0 PET
Fund](https://nlnet.nl/PET), a fund established by NLnet with
financial support from the European Commission's [Next Generation
Internet](https://ngi.eu/) programme, under the aegis of DG
Communications Networks, Content and Technology under grant agreement
No 825310.
