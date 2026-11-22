---
name: "competitive_perf_engineer"
description: "Competitive Performance Engineer protocol (EL's standing order, 2026-11-21): when core logic works and passes edge cases, Rank #1 on a tight make.fun leaderboard is decided by tiny margins — profile, sweep parameters, micro-optimize, and ratchet-verify (keep only strict improvements). Runs after multi_hypothesis_protocol Step 4 and the adversarial QA pass, before final bundle verification. Read with makefun_winning_playbook (perf playbook)."
---

# Competitive Performance Engineer Protocol

Core logic works and passes edge cases — but Rank #1 is decided by 0.1% accuracy or a few milliseconds. Squeeze the maximum score out of the local grader. Execute in order.

## STEP 1 — Baseline Benchmark & Granular Profiling

1. Run the official local grader: record exact **Baseline Score** and **Baseline Execution Time**.
2. Profile the execution (`cProfile`/`line_profiler` for Python, `node --prof`/`performance.now()` for JS/TS, `perf`/flamegraph where applicable).
3. Identify the top 3 hotspot functions consuming >80% of CPU/memory, and pinpoint where metric points are lost.
4. Benchmark like the grader: 1 CPU (`taskset -c 0`), cold caches, grader-exact commands (playbook perf rules).

## STEP 2 — Automated Parameter & Hyperparameter Sweep

1. List every tunable parameter (thresholds, weights, window sizes, learning rates, decay factors, beam widths, retry limits, heuristic constants — e.g., chess node cap NC).
2. Write a tuning script (`scripts/tune_params.py` or equivalent; keep in `tests/` or `scripts/` per repo rules) using Grid Search, Random Search, or Bayesian/Optuna-style optimization against the local grader's exact scoring function.
3. Guard against overfitting: validate candidate sets across multiple data slices/seeds; select parameters that generalize, not lucky flukes.
4. Apply the winning configuration; record the new score.

## STEP 3 — Low-Level Algorithmic & Runtime Micro-Optimization

Attack the Step 1 hotspots:
1. **Eliminate redundant work**: memoization/caching, precomputed lookup tables, hoist invariants out of inner loops.
2. **Vectorization & data structures**: flat typed arrays, sets, bitmasks, int-coded boards over string/object lookups; vectorized ops where the runtime allows (stdlib-only sandboxes: prefer arrays/bit tricks over NumPy unless allowed).
3. **Memory & I/O efficiency**: minimize allocations in hot loops; batch I/O.

## STEP 4 — Strict Ratchet Verification & Cleanup

1. After EACH individual optimization, re-run the official local grader:
   - Score improves (or ties with strictly faster runtime) → keep + commit (`git commit -m "perf: <optimization> | score: <val> | time: <ms>"`).
   - Score drops even slightly → revert immediately (`git checkout -- <file>`). Never regress.
2. Remove temporary profiling/sweep scripts before final packaging (repo stays clean; reusable harnesses may stay in `tests/` if rules allow).
3. Run the official local grader twice consecutively: deterministic, peak performance, bundle-ready.

## Standing rules

- Transcript rules apply to every code change (authored entrypoints, ≥2 write/edit messages, compress after last source write).
- Size caps are hard (e.g., chess packed ≤4,096 B): every optimization is measured against the cap; size-growing changes need compensating trims.
- Speed rules: smoke benchmarks first, full sweeps in background, harnesses saved for reuse.
- Security notices during execution need EL's fresh explicit approval per flagged stage.
- This protocol is the deep-dive of `multi_hypothesis_protocol` Step 4; the adversarial suite (`adversarial_qa_redteam`) must still pass after every kept optimization.

## Verification Loop — Regression Test on Every Fix (EL, 2026-11-22)
Whenever QA (or the adversarial suite) finds a gap or bug, the Builder must add **1 new regression test case** to the local test script/harness that reproduces the found failure, BEFORE any final optimization pass. No optimization is kept unless the new case passes AND the full existing suite still passes — performance work must never break functionality that already passed.
