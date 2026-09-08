---
draft: true
title: "Turning a manual release into pipeline code"
date: 2021-06-01
weight: 3
summary: "FPGA builds, OpenWrt images, ISOs, manifests and FLOSS lists produced on every build, on build agents kept in step by Terraform and Ansible."
context: "Hottinger Brüel & Kjær, 2019 to 2022"
---

**What I was handed.** A product line whose software release depended on a handful of people and a wiki page. The firmware for a recorder card was three things built three ways: FPGA bitstreams from vendor tools on one engineer's workstation, DSP code from the TI toolchain, and an OpenWrt image for the Linux board assembled by hand. Around those came the desktop software installer, an ISO for the mainframe, a manifest saying which versions of what went into a release, and a list of open-source components with licences that customers were entitled to. Each step worked. Each step also lived in someone's head, and a release took days of a senior engineer's time.

I was the person who had just made the build system reproducible with CMake, so the release process landed on me as the next thing nobody owned.

**What I didn't know.** Most of it. I had not built an FPGA bitstream or an OpenWrt image before, and the build machines were snowflakes: Windows and Linux boxes configured over years with tools installed by whoever needed them.

**What I found.** The problem was not any single step. It was that no step produced a durable, named artifact that the next step could consume. Each handover was a person copying files. So the design was to make every stage emit artifacts and make every next stage consume only artifacts.

Stage by stage: the FPGA pre-processing and build moved into the pipeline with its own toolchain container, so a bitstream was a build output like any other. The OpenWrt image build was scripted end to end, with the resulting image, its package manifest and its licence list published together. The desktop installer, the mainframe ISO and a USB-stick variant were generated on every pull-request build, not only at release time, so a broken packaging step showed up the same day it was introduced. Manifests were generated from the artifacts rather than typed. Everything landed on the network store under a predictable path per build.

The snowflake machines went last. The on-premise build agents, Windows and Linux, were provisioned with Terraform and kept current with Ansible, so every worker carried the same toolchain versions and a build agent could be rebuilt from a commit instead of a memory. In the same pass, the pipelines themselves were parallelised and made incremental, which took the main build from hours to about a third of that.

**What changed.** A release became a pipeline run that anyone on the team could start, and the artifacts from any pull-request build were the same artifacts a release would ship. The senior engineers got their days back. When a customer asked for the licence list of a firmware version two years old, the answer was a path, not an archaeology project.

The part I took away: automation of a manual process is mostly an exercise in finding where the humans were doing the integration, and giving that job to a file with a name.
