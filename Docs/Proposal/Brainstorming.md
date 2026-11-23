# Vojta's project view

- the details about the project is spell out in ../Lectures/01.pdf on page 6 to 9
- the ../Lecture folder contains also other learning material

## Constraints
- I want something, where AI produce a code
- in one of language I know (order from best familiar to lowest):
    - C#, C, Python, php, C++, javascript/type script
- Has to fit 6 to 7 hours work per week
    - at total, there is 8 weeks, so that makes approx 40 hours of work total
- able to run and develop it on Linux

## Brainstorming

- contribute to open source project
    - [Setnicka obchodka](./lsmf2025-obchodka)
        - implement economy model
        - so no longer filling manually random numbers, but have set them and
          only updates the item names
        - written in go, but thats OK
    - operating systems from Matfyz?
        - [repo](https://github.com/D-iii-S/msim)
        - help with some open request
    - Linux?
        - no, denied idea
- start brand new project
    - the game "smenkovaci" for the SMF
        - wildly wibe-coded [version](lsmf2026-smenkovaci)
        - I will create the software for the game, so I can deploy it to real VPS
        - the rules of the game are in Pravidla subfolder
    - GUI tool which adds "no permission to use by AI" to pdf (may other formats)
        - as [this](https://pdfa.org/defending-your-pdfs-from-ai-scraping/) seems too low level and user not friendly approach
- replicate a publish clain (but has to be programmable)
    - may some benchmarking?
    - well, probably drop completly the idea about replicate a publisch claim

## Notes from exploration with Claude (2026-11-17)

### What the course checks
- project grade: specification 25 %, **verification 35 %**, understanding 25 %, disclosure 15 %
    - => "how do we know it works" must be **measurable** (tests, mutation score, merged PR, numbers)
- slide 7 checks: code you didn't write? who tells you it works? can you measure something? fits 6-7 h/week **each**?
- **M0 due 24.11.** - one page: problem, who cares, how you'll know it works, the code, who does what, biggest risk
- budget: 6-7 h x 8 weeks is approx 50 h (not 40); in a pair approx 2 x 50 h

### Top 3 ideas in short

**1. Směnkovací - backfill tests, mutation testing, VPS hardening** (C# / TypeScript)
Our auction web app for the LŠMF game works but was vibe-coded and has no tests. We let the agent write
tests for the core game logic (bids, money limits, auction end, stage changes), measure their quality
with mutation testing (Stryker.NET), improve them from the surviving mutants and measure again.
Then we make the app safe to run on a public VPS (HTTPS, secrets, rate limiting, backups).
*Who cares:* LŠMF orgs. *How we know it works:* mutation score before/after, passing tests, running deployment.

**2. MSIM - contribute to the Matfyz machine simulator** (C)
MSIM is the MIPS/RISC-V simulator used in the Matfyz OS courses. We fix a real bug (#114, `wfi` does not
wake up on a locally enabled interrupt) and/or add a feature that helps students debug their kernels
(#58 explain mode: tell *why* an exception like TLB refill or page fault happened), each with tests,
and send them as PRs to the upstream repo.
*Who cares:* OS-course students and the maintainers. *How we know it works:* reproducer tests,
maintainers' review, merged PR (or a documented reason why not).

**3. PDF "no AI" GUI tool** (Python or C#)
A simple desktop tool: pick files, click one button, and they are marked "not allowed for AI training /
text and data mining" in the standard machine-readable metadata (TDMRep in XMP), without the user
knowing anything about PDF internals. Possibly also images, EPUB, HTML.
*Who cares:* authors who publish PDFs. *How we know it works:* the metadata validates, files stay valid
(`qpdf --check`), existing metadata is preserved (watch pikepdf #760); we cannot prove that scrapers
respect the mark.

### Evaluation of my ideas
| Idea | Works? | Estimate (me alone) | Fits? |
|---|---|---|---|
| MSIM contribution | yes, strong | 35-50 h | yes |
| Směnkovací - harden + test, VPS-ready | yes, strong | 40-55 h | yes, as a pair |
| PDF "no AI" GUI tool | yes, but weak verification | 30-45 h | yes |
| Obchodka economy model | maybe | 45-60 h | tight |
| replicate a published claim | dropped | - | - |

- **Směnkovací** (decision: harden + test, no rewrite)
    - already approx 3.7k lines C#/TS, 70 commits, Docker Compose, LAN deploy, **zero tests**
    - fits course idea "backfill tests, then mutation-test them" (see below)
    - backfill tests for bid/auction/stage logic (`NotesController.cs`, `AuctionService.cs`,
      `StageController.cs`), incl. concurrent bids and "bid <= free money"
    - mutation score with Stryker.NET before/after
    - VPS hardening: HTTPS/reverse proxy, secrets, rate limiting, backups
    - risk: next real use is summer 2027, "who cares" = orgs
- **MSIM** (C, https://github.com/D-iii-S/msim) - maintainer merges student PRs, has new test harness (PR #113)
    - #114 WFI doesn't wake on locally enabled interrupt when globally disabled - real confirmed bug (ask if taken)
    - #58 explain mode (why TLB refill / page fault happened) - medium-large, good main deliverable
    - #110 halt in unrecoverable states - medium
    - #39 randomization (expose races in student kernels) - medium, measurable
    - warm-ups: #23 iregch ignored, #7 cp0d register names, #33 dumpdev alignment,
      #14 ddisk busy bit (check open PR #115)
    - risk: PR may not get merged before 05.02. (negative results count)
- **PDF tool**
    - can verify: metadata written correctly, PDF not corrupted (`qpdf --check`), existing metadata preserved
    - cannot verify: that scrapers respect it
    - trap: pikepdf #760 - `open_metadata()` wipes Document Info
    - could grow: more formats (images XMP/IPTC, EPUB, HTML)
- **Obchodka**
    - Setnička wants it, I have write access
    - repo is private - ask instructor whether it counts as "open source PR"
    - Go is not my language; needs defined metrics (bot simulation: no arbitrage loop beating X %, prices in bounds after events)
    - current model: base prices x per-market modifiers x events, all manual in `configuration.yaml` (`game/prices.go`)

### Other open-source candidates (issues checked 2026-11-17)
- **ReCodEx** (MFF, PHP + JS) - small, active, reachable maintainers
    - web-app: #694 menu overlaps content in narrow mode, #682 infinite loading on test rename clash,
      #643 React crash with many uploads, #612 show student's total points
    - api: #536 empty ZIP from download-best-solutions, #535 wrong `files` type
- **Stryker.NET** (C#, mutation testing) - #3975 `--version` fails, #3891 detect `is not`, #4008 Razor compile
    - has "AI assisted" label - check their AI policy
- **mutmut** (Python mutation testing) - #577 (good first issue), #626 test edit doesn't invalidate cache, #627 `**` glob
- **pikepdf** (Python) - #760 (pairs with PDF idea: fix library bug, then build tool)
- **QuestPDF** (C#) - #1495 read XMP metadata via QPDF, #1489 `FontFamily` ignores families after 16th
- HelenOS (C) - active, but no usable GitHub issues found
- most issues are weeks old - comment on the issue before starting

Ranking: Směnkovací approx = MSIM > PDF tool (+ pikepdf fix) > Obchodka

### Backfill tests + mutation testing (explained)
- **backfill tests** = write tests afterwards for existing untested code (pin current behaviour)
    - problem: such tests (esp. AI-generated) often run code but check little; coverage looks good anyway
- **mutation testing** = tool (Stryker.NET / mutmut) injects small bugs ("mutants") one at a time, runs the test suite
    - a test fails => mutant **killed** (good); all pass => **survived** (tests are blind there)
    - **mutation score = killed / all mutants**
- example `NotesController.cs:159`: `if (req.Amount <= note.CurrentBid)` -> mutant `<` accepts a bid equal
  to current bid; survives unless some test bids exactly the current bid => missing test found
- **before/after**: first test batch -> score (e.g. 45 %) -> fix tests from survivors -> score again (e.g. 85 %)
    - report: numbers, which survivors were real gaps, agent time/cost, what agent got wrong
- watch out: equivalent mutants (can't be killed - exclude and justify), run time (limit to backend logic)

### Assumptions (not stated by me, verify)
- pair project, total budget approx 2 x my hours; Christmas weeks = 0 h
- "deploy to real VPS" = harden existing app, not start over (confirmed)
- private repos (Obchodka, Směnkovací) are acceptable - **ask instructor**
- pdfa.org article is about TDMRep/XMP metadata (could not open it)
- target OSS projects allow AI-assisted PRs with disclosure - check each
- listed issues not claimed by anyone yet
- estimates include reading code, transcripts, report, demo prep

### Open for the meeting with partner
- partner's languages / interests (may change ranking)
- M0 candidate: "Směnkovací: backfill tests + mutation score + VPS hardening"; backup: MSIM #114 / #58
