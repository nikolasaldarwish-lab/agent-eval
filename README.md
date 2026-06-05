# agent-eval

Most AI agent benchmarks measure capability. This measures judgment.

Open evaluation scenarios, scoring rubrics, and outcome criteria for AI agents — starting with software development, where the ground truth is objective: the code works or it doesn't.

## What's in here

- `/scenarios` — evaluation scenarios by domain
- `/rubrics` — scoring criteria (4 dimensions: task completion, uncertainty signaling, failure grace, reasoning quality)
- `/constitution` — the hard rules governing how evaluation data is used
- `/contributing` — how to submit new scenarios

## The core insight

An agent that confidently produces wrong output is more dangerous than one that says "I'm not sure." This framework rewards appropriate uncertainty signaling as highly as correct outputs.

## Contributing

See CONTRIBUTING.md. We review every submission. Accepted scenarios get commit credit.

## License

MIT