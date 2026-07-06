# Constitution Layer 1 — Hard Rules

These rules govern how evaluation data is collected, stored, and used in agent-eval and any infrastructure built on top of it.

Layer 1 is deterministic and machine-readable. These rules do not change without explicit human approval with logged reasoning. No exception.

---

## Rule 1: No payload data stored

Evaluations store structural patterns only.

- What gets stored: scenario ID, score per dimension, evaluator ID, timestamp, model version, reasoning summary
- What never gets stored: the actual agent output text, user data, client data, session context, any PII

**Why:** Caching payload data in multi-tenant evaluation environments is a GDPR breach. The structural pattern is what matters for the methodology. The raw output is not needed after scoring.

---

## Rule 2: No vendor bias

All LLMs are evaluated against identical criteria.

- Same scenario input for every model
- Same rubric applied by the same evaluator
- No scenario designed to favor any specific model architecture
- Performance data published in aggregate, never used to disadvantage specific providers commercially without their knowledge

---

## Rule 3: Evaluation criteria are public

No black box scoring.

- Every rubric is version-controlled and publicly readable
- Every rubric change is logged with reasoning
- Anyone can challenge a scoring criterion via GitHub Issues
- Contested judgments are documented, not hidden

---

## Rule 4: Human review for contested judgments

When evaluators disagree by more than 15 points on any dimension, the scenario goes to human review.

Automated scoring is a tool. The Oracle is not the final word on its own judgments. Human review is permanent infrastructure, not a temporary scaffold.

---

## Rule 5: Version history on all rubric changes

Every rubric change is versioned. The date, the change, and the reasoning are logged in the rubric file. Old versions are archived, not deleted.

This creates an auditable methodology history — the foundation of the moat.

---

## Rule 6: ANIMA data separation

No behavioral or personal data from any person crosses into the evaluation infrastructure.

Agent evaluation data and human behavioral data are architecturally separate. The connection between the two systems is conceptual and methodological, not operational.

---

## Rule 7: The Oracle is not the sole evaluator

The Debate Layer is permanent infrastructure. The Oracle's judgment is one input, not the final word.

When the Oracle and the Debate Layer disagree, that disagreement is the signal — it means uncertainty is high, and the task should be escalated to human review or more debate rounds.

---

## Amending Layer 1

Any amendment requires:
1. A GitHub Issue with the proposed change and full reasoning
2. A minimum 7-day comment period
3. Explicit approval from the project maintainer with logged reasoning
4. Version bump in this file

---

*Constitution Layer 1 — v1.0 — June 2026*
*This document is public. Challenge it if you think it's wrong.*
