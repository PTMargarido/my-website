---
draft: true
title: "Being the last integrator: federated login in a machine GUI"
date: 2025-09-01
weight: 2
summary: "Wiring PAM and SSSD into a large Qt application for the Cyber Resilience Act, and the stale-cache bug that would have kept revoked access alive."
context: "ASML, 2025"
---

**What I was handed.** The main Qt application that operators use on ASML machines had to authenticate users against the company's identity provider instead of local accounts, on every machine type, to meet the EU Cyber Resilience Act. Several teams owned pieces: the identity platform, the machine's operating system image, the directory integration and the application itself. I was the last integrator. My job was to make it actually work inside the application, on real machines, and to find out which of the pieces did not fit.

I had joined ASML a few months earlier. I knew the application code and Qt. I had never configured SSSD, and my PAM knowledge was what any Linux user picks up by accident.

**What I didn't know.** How much of the login path lives outside the application. The Qt side is small: a PAM conversation, a result, a session. Everything interesting happens in the PAM stack and in SSSD, which talks to the identity provider, caches what it learns, and answers questions like "is this user in this group" long after the network request that produced the answer.

**What I found.** The integration worked on a fresh machine and misbehaved on machines that had been running for a while. Tracing the PAM conversation and SSSD's own logs showed the identity state on the machine, group membership in particular, was being cached and never invalidated. A user whose access had been revoked centrally could still log in with their old groups until the cache expired or the machine was rebooted. For a security feature that is the one property you cannot get wrong, and it would not have been visible from the application's point of view at all.

Alongside that, there was the usual long tail of integration issues that only appear once all the pieces are on one machine: PAM module ordering, the difference between the identity as the provider names it and as the application expects it, and error paths that the application had to present sensibly to an operator with no terminal.

**What changed.** I wrote up each issue with a reproduction and the layer it belonged to, took them to the owning teams, and followed each one to closure rather than handing over a list. The cache behaviour was fixed on the platform side so revocation took effect promptly. The application shipped with federated login on all machine types.

The part I took away: on a feature that crosses four teams, the person holding the last piece is the only one who sees the whole path. That is not a coordination chore; it is where the real bugs are.
