---
draft: true
title: "Moving DSP results to Linux without dropping them"
date: 2020-12-16
weight: 1
summary: "How a harmonic-analysis feature broke the DSP-to-Linux transport, and the shared-memory ring-buffer redesign that fixed it."
context: "Hottinger Brüel & Kjær, Genesis HighSpeed data acquisition, 2020"
---

**What I was handed.** A data-acquisition mainframe with two very different halves. Up to seventeen recorder cards, each with a multi-core TI C667x DSP running SYS/BIOS, turned raw samples into results in real time. An OpenWrt Linux board on the other side of a PCI bus collected those results, served them to the desktop software and stored them. Results that came out at a fixed rate had a well-trodden path. Results that came out whenever a calculation finished, so-called asynchronous results, went through a much thinner path: each DSP core wrote into its own ring buffer in shared PCI memory, and the Linux side polled a write index and read whatever had arrived.

I had been at the company about two years, mostly on the Linux side and the build system. I had not touched the DSP transport code before.

**What I didn't know.** The feature I was building, harmonic analysis to IEC 61000, changed the shape of the data. Until then an asynchronous result was one value with a timestamp. A harmonic spectrum is an array, and a large one: up to forty thousand samples for a single result. The existing reader assumed a result always fitted in the space between the read position and the write index. The DSP side assumed the reader could always keep up, because it had no way to know otherwise. Neither assumption held once vectors were in play, and the failure mode was silent: the write index lapped the read position and the Linux side happily parsed garbage.

**What I found.** Two separate problems that had looked like one.

The first was on the reader. A vector could be larger than what was available in the ring buffer at any moment, so reading had to become resumable. I introduced a transfer record per core that tracked how many bytes of the current item had been copied out and where the next chunk should land, so a forty-thousand-sample spectrum could arrive over several polling rounds and still be delivered as one object. The record owned its partial buffer and moved, never copied, so a half-read vector could not be duplicated by accident.

The second was on the DSP. Every core now published a read-progress structure of its own back into shared memory, so the writer could see how far the reader had got and detect an overrun before it happened, rather than the reader detecting it afterwards. While in there I found that the DMA engine's maximum transfer length was set to the raw hardware limit, which is not a multiple of eight. Vectors made that matter. I aligned it and pinned both constraints with compile-time asserts so the next person could not undo it silently.

**What changed.** Harmonic spectra flowed end to end at full rate with no overruns, on every core count the hardware supported. The test file for the transport grew by about five hundred and fifty lines, covering partial reads, rollovers and overrun detection with mocked DMA and cache control, so the whole path ran under Google Test on a build machine without a DSP in sight. That test suite is what later let the team change the transport again with confidence.

The part I took away: the bug was not in either half. It lived in the contract between two boards written by two teams years apart, and nobody owned the contract. Reading both sides until I could draw the contract on a whiteboard was most of the work.
