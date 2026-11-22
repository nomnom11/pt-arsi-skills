---
name: "multi_hypothesis_protocol"
description: "Multi-Hypothesis Exploration & Optimization Protocol (EL's standing order, 2026-11-21) for competing to WIN 1st place in make.fun bounties: deconstruct the grader/metric, build and benchmark 3 fundamentally different approaches, pick the winner or a hybrid, then relentless metric optimization + adversarial stress testing, then final sandbox/bundle verification. Read with makefun + makefun_winning_playbook + bounty_qa_checker before every bounty build or improvement pass."
---

# Multi-Hypothesis Exploration & Optimization Protocol

Passing basic tests is NOT enough. Never stop at a local optimum. Engineer the highest-scoring solution possible. Execute in order; do not skip steps.

## STEP 1 — Grader & Metric Deconstruction

1. Read all bounty rules, `skill.md`, and local grading/evaluation scripts in the workspace (kit RULES, competition.yaml, harness/scorer code).
2. State explicitly: the exact scoring formula, tie-breakers, runtime/memory limits, sandbox constraints (CPU/RAM, offline, flags like `python3 -P -s`).
3. Identify what separates an average passing score from a Top-1 leaderboard score on THIS metric (check the leaderboard: #1's signal, house baseline, paid places, field size).

## STEP 2 — Multi-Hypothesis Exploration (3 distinct approaches)

Do NOT rely on a single standard approach. Design, implement, and benchmark **three fundamentally different** strategies (e.g., A: fast heuristic/greedy; B: exact/dynamic-programming/mathematical optimization; C: advanced statistical/ML/ensemble or low-level optimized):

1. Build + test **Approach A** → run the local grader/benchmark → record exact score + execution time → commit (`git commit -m "exp-A: <summary> | score: <val>"`).
2. Reset to clean baseline, build + test **Approach B** → record score + time → commit (`exp-B: ...`).
3. Reset to clean baseline, build + test **Approach C** → record score + time → commit (`exp-C: ...`).

Notes for our setup:
- Approaches are built by builder sessions (transcript evidence!): entrypoints authored via write/edit, ≥2 write/edit messages per entrypoint, large data files via write tool — see playbook transcript traps.
- Benchmark like the grader: 1 CPU (`taskset -c 0`), cold caches, grader-exact commands. Smoke test first, full test in background (speed rules).
- If the bounty format makes 3 full approaches impractical (e.g., judge/artistic bounties), run 3 distinct *variants* of the decisive dimension (model, layout, game systems) and judge them with the same discipline — record why.

## STEP 3 — Comparison & Winner Selection

Present a comparison table: Score, Runtime, Memory, Failure Modes for A/B/C. Select the highest-scoring approach — or build a **Hybrid** combining the best components of the top two — as the main branch.

## STEP 4 — Relentless Metric Optimization & Stress Testing

1. **Error & bottleneck analysis**: inspect exactly where the winner loses points or spends the most CPU (profile; per-case/per-slice breakdown).
2. **Iterative tuning**: at least 3 targeted optimization cycles (hyperparameter sweeps, algorithmic pruning, caching, data-structure upgrades). Keep a change ONLY if the local grader score strictly improves; revert immediately if it drops.
3. **Adversarial stress test**: generate synthetic worst-case / noisy / max-scale inputs locally; verify no crash and no score degradation.

## STEP 5 — Final Sandbox & Bundle Verification

1. 100% offline compatibility, relative paths only, locked dependencies, deterministic output across two consecutive runs.
2. Muse app agents: session logs/usage intact for `--muse-usage` bundling (usage rows written before sealing/bundling; compress shown after last source write when packed).
3. Run the make.fun bundle check (`makefun check`) — zero errors before finishing.
4. Independent QA pass per `bounty_qa_checker` before submit; EL's explicit approval at the submit gate; security notices need EL's fresh approval per flagged stage.

## Standing rules that always apply

- On-Chain Safety Rails (AGENTS.md) — no exceptions.
- Playbook tactics (perf playbook, transcript traps, grader quirks) are part of Step 1/4 knowledge.
- Record each bounty run's Step 1 deconstruction + A/B/C table in the bounty workspace so the next pass starts warm.
