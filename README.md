# Wargames

A skill for proactively red-teaming agent evaluations: send a small adversarial swarm to find ways to earn credit without completing the intended task, reproduce confirmed flaws, repair the evaluation, and turn the findings into guidance for future eval generators.

The repository includes the [skill](skills/wargames/SKILL.md), five [runnable audit evals](evals/wargames/README.md), a separate lesson-transfer stage, and [evidence from wargaming the evals themselves](evals/wargames/REPORT.md).

## Install the skill

Copy `skills/wargames` into your agent's skills directory. For Codex, the default location is `~/.codex/skills/wargames`. Invoke it with `$wargames` when auditing or creating an evaluation.

## Run the evals

Requires Python 3.11 or newer with SQLite serialization support, and a running Docker daemon.

```bash
cd evals/wargames
docker build -t wargames-evals:local .
python3 calibration.py ./calibration-output
python3 run.py prepare ./prepared-cases
```

Calibration checks reference submissions and deliberately incorrect submissions. Prepared cases use neutral names; give an audit agent only one case's request and workspace, plus a separate submission directory. Keep the operator manifest and grading code outside candidate context.

See the [eval instructions](evals/wargames/README.md) for grading submissions, running fresh-agent transfer, and enforcing the execution boundary.

## What was verified

The original eight calibration checks passed. One development audit reproduced a verifier bypass and submitted working repairs; a second fresh-context agent used the resulting lessons to generate a related SQLite evaluation. Both pilot outputs passed their then-current executable grading checks. The subsequent [intent review](evals/wargames/INTENT_REVIEW.md) identified missing candidate-work requirements; grader version 3 adds those checks and three calibration cases.

Those runs establish a development trajectory, not a held-out result, broad transfer, reliability estimate, or causal improvement claim. Reasoning, lesson quality, and run provenance still require review. The [ledger](evals/wargames/LEDGER.md) records reward-hacking mechanisms and prevention evidence; the [report](evals/wargames/REPORT.md) records the original run. Machine-specific paths in published evidence are replaced with `<repo>`; generated SQLite state and local scratch outputs are excluded.
