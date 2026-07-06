# Contributing to agent-eval

Thank you for helping build evaluation infrastructure for AI agents. Every scenario you contribute improves how the entire community measures agent quality.

---

## What we're looking for

**High value contributions:**
- Scenarios from real agent deployment failures you've witnessed
- Edge cases that exposed unexpected agent behavior
- Scenarios where the "right" answer is genuinely debatable (these are the most valuable)
- Rubric challenges — if you think a scoring criterion is wrong, argue it

**We don't need:**
- Trivial tasks any agent handles correctly
- Synthetic scenarios with no grounding in real usage
- Scenarios where the failure mode is just "wrong answer" — we want behavioral and judgment failures

---

## Scenario format

Create a new file in `/scenarios/[domain]/[DOMAIN-ID]-short-name.md`

Domain codes: `SD` (software dev), `GA` (gastronomy), `CS` (customer support), `FI` (financial), `LE` (legal)

Use this template:

```markdown
# [DOMAIN-ID] Scenario Title

**Domain:** [software-dev / gastronomy / customer-support / etc]
**Type:** [task-completion / edge-case / failure-mode / uncertainty-signal]
**Difficulty:** [easy / medium / hard]
**Contributed by:** [GitHub username]
**Real-world grounding:** [brief note on whether this comes from a real deployment scenario]

---

## The Input

[The exact prompt or task description the agent receives. Be specific. Include all context the agent would have.]

---

## Evaluation Criteria

### What excellent looks like (Score 4-5)
[Describe specifically what a high-quality agent response does]

### What acceptable looks like (Score 2-3)
[Describe a passing but not excellent response]

### What failure looks like (Score 0-1)
[Describe specific failure modes — especially dangerous ones]

### Uncertainty signal required?
[Yes/No — if yes, describe when the agent should ask for clarification rather than proceeding]

---

## The Ground Truth

[For scenarios with an objective answer, state it here.
For judgment scenarios, describe the key factors that should inform the response.]

---

## Common Agent Failure Modes

[List 2-3 specific ways you've seen agents fail on this type of scenario]

---

## Notes for Reviewers

[Anything the reviewer should consider when evaluating submissions against this scenario]
```

---

## Rubric challenges

If you disagree with how a scenario is scored, open an issue with:
- The scenario ID
- The criterion you're challenging
- Your argument for why it should be different
- An example output that your proposed rubric would score differently

Rubric debates are the most valuable contribution you can make. Disagreement is data.

---

## Review process

1. Submit a PR with your scenario file
2. A maintainer reviews within 7 days
3. If accepted: merged, credited, logged
4. If needs revision: comments explaining what to change
5. If rejected: explanation of why

We reject scenarios that are trivial, purely synthetic, or don't include failure mode analysis.

---

## Recognition

Every accepted scenario gets:
- Credit in the scenario file header
- Entry in [CONTRIBUTORS.md](./CONTRIBUTORS.md)
- A mention in the next livestream session

---

## Code of conduct

One rule: argue about the scenarios, not about each other.

Disagreements about scoring criteria, ground truth, and failure modes are exactly what this project is for. Be direct. Be specific. Be wrong sometimes. That's how the rubrics improve.
