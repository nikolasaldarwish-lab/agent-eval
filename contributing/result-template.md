# Evaluation Result Template

Use this template to log the result of an evaluation run. One file per run, or append as a row to a shared log — either is fine as long as every field below is captured.

## Run Metadata

- **Scenario code:** (e.g. GA-003)
- **Rubric version:** (e.g. core-rubric v1.0)
- **Agent tested:** (name/version/config)
- **Evaluator:** (name or handle)
- **Date:** (YYYY-MM-DD)

## Scores

| Dimension | Weight | Score (0–max) | Notes |
|---|---|---|---|
| Task Completion | 40% | | |
| Uncertainty Signaling | 25% | | |
| Failure Grace | 20% | | |
| Reasoning Quality | 15% | | |

**Total (weighted, /100):**

## Evidence

- Link or excerpt of the agent's actual output supporting each score above.
- Note any transcript sections relevant to contested judgments.

## Contested Judgments

- Note any dimension where scoring was ambiguous or where a second evaluator might reasonably disagree by 15+ points. Per the constitution (Layer 1, Rule 4), disagreements of this size trigger human review.
