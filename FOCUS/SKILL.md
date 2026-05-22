---
name: focus
description: FOCUS protocol — four mandatory gates (THINK, SIMPLIFY, SURGICAL, STAY-ON-GOAL) that prevent fast/wrong AI answers on high-stakes work. Activate when the user invokes `/focus`, says "apply FOCUS", or when working on production code, real money, architecture decisions, recurring bugs, or anything where wrong answers cost real money/safety/legal/infra. Also activate the moment you feel confident the answer is obvious — that is exactly when mistakes happen. Source: github.com/vinayr1973-sudo/focus-skill (MIT, by Vinay R).
---

# FOCUS — Four-Gate Reasoning Protocol

> "Being slow is correct. Being rushed is wrong."
> "The goal is not to be fast. The goal is to be right the first time."

Output sequence: **THINK → SIMPLIFY → SURGICAL → STAY-ON-GOAL → Output**

Each gate is a binary check. Fail any gate → return to THINK and restart. Do not bypass.

---

## Gate 1 — THINK

Plan before suggesting. Gap-analyse the plan. Produce three internal revisions. Only the third revision reaches the user.

- **Revision 1**: the obvious first answer. Write it. Then attack it.
- **Gap analysis**: identify ≥3 missing pieces, hidden constraints, or unstated assumptions. If fewer than 3 gaps surfaced, you have not thought hard enough — restart.
- **Revision 2**: the revised answer addressing the gaps. Must look materially different from Revision 1 (if it looks the same, you are still in your first idea — restart).
- **Revision 3**: the polished, defensible answer that gets shown to the user.

Without THINK → "Use PostgreSQL with tables for trades and positions."
With THINK → full schema with bracket order relationships, idempotency keys, audit trail, P&L separation, instrument metadata, BRIN indexes, range partitioning, plus the *why* for each.

---

## Gate 2 — SIMPLIFY

The solution must travel in a straight line from input to output. No loops. No circular dependencies. No branching mid-execution. No "if A fails, try B, then C, then escalate to D."

- One source of truth per piece of state.
- One direction of data flow.
- Failure modes are explicit and detected, not buried in retries.

Without SIMPLIFY → "Check Firestore first. If empty, check IB. If IB times out, check in-memory. If in-memory is empty, retry in 5 min."
With SIMPLIFY → "Read `ib.portfolio()` directly. Close every non-zero position. Record P&L. Done."

If the plan has loops, fallback chains, or "let's also..." branches → restart from THINK.

---

## Gate 3 — SURGICAL

One solution. The best solution available anywhere in the world.

- Reference real-world superhuman examples (Gawande, Sullenberger, Singer, SpaceX). Ask: *would Paul Singer sign off on this risk analysis?*
- Fix root causes, not symptoms. A patch that handles "the headline that broke us yesterday" is not surgical — the architecture that lets *any* irrelevant headline halt trading is the problem.
- Map the complete decision tree.
- Identify every failure mode AND its detection mechanism. If a failure mode has no detection, the protocol stops — restart.

Without SURGICAL → "Add a filter to ignore stock tickers in the news scorer." (Symptom fix — a different irrelevant headline breaks it tomorrow.)
With SURGICAL → "Add instrument-universe check at architecture level before any halt write. Only instruments TRIRO trades can trigger a halt. Zero future patches needed."

---

## Gate 4 — STAY-ON-GOAL

One task at a time. Complete it so thoroughly no mistake is findable. Do not fix unrelated problems inline.

- The stated goal is the only goal. Adjacent improvements get written down for the next task — not touched now.
- Verify the output. Then verify the verification.
- If scope grew beyond the stated goal during execution → reset to the original goal and offload the rest.

Without STAY-ON-GOAL → fix signal display, restructure the force graph, change the colour scheme, refactor the WebSocket handler. Colour change breaks dark mode. The original fix is lost in the noise.
With STAY-ON-GOAL → change one word in one query. Verify with curl. Commit. Done. Related problems written down for the next task. Not touched.

---

## Pause Gates — when the protocol restarts from THINK

Restart immediately if any of the following is true at the moment of would-output:

1. Fewer than 3 gaps identified in gap analysis.
2. Suggestion-2 looks materially the same as Suggestion-1.
3. Solution contains loops or circular dependencies.
4. Root cause is not identified — only a symptom is being treated.
5. Any failure mode has no detection mechanism.
6. Output scope has grown beyond the stated goal.

---

## When to activate

| Situation | Intensity |
|---|---|
| Production code, real money, real users | Maximum — all 4 rules |
| Architecture decisions | Maximum — all 4 rules |
| Recurring bugs that keep coming back | Maximum — all 4 rules |
| Complex multi-step plans | Maximum — all 4 rules |
| "Make sure this is right" / safety-critical | Maximum — all 4 rules |
| General feature development | Standard — all 4 rules, abbreviated |
| Quick factual questions | Minimum — THINK only |

**Confidence trap**: the moment you feel the answer is obvious, activate maximum intensity. Confidence is when mistakes most often happen.

---

## Real-world reference points

Borrow from humans who achieved superhuman outcomes by being structurally slow:

| Person | Achievement | FOCUS principle |
|---|---|---|
| Atul Gawande | Surgical mortality −47% with a checklist | Pre-execution verification |
| Chesley Sullenberger | 155 people on the Hudson in 208 sec | Preparation enables impossible execution |
| Paul Singer | 47 years no losing year | Paranoid failure analysis is safety |
| SpaceX | Reusable rockets — considered impossible | First principles beat convention |

Before outputting: *"Would Paul Singer sign off on this risk analysis?"*

---

## How to apply in this conversation

When invoked, prepend the user-visible response with one short sentence acknowledging which gates ran (e.g., "Applied FOCUS — gates passed on revision 3 after one restart on the SIMPLIFY check"). Then deliver the surgical, verified output. Do not dump the internal revisions; the user wants the polished output, not the worksheet.

If a gate fails mid-task and you cannot recover within a reasonable number of restarts, surface that honestly: "FOCUS halted at SURGICAL — I cannot identify the root cause without more information. Here is the symptom-level patch + the gap I cannot close."

---

*Source: [github.com/vinayr1973-sudo/focus-skill](https://github.com/vinayr1973-sudo/focus-skill) (MIT). Built by Vinay R while shipping TranzGuard, TRIRO, and ORIRO with Claude as the primary engineering tool. FOCUS emerged from the real cost of fast/wrong AI answers in production trading and fintech systems.*
