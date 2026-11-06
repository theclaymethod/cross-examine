# Cross-Examine

A skill for proactively red-teaming agent evaluations: send a small adversarial swarm to find ways to earn credit without completing the intended task, reproduce confirmed flaws, repair the evaluation, and turn the findings into guidance for future eval generators.

The repository includes the [skill](skills/cross-examine/SKILL.md), five [runnable audit evals](evals/cross-examine/README.md), a separate lesson-transfer stage, and [evidence from cross-examining the evals themselves](evals/cross-examine/REPORT.md).

## Install the skill

Copy `skills/cross-examine` into your agent's skills directory. For Codex, the default location is `~/.codex/skills/cross-examine`. Invoke it with `$cross-examine` when auditing or creating an evaluation.

The skill was previously named `wargames`. Historical evidence retains its original names, paths, image tags, and settings; use the recorded commit when reproducing an old run. Current commands use `cross-examine` and `CROSS_EXAMINE_*` settings.

## Run the evals

The skill uses local files by default and requires no database or container runtime. The bundled examples specifically exercise database behavior, so their runner requires Python 3.11 or newer with standard-library SQLite serialization support. No third-party Python packages are required.

```bash
cd evals/cross-examine
python3 calibration.py ./calibration-output
python3 run.py prepare ./prepared-cases
```

Local execution is the default and is intended for trusted calibration or reviewed scripts. It has no sandbox: programs retain your host permissions. For untrusted submissions or enforced permission checks, use an adequate sandbox; the bundled Docker option is:

```bash
docker build -t cross-examine-evals:local .
python3 calibration.py ./container-calibration --executor docker
```

Choose the executor explicitly for scored runs and preserve the result's `execution_boundary`. The runner never falls back from Docker to local execution. Docker runs preflight the configured shared scratch storage and SQLite lifecycle, then verify exact-container cleanup after each replay. Infrastructure failures remain unscored.

Calibration checks reference submissions and deliberately incorrect submissions. Prepared cases use neutral names; give an audit agent only one case's request and workspace, plus a separate submission directory. Keep the operator manifest and grading code outside candidate context.

See the [eval instructions](evals/cross-examine/README.md) for grading submissions, running fresh-agent transfer, and enforcing the execution boundary.

## What was verified

After the rename, grader version 6 and fixture version 4 passed all 11 calibration checks locally and with Docker. The [rename receipt](evals/cross-examine/evidence/rename-cross-examine-2026-11-06/receipt.json) records validation; historical evidence remains unchanged.

The [latest frozen run](evals/cross-examine/evidence/current-skill-v5-2026-11-06/REPORT.md) passed all 11 calibration checks with each executor and all five audit artifact graders. Only one of three audit lanes completed because two agent calls were blocked by an automated cybersecurity-risk check. Fresh transfer outputs passed with both revised and original guidance, so this pair showed no learning advantage.

The original eight calibration checks passed. One development audit reproduced a verifier bypass and submitted working repairs; a second fresh-context agent used the resulting lessons to generate a related SQLite evaluation. Both pilot outputs passed their then-current executable grading checks. The subsequent [intent review](evals/cross-examine/INTENT_REVIEW.md) identified missing candidate-work requirements; grader version 3 adds those checks and three calibration cases.

Those runs establish a development trajectory, not a held-out result, broad transfer, reliability estimate, or causal improvement claim. Reasoning, lesson quality, and run provenance still require review. The [ledger](evals/cross-examine/LEDGER.md) records reward-hacking mechanisms and prevention evidence; the [report](evals/cross-examine/REPORT.md) records the original run. Machine-specific paths in published evidence are replaced with `<repo>`; generated SQLite state and local scratch outputs are excluded.
