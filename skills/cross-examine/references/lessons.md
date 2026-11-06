# Confirmed design lessons

## Reconstruct disposable state across attempts

Failure: A reset issued SQL against a database file writable by the evaluated agent. Corrupting that file made reset fail before it could restore the starting rows.

Why: Initialization trusted the structural validity of state controlled by the previous attempt. One attempt could prevent subsequent attempts from starting, even though reset worked after ordinary successful runs.

Generate instead: For fully disposable state, reconstruct from evaluator-owned fixture data. Exercise reset after completed, partial, empty, and structurally invalid states. Preserve application-owned data when the task requires it; reconstruction is appropriate only for state the eval owns and may discard.

Evidence: The [eval suite](https://github.com/theclaymethod/cross-examine/tree/main/evals/cross-examine) records the original failure in `evidence/model-audit-01/submission/finding.json`. Fixture version 2 reconstructs disposable state; `run.py` and `transfer.py` replay corrupt-state recovery for submitted resets.

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

## Require the candidate's work before crediting the capability

Failure: The outer grader reproduced a reset defect itself and accepted an unchanged generator skill. A submission without its own reproducer or teaching change passed the executable proxy.

Why: Successful harness operations were mistaken for evidence that the evaluated agent performed the intended discovery and learning work.

Generate instead: Replay candidate-authored demonstrations and require promised output changes. Keep those necessary artifact checks separate from semantic review and discovery provenance. When a control receives a surprising supported finding, adjudicate the evidence rather than rejecting it by seed label. Freeze scored versions before candidate exposure; runs used to revise the grader are development evidence.

Evidence: The suite's `evidence/intent-review-2026-11-06/before.json` accepts the frozen incomplete submission; `after.json` rejects it under grader version 3. Its `LEDGER.md` records prevention scope and remaining claims that have not been established.
