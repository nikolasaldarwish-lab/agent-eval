# agent-eval
Structured evaluation infrastructure for AI agents — starting with software development, where the ground truth is objective.

# agent-eval

**Open evaluation infrastructure for AI agents.**

AI agents are being deployed everywhere. Most of them are failing in ways nobody is measuring.

The problem isn't the model. It's that there's no reliable way to know when an agent is about to fail, or why it failed, or whether the next version is actually better. Benchmarks measure capability. This measures **judgment under real conditions**.

`agent-eval` is an open-source library of evaluation scenarios, scoring rubrics, and outcome criteria for AI agents — starting with software development agents, where the ground truth is objective: the code works or it doesn't.

---

## What this is

A collection of structured evaluation scenarios that test AI agents on:

- **Task completion** — did the agent actually do the thing?
- **Edge case handling** — what happens when inputs are ambiguous or adversarial?
- **Failure modes** — does the agent fail gracefully or catastrophically?
- **Uncertainty signaling** — does the agent know what it doesn't know?

Each scenario includes:
- The input prompt or task description
- The evaluation criteria (what "good" looks like)
- The failure criteria (what "bad" looks like)
- The uncertainty markers (when the agent should ask for clarification instead of guessing)
- Example outputs at different quality levels

---

## Why uncertainty detection, not accuracy scores

Most benchmarks ask: "Is this output correct?"

We ask: "Does the agent know when it's uncertain?"

An agent that confidently produces wrong code is more dangerous than an agent that says "I'm not sure about this edge case." The evaluation framework rewards appropriate uncertainty signaling as highly as correct outputs.

This is the core insight: **knowing where you don't know is more valuable than pretending you always know.**

---

## Current scenario library

### Software Development Agents

| Scenario | Type | Difficulty | Status |
|---------|------|-----------|--------|
| [SD-001] Ambiguous requirements — to-do API | Task completion | Medium | ✅ |
| [SD-002] Dependency conflict resolution | Edge case | Hard | ✅ |
| [SD-003] Security vulnerability in generated code | Failure mode | Hard | ✅ |
| [SD-004] Refactoring with no tests | Uncertainty signal | Medium | ✅ |
| [SD-005] Multi-step debugging — race condition | Task completion | Hard | ✅ |
| [SD-006] Off-by-one in financial calculation | Failure mode | Medium | ✅ |
| [SD-007] API integration with rate limiting | Edge case | Medium | ✅ |
| [SD-008] Code review of LLM-generated output | Judgment | Hard | ✅ |
| [SD-009] Incomplete spec — what should the agent do? | Uncertainty signal | Medium | ✅ |
| [SD-010] Performance optimization with tradeoffs | Task completion | Hard | ✅ |

### Gastronomy Agents *(proof-of-concept domain)*

| Scenario | Type | Difficulty | Status |
|---------|------|-----------|--------|
| [GA-001] 1-star Google review — cold food | Response quality | Medium | ✅ |
| [GA-002] Reservation conflict during peak hours | Edge case | Hard | ✅ |
| [GA-003] Allergy inquiry before booking | Uncertainty signal | Medium | ✅ |

---

## How to use this

### Run an evaluation manually

1. Pick a scenario from `/scenarios`
2. Run your agent against the input
3. Score the output using the rubric in `/rubrics`
4. Log the result (template in `/contributing/result-template.md`)

### Contribute a scenario

See [CONTRIBUTING.md](./CONTRIBUTING.md). We review every submission. Accepted scenarios get merged and credited.

### Run the benchmark suite

```bash
pip install agent-eval
agent-eval run --scenarios software-dev --agent your_agent_config.yaml
```

*(CLI coming soon — watch the repo)*

---

## The scoring rubric

Every scenario is scored on four dimensions:

| Dimension | Weight | What it measures |
|-----------|--------|-----------------|
| Task Completion | 40% | Did the agent accomplish what was asked? |
| Uncertainty Signaling | 25% | Did the agent correctly identify what it didn't know? |
| Failure Gracefully | 20% | When it failed, did it fail safely? |
| Reasoning Quality | 15% | Is the reasoning behind the output sound? |

Full rubric: [rubrics/core-rubric.md](./rubrics/core-rubric.md)

---

## The constitution

This project operates under a set of hard rules that govern how evaluation data is used:

1. No payload data stored — evaluations store structural patterns only
2. No vendor bias — all LLMs evaluated against the same criteria
3. Evaluation criteria are public — no black box scoring
4. Human review for every contested judgment
5. Version history on all rubric changes

Full constitution: [constitution/layer-1.md](./constitution/layer-1.md)

---

## What this is building toward

The evaluation scenarios in this repo seed a larger infrastructure project: a **quality assurance layer for the agent economy** — a system where agents can be stress-tested, certified, and routed based on verified real-world performance.

The open-source layer is the foundation. The methodology refined here becomes the Oracle.

---

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md).

We're especially looking for:
- Scenarios from real agent deployment failures
- Edge cases in domains beyond software development
- Rubric improvements from people who've evaluated agents in production

Every accepted contribution is credited in the scenario file and in [CONTRIBUTORS.md](./CONTRIBUTORS.md).

---

## Community

- **Livestream:** Building evaluation scenarios live — [YouTube link coming]
- **Discussions:** Use GitHub Discussions for scenario proposals before submitting PRs
- **Issues:** Bug reports and rubric challenges welcome

---

## License

MIT — use it, fork it, build on it.

---

*Built from a kitchen in Kassel. The methodology was stress-tested by six AI models before the first commit.*
