---
name: "adversarial_qa_redteam"
description: "Adversarial QA / Red-Teamer protocol (EL's standing order, 2026-11-21): assume local practice data is an easy happy-path sample and the hidden make.fun server suite contains brutal edge cases, noisy distributions, and max-scale stress inputs. Break the solution locally before the server grader does, then harden until bulletproof. Run by the QA checker role after the standard QA pass, before compress/submit. Read with bounty_qa_checker + multi_hypothesis_protocol."
---

# Adversarial QA & Red-Teamer Protocol

Assume the local practice data is an easy "happy path" sample. The hidden server evaluation suite contains brutal edge cases, noisy distributions, and maximum-scale stress inputs designed to break naive solutions. Mission: break our solution locally first, then harden it until bulletproof. Execute in order.

## STEP 1 — Vulnerability & Overfitting Audit

1. Inspect the current solution alongside bounty rules, schema constraints, and the local grader.
2. List at least **5 specific ways** the code could fail or lose points on unseen hidden cases: overfitting to local sample patterns, integer overflow, division by zero, NaN/null values, out-of-distribution noise, memory spikes, timeout on max input size, degenerate structures (empty/single/duplicate inputs), pathological orderings.

## STEP 2 — Synthetic Adversarial Test Generator

Write a standalone suite (`tests/adversarial_gen.py` or equivalent, kept in the repo's `tests/` for reuse) generating data significantly harder, noisier, and larger than practice data:

1. **Boundary & degenerate inputs**: empty, single-element, all-zeros, identical/duplicate values, negative/extreme values, exact schema min/max limits.
2. **Distribution shift & heavy noise**: out-of-distribution patterns, adversarial outliers, corrupted/missing fields, randomized noise injections (catch overfitting).
3. **Worst-case scale & complexity traps**: maximum allowed input sizes, pathological structures (worst-case graph topologies, sorted/reverse-sorted arrays, high-volatility sequences) to test strict timeout and memory limits.

Chess example: checkmate/stalemate FENs, en-passant & castling edge cases, promotions, bare-king endgames, max-branching middlegames, 200-ply games, TT saturation, positions where the only legal move is a capture/promotion.

## STEP 3 — Stress-Test Execution & Failure Diagnosis

Run the solution against the full adversarial suite; measure crash/exception rate, timeout/peak memory (1 CPU, grader-like limits), and score degradation vs clean practice data. Log every failure with exact reproduction details (input, command, observed vs expected).

## STEP 4 — Hardening & Dual-Verification

1. Harden with generalizable, mathematically sound logic — never hardcoded patches for specific test inputs. (Hardening edits go back to a builder session; the checker reports, the lead routes — see `bounty_qa_checker`.)
2. Re-run BOTH suites: adversarial (0 crashes, 0 timeouts, stable score) AND the official local grader (score intact or improved).
3. Clean up temporary artifacts that violate repo rules, commit (`git commit -m "harden: pass adversarial stress tests"`), confirm bundle readiness.

## Standing rules

- Speed rules apply: smoke adversarial subset first (minutes), full suite in background; harness saved in `tests/` for reuse.
- Transcript rules apply to any code changes (authored entrypoints, ≥2 write/edit messages, compress after last write).
- Security notices during execution need EL's fresh explicit approval per flagged stage.
- The adversarial pass is part of Step 4 of `multi_hypothesis_protocol` and the QA gate in `bounty_qa_checker` — all three run before any submit.
