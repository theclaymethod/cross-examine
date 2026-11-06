# Confirmed design lessons

## Reconstruct disposable state across attempts

Failure: A reset issued SQL against a database file writable by the evaluated agent. Corrupting that file made reset fail before it could restore the starting rows.

Why: Initialization trusted the structural validity of state controlled by the previous attempt. One attempt could prevent subsequent attempts from starting, even though reset worked after ordinary successful runs.

Generate instead: For fully disposable state, reconstruct from evaluator-owned fixture data. Exercise reset after completed, partial, empty, and structurally invalid states. Preserve application-owned data when the task requires it; reconstruction is appropriate only for state the eval owns and may discard.

Evidence: The [eval suite](../../../evals/wargames/README.md) records the original failure in `evidence/model-audit-01/submission/finding.json`. Fixture version 2 reconstructs disposable state; `run.py` and `transfer.py` replay corrupt-state recovery for submitted resets.

## Match report validation to the declared contract

Failure: An audit delivered structured execution evidence, but the outer grader required the evidence field to be a string even though the request had not specified that restriction.

Why: A serialization choice was mistaken for evidence quality, rejecting a substantively useful report before its replay results could be assessed.

Generate instead: Declare artifact schemas explicitly. Where text and structured evidence serve the same purpose, accept both and assess their substance separately. Calibrate with realistic candidate-produced artifacts as well as handcrafted references.

Evidence: The same suite's `evidence/model-audit-01/submission/finding.json` contains structured replay evidence. The current `run.py` accepts text, list, or object evidence, and `candidate.md` makes those formats explicit.

## Keep control expectations aligned with supplied assets and permissions

Failure: A no-change control was compared against a different generator document from the one supplied to the auditor. A protected-oracle control also required a permission-limited finding instead of accepting a bounded clean conclusion.

Why: Both grading rules imposed requirements outside the candidate's contract. Correct restraint could be scored as failure.

Generate instead: Derive preservation checks from the exact supplied artifact. For negative controls, accept justified bounded conclusions and review access assumptions rather than requiring an invented defect.

Evidence: `evidence/contract/report.md` in the same suite records the mismatched documents and classification predicate. The current `run.py` compares against the materialized source and accepts either clean or access-limited conclusions for the protected-oracle control.
