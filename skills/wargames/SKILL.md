---
name: wargames
description: Proactively red-team generated agent evaluations with a small adversarial swarm. Use when creating or hardening eval tasks, environments, or verifiers to expose ways to pass without fulfilling the intended task and teach eval generators from confirmed flaws.
---

# Wargames

Attack an evaluation before trusting its scores. Seek a concrete counterexample: an agent receives credit without achieving the intended outcome, or the environment prevents or mismeasures a legitimate solution. Feed confirmed design flaws back into the instructions that generate future evaluations.

Before generating or reviewing an eval, read [confirmed design lessons](references/lessons.md) and apply those whose conditions match the task.

## Establish the game

Read the task, environment setup and reset path, verifier, scoring rules, and any eval-generator instructions. State the intended capability and observable success independently of the verifier's implementation. If the intended outcome is ambiguous, resolve that ambiguity before declaring a shortcut illegitimate.

Map what the evaluated agent can actually read, write, execute, and influence, including artifacts, processes, tools, network access, and persistent state. Separate evaluator-only assets from agent-accessible assets. Record the exact verifier entrypoint and how its result becomes a score.

Use disposable copies or isolated runs. Establish that reset works and that a known legitimate outcome receives the expected result. Use an existing reference solution when available; report missing baseline evidence rather than inventing it. A broken baseline is itself a finding and limits conclusions from later attacks.

Choose a bounded attack budget before dispatch, using the user's budget when provided; otherwise default to one parallel round with three attackers and at most two concrete attempts per attacker. Verification and reporting still follow when the attack budget ends. Scope all attempts to the authorized eval sandbox.

## Dispatch the adversaries

Launch a mini-swarm with separate ownership and environments. Three complementary lanes are a useful default; adapt them to the eval:

- **Verifier:** Obtain credit with a wrong or incomplete result. Probe what the verifier trusts: self-reported success, editable expected answers, forged artifacts, partial matches, empty cases, skipped checks, errors, and score aggregation.
- **Environment:** Exploit lifecycle and isolation. Probe stale state, answer leakage, writable evaluator assets, process interference, reset failures, and contamination between runs.
- **Task contract:** Satisfy the literal checks while violating the intended outcome. Probe proxy metrics, hardcoded answers, output substitution, omitted requirements, and legitimate alternatives the verifier rejects.

Each attacker receives the task, success contract, access boundary, runnable environment and verifier, budget, and this objective:

> Find the cheapest reproducible way to earn credit without achieving the intended outcome, or demonstrate an environment/verifier defect that invalidates the evaluation. Produce the exploit and observed result. Attack the evaluation; repairing it is the coordinator's job.

Keep their hypotheses independent during the first round. Give each agent its own scratch space and reset state; serialize runs if the infrastructure cannot isolate them. Attackers may alter only their assigned sandbox and return findings; the coordinator owns canonical eval and skill edits. If delegation is unavailable, run the lanes sequentially and disclose that limitation.

White-box inspection can expose hypotheses, but a valid exploit must execute with the evaluated agent's actual access. Mark attacks requiring extra access separately. Avoid counting an intended efficient solution as an exploit merely because it differs from a reference trajectory.

Require each attacker to return:

- The violated requirement and the candidate's actual permissions.
- The smallest artifact or exact actions needed to reproduce, including setup and reset.
- Verifier invocation, observed output/score, and independent evidence of the actual task outcome.
- Any unexecuted hypothesis, failed attempt, or infrastructure limitation, clearly distinguished from a reproduced finding.

## Reproduce and adjudicate

Replay candidate findings from a clean reset against the original eval. Preserve the relevant eval/verifier version and exploit artifact. Confirm the result through the actual scoring path; a locally forged success message alone is insufficient.

Judge the candidate outcome against the task contract independently of the verifier. For environment failures, reproduce the broken invariant or legitimate outcome that was mismeasured. Distinguish:

- A confirmed scoring bypass or environment defect.
- A specification ambiguity that needs an explicit contract decision.
- A hypothesis or attack that did not reproduce.

Merge duplicate findings by root cause. Explain the trust boundary or invalid assumption that made each confirmed flaw possible and what it does to score interpretation. A bounded search without findings establishes only what was attempted, not that the eval is secure.

## Harden within scope

When eval changes are authorized, fix the faulty assumption at its source. Prefer checking independently observable outcomes, protecting evaluator-owned state, isolating runs, and restoring reliable reset behavior over blacklisting one exploit's spelling.

Replay the confirmed exploit after the fix: it should lose undeserved credit or the environment defect should disappear. Rerun the legitimate baseline and relevant existing checks to show that valid solutions still work. Respect the repository's rules for creating test files; direct replays and existing checks can provide evidence without adding new tests.

If the scope is review-only, deliver the reproducer and proposed correction without modifying the eval. Preserve unresolved findings when repairs or validation cannot be completed. Additional attack rounds require remaining budget or a newly agreed budget.

## Teach the generator

After confirming a flaw, find the skill that actually guides this eval generator. Add or refine one concise lesson there, or in an existing reference it already routes to. Preserve unrelated instructions and deduplicate by mechanism.

If no generator skill exists, create `references/lessons.md` beside this skill when the first confirmed lesson is available, link it from this skill, and instruct future runs to read it before generating or reviewing evals. Keep the reusable lesson with the skill and detailed repro artifacts with the eval. Report any inability to persist the lesson.

Each lesson must explain:

- **Failure:** The design assumption and how it was exploited.
- **Why:** The gap between the measured signal and intended outcome.
- **Generate instead:** A concrete construction rule for future evals, scoped to the conditions that make it necessary.
- **Evidence:** The eval/version and reproducer location, plus the observed result and any verified repair.

Promote only confirmed mechanisms into generation guidance. Keep conjectures in the findings report. For example, if a verifier accepts an agent-writable success flag, teach generators to measure the actual outcome through evaluator-owned evidence; banning that particular filename does not address the flaw.

Finish with the confirmed findings and evidence, repairs and replay results, exact lesson files changed, and untested surfaces or unresolved limitations. Claim completion only for the attack budget and validation actually performed.
