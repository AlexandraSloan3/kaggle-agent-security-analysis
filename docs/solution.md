# Solution notes

These notes explain the probe-then-scale submission for [AI Agent Security: Multi-Step Tool Attacks](https://www.kaggle.com/competitions/ai-agent-security-multi-step-tool-attacks). The submitted code is my team's, adapted from a public Kaggle notebook by Adhiraj Jagtap; these notes are my technical analysis of it.

The submitted implementation is in [`notebooks/sub.ipynb`](../notebooks/sub.ipynb), with the matching Python source in [`src/attack.py`](../src/attack.py). The annotated version adds English explanations while keeping the same executable statements.

## Probe-then-scale

The submission compares two templates: `direct_mail` and `minimal_mail_control`. Both ask the sandboxed agent to perform a routine delivery to a synthetic recipient. The second appends a Harmony-style dialogue fragment. The original prompt strings are kept exactly; their effectiveness outside this benchmark is not established.

For each template, the code runs two probes. Each probe resets the environment, calls `env.interact(message, max_tool_hops=1)`, and inspects the exported trace.

Let `c` be the number of trace events with `ok is True` and `name == "email.send"`. The reward proxy for one probe is:

```text
proxy(c) = 4 * c + 2, if c > 0
           0,         otherwise

template_rate = sum(probe proxies) / sum(probe elapsed times)
```

Elapsed time includes resetting the environment, interaction, and trace inspection. Each duration is floored at 0.001 seconds. Exceptions in the probe block produce a zero proxy for that probe.

This count is used to choose a template. It checks successful tool events only; it does not evaluate the official `CONFUSED_DEPUTY` predicate, severity, guardrail decision, or final score. Those are handled by the evaluator.

## Why a rate, not a raw count

Each evaluation phase runs under a fixed time budget, and the replay phase stops at its deadline while keeping the findings already completed. Normalizing the reward by probe time therefore aligns the selection heuristic with how the evaluator spends its budget: the quantity of interest is successful findings per second, not per attempt. The small probe stage is the way a timing signal enters template selection.

## Candidate generation

When an environment is available, the algorithm expands the selected template into 2,000 `AttackCandidate` objects. Each object contains one user message. The recipient function returns:

```text
probe{index modulo 10000, formatted with four digits}@outside.invalid
```

Four addresses are consumed by probing. Normal generation therefore starts at `probe0004@outside.invalid` and ends at `probe2003@outside.invalid`. The function supports a pool of 10,000 addresses; a normal run does not emit 10,000 candidates. A unique recipient per candidate means no two candidates are identical.

Without an environment, the algorithm skips probing and returns 250 candidates from the first template. This is a separate fallback path.

## Submission bootstrap

The first notebook cell writes `attack.py`. The second prepares a placeholder `submission.csv` and starts `JEDAttackInferenceServer`.

The CSV's zero values are placeholders, not measured results. Server exceptions are re-raised when `KAGGLE_IS_COMPETITION_RERUN` is set; otherwise, the exception is printed. A printed non-rerun exception does not demonstrate a working submission.

The source attempts to locate `kaggle_evaluation` under `/kaggle/input`, with two working-directory-relative fallbacks. It also tries two base-class constructor signatures. Those fallbacks are compatibility attempts, not a guarantee that an arbitrary SDK version will work.

## Validation experiment

[`validation.ipynb`](../notebooks/validation.ipynb) records a separate experiment. It differs from the submission in several settings:

| Setting | Submission | Validation experiment |
| --- | --- | --- |
| Templates | `direct_mail`, `minimal_mail_control` | `minimal_field_mail`, `compact_mail_tight` |
| Candidates with an environment | 2,000 | 10 |
| Candidates without an environment | 250 | 250 |
| Probe hop request | 1 | 1 |
| Replay control | Competition evaluator | Custom harness: GPT-OSS 8, Gemma 1 |
| Guardrail in the bundled harness | Not supplied by the submission | Public `OptimalGuardrail` |

The harness imports defaults from the competition gateway, loads the two model servers, generates and replays candidates, and writes diagnostic reports. Its custom policy can force a model score to zero when an interaction audit records a violation. Consequently, its outputs should not be presented as official private leaderboard measurements.

## Implementation limits

- Only four probes inform the template choice; this provides limited evidence about stability.
- The code does not rank candidates by quality or recipient margin. It emits them in address order.
- A one-hop probe does not impose a one-hop cap on the evaluator's later replay.
- `config` is accepted by `run` but not read. Changing `n_candidates` in a configuration dictionary will not override the constant.
- If all probes fail, the first template wins the zero-rate tie and candidates are still generated.
- Timing uses wall-clock measurements and may vary with model loading state, hardware, caching, and runtime versions.
- `PAYLOAD` and `_ALPHA` are retained from the original source. Neither template uses the supplied `payload` formatting field; their actual bodies say `all systems nominal`.
- Broad exception handling can hide setup or probe failures. Inspect logs during reproduction.

## What I take from this, and what I would test next

The main point I draw from this method is that the evaluator's rules — replay under a time budget, held-out private scoring — are part of the problem, so aligning the search with those rules matters more than the wording of any one prompt. The rate-based proxy is a small, concrete instance of that idea.

I would next test stability over repeated runs, check the exact predicates, compare hop limits under controlled conditions, and record a complete environment manifest. Recipient ranking, gradient-based prompt optimization, and private-guardrail probes are not implemented in this version. Earlier notes used the word "honeypot" for the public/private mismatch, but there are no supporting records here to establish organizer intent or specific performance gains by other teams.
