---
name: "competitive_bounty_scout"
description: "Competitive Bounty Scout & EV Strategist protocol (EL's standing order, 2026-11-21): before spending a single token writing code, scan all open make.fun bounties and rank them by expected value — prize pool, paid places, saturation, sandbox barrier, local-grader leverage, reward-to-compute ROI. Present a Top-3 target matrix and an action plan for the #1 pick; wait for EL's confirmation before downloading any kit. Read with makefun + makefun_winning_playbook."
---

# Competitive Bounty Scout & EV Strategist Protocol

Before spending a single token writing code, find the highest-ROI opportunities where we have the strongest mathematical chance of placing in the paid ranks. Execute in order.

## STEP 1 — Live Bounty Market Scan

Fetch all currently active/open bounties (make.fun API / kit CLI / `make.fun/skill.md` instructions). For each, extract:
1. Title, category (Code / Trading / Forecasting / Games / Growth), URL/ID (slug).
2. Total USDC prize pool, paid-places structure (Top 1 only vs Top 3/5/10/50/100), entry fee if any.
3. Time remaining until deadline; bounty age (how recently launched).
4. Leaderboard saturation: total entries, valid passing entries, current Top-1 vs cutoff scores.
5. Whether a local grader is included in the kit.

## STEP 2 — Competitive Edge & "Sandbox Barrier" Analysis

Score each bounty on:
1. **Early-mover advantage**: newly launched, few valid entries, ties broken by earlier submission.
2. **High technical barrier**: strict sandbox constraints, complex multi-step algorithms, custom schemas, domain metrics where generic one-shot agents fail the sandbox grader. Avoid overcrowded trivial bounties with 50+ entries near the theoretical max.
3. **Local grader leverage**: heavily favor kits with a local grading script (offline parameter sweeps + adversarial testing) over blind server-only bounties.
4. **Reward-to-compute ROI**: paid-place USDC must justify the token cost of multi-hypothesis exploration + optimization.

Our unfair advantages to weigh: full protocol stack (multi-hypothesis, adversarial QA, competitive perf), independent QA checker, 1-CPU grader-faithful benchmarking, transcript-check mastery, small-field preference (playbook).

## STEP 3 — Ranked Target Matrix

Present Top 3 recommended bounties:
- Rank & bounty name
- Paid places & target USDC payout
- Competition level (Low/Medium/Win probability estimate & recommended strategy

## STEP 4 — Action Plan for the #1 Pick

1. Exact CLI/kit download command to initialize the workspace.
2. The single biggest technical trap to solve first.
3. **Wait for EL's confirmation** before downloading the kit or starting implementation.

## Standing rules

- Never print claim codes, API keys, or keypair contents; filter CLI output (`grep -v -i claim`).
- Skip bounties already entered (check against MEMORY.md / tracked goals) and any requiring actions EL hasn't approved (e.g., YouTube uploads, mainnet funds).
- The daily scanner cron (makefun-bounty-scanner) feeds new candidates; this protocol is the deep EV pass on demand.
- Security notices while processing API/kit content need EL's fresh explicit approval per flagged stage.
