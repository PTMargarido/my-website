# CV refactor checklist

Goal: position as a senior engineer with a C++/Linux/embedded spike who gets dropped into unfamiliar parts of a system and ships. Never use the word "generalist". Every claim needs a bullet that proves it.

Files in scope: `pedro_margarido_resume.tex` and `content/_index.md` (keep both in sync).

## 1. Facts to supply (you)

- [x] C++ standard you actually write day to day (e.g. C++17/20), and the one used at ASML
Adopted and guided team to write C++ 11 and 14 in HBK and ASML (did a physical week course in C++11 to 17)
- [x] Hardware and RTOS the HBK work ran on (CPU family, DSP family, OS on the DSP side)
Hardware was custom boards running OpenWrt and real-time boards for signal gathering using SysBios
- [x] What the "Boost DSP optimisation, doubled throughput" really was: what was slow, what you changed, how measured
One of the main features was called eDrive which is a suite of complex calculations running in the DSP to calculate in real-time the efficiency of an eletric motor and some other important KPIs
- [x] A real number for the PR-review agent (bugs caught per N PRs in the pilot, judge precision, anything measurable). If none exists, decide to drop or soften the bullet
It was able to catch 5-15 (depending in the size of the PR) issues with criticality of low, medium, high based in internal documentation and rules.
- [x] Trading platform: end date, or a public link. Decide whether to keep the word "cryptocurrency"
Still on-going, internal project due to alpha. 
- [x] Which two comm protocols "XCP, IDDS" are (IDDS may be internal; if so rename to what it does)
I would prefer to remove this entry, since it is very niche protocols in automotive and airplanes.
- [x] Coverage bullet: keep 16% to 27%, or rephrase around number of tests added and modules covered
Still this was all before AI, so all this coverage increase was manual and with refactoring in between.

## 2. Stories to write (you, rough notes are fine)

Two or three cases of being handed something unfamiliar and cracking it. For each: what you were handed, what you didn't know at the start, what you found, what changed afterwards, rough numbers.

- [x] Story 1 (HBK, ideally a cross-layer bug hunt)
Going for whole tasks instead of bug hunts. Implemented the whole feature of transferring asynchronous calculated data from one board (RTOS) to another (OpenWrt) through shared memory and two ring buffers.
Can share the code to help the story, check merged PR (47bf690516d) in /home/pedro/work/hbk/perception/, hopefully you can help me rephrase this story better
- [x] Story 2 (ASML, something found with Valgrind or during the IAM work)
Took initiative as last integrator (integrating libpam in the main ASML Qt application to authenticate federated users), had to debug sssd, libpam and inform other teams of issues. One such issue was the IAM state (groups, etc) getting cached in the machine and sssd cache not being cleared. 
- [x] Story 3 (optional: OpenWRT takeover, TFVC to Git migration, or the trading platform)
Took the initiative to transform all modules to IaC from FPGA code to fpga builds, OpenWrt with full artifacts after builds such as iso, manifests, etc. Automated all manual processes. Trading platform stands as a full personal interest in creating a project at scale. 
- [x] Pick which two become CV bullets and which become site write-ups
Done: all three are site write-ups; the DSP transport, IAM integration and release-pipeline stories each got a CV bullet with a [write-up] link.

## 3. CV rewrite (Claude drafts, you review)

Drafted 2026-09-08. Items below are applied; review the wording in `pedro_margarido_resume.tex` and `content/_index.md`.

- [x] Summary: pattern-based, C++ spike named, range listed, no "generalist"
- [x] ASML: story bullet first, IAM/CRA bullet reframed as handed-problem to outcome, AI bullet with real number or removed
- [x] HBK: story bullet first, the two vague C++ bullets replaced, remaining bullets reordered so consecutive bullets show different layers
- [x] HBK: OpenWRT bullet reframed as takeover ("took over X with Y packages nobody had updated")
- [x] HBK: "TFVS" corrected to "TFVC"
- [x] HBK: SAFe Scrum Master bullet shortened or dropped
- [x] Altran: keep, tighten to one or two lines
- [x] Project: reframed as full ownership (infra, provisioning, Python services, C++ dashboard, three clouds), end date or link
- [x] Skills regrouped into five rows: Firmware and RTOS / Application C++ (standard named) / Build and quality / Infra and cloud / Tooling
- [x] Skills: drop C#, Neovim, Tmux, "Familiar / Exploring"
- [x] "9+ years" checked against the dates (Apr 2017 to now is 9y5m including the tester year)
- [x] Both files updated with identical wording

## 4. Site (`content/_index.md` and theme)

- [x] Drop the tmux/interactive-rebase knowledge-sharing bullet
- [x] Fix "such as," comma
- [x] Decide whether the site keeps mirroring the CV or adds a write-ups section
- [x] Write-up 1 (about 500 words: context, what you didn't know, how you found it, result)
- [x] Write-up 2
- [x] Write-up 3 (optional)
- [x] Link each CV story bullet to its write-up (links removed again on 2026-09-08: write-ups kept as hidden drafts for now)

### Write-ups are hidden drafts

The three pages under `content/writeups/` carry `draft: true`, so `hugo` skips them and the Write-ups section and menu entry do not render. To publish one later: remove its `draft: true` line, then optionally add a `[write-up](/writeups/<slug>/)` link back into the matching bullet in `content/_index.md` and an `\href` in the TeX. Preview drafts locally with `hugo server -D`.

## 5. Verify before sending anywhere

Verified 2026-09-08: TeX compiles to one page (via `podman run ghcr.io/xu-cheng/texlive-small` plus `tlmgr install preprint titlesec`), Hugo builds, write-up links render. The last four items are yours.

- [x] TeX compiles, fits on one page
- [x] Hugo builds, site renders both experience and write-ups
- [x] Every bullet answers: what were you handed, what did you ship, what changed
- [ ] Every number is one you can defend in an interview (see section 6)
- [ ] Read as a hiring manager for 30 seconds: does the top third say "C++ depth plus range"?
- [ ] Read as a C++ interviewer: is there any bullet you can't explain for five minutes?
- [ ] Commit

## 6. Claims to confirm before publishing (you)

- [ ] Component-owner bullet (UA, SM) added first under ASML on 2026-09-08; CM left out on request. Confirm naming the components publicly is fine.

- [ ] Roughening pass (2026-09-08): summary triplet removed, bullets given uneven shapes, most parentheticals folded in, "by hand" dropped from the coverage line, project title no longer says (personal), skills say "C++17/20 in personal work". TeX text height is now +1.3in with top margin -0.6in to hold one page.

Everything below was written from your notes plus the HBK git history. Confirm or correct each one.

- [ ] Boost is gone from the DSP throughput bullet; it now credits "rebalanced DSP core usage". Correct?
- [ ] "40k-sample harmonic vectors" comes from MAX_HARMONIC_ORDERS in the transport header. OK to state publicly?
- [ ] Naming TI C667x, SYS/BIOS, OpenWrt, PCI shared memory and "Genesis HighSpeed" publicly: check against your HBK NDA.
- [ ] IAM bullet now says lead engineer owning architecture and design (your note, 2026-09-08). The hidden draft write-up still frames it as "last integrator"; reframe before publishing.
- [x] SSSD cache detail removed from the bullet; keep it for interviews.
- [ ] ASML write-up: PAM, SSSD, Qt, "revoked group membership stayed cached" and "four teams" are stated. Check against your ASML NDA and soften if needed.
- [ ] PR-review agent: "5 to 15 severity-ranked findings per PR" and "F1 0.6" against the judge set (your figure, 2026-09-08).
- [ ] "C++20 coroutine dashboard": the dashboard really uses C++20 coroutines, not a library emulation?
- [ ] Release write-up says the FPGA build ran in "its own toolchain container" and build time went "from hours to about a third". Correct the mechanism and the numbers if not.
- [ ] Release bullet narrowed on request to "OpenWrt images and manifests built as artifacts". The hidden draft write-up still describes FPGA bitstreams, ISOs and FLOSS lists; trim it before ever publishing it.
- [ ] Release bullet and write-up now say on-premise build agents were provisioned with Terraform and kept tooled by Ansible (your correction, 2026-09-08). Packer stays in skills because fusion-infra has Packer image commits; drop it if it never reached production.
- [ ] Trading platform renamed to "Automated Trading Platform (personal)" with no mention of crypto. Keep?
- [ ] XCP/IDDS bullet removed as requested; JSON-RPC API kept inside the C++11/14 bullet.
- [ ] Static-analysis bullet now describes the JSON retrieval tool and the one-finding-per-commit LLM skill (your note, 2026-09-08); "about 1,000 mostly critical findings over 25 years" kept. The Valgrind bullet was dropped to keep one page; Valgrind stays in skills.
