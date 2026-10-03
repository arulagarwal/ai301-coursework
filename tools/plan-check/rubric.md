# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `cause-fits-repro` | The plan's stated cause, wherever it sits (a `Cause:` line, a Diagnosis section, or the comment's account of the bug), read against every step, control run, and the Actual line in `## Repro evidence`. A cause offered in `## Issue` or `## Thread highlights` is a claim to test against that evidence, not evidence itself. Live mode: the cause in `plan.md` against the student's posted repro comment on the issue. | The cause is **grounded and consistent**. Grounded: something in the package supports it (a repro result, a quoted code line, a trace), or the plan labels it a hypothesis and names the step that would confirm it. Consistent: no repro step or control contradicts it. A contradiction is a step where the bug appears with the blamed mechanism absent or bypassed, or where the blamed mechanism is shown working while the bug persists. A cause taken from the thread passes when the repro evidence fits it. Fails on a cause the repro evidence contradicts, however confident the plan or the thread is; on a cause asserted as found with nothing in the package behind it; and when the plan states no cause at all. | required |
| `bounded-scope` | Every change the plan and comment commit to (numbered changes, approach steps, files named, anything promised for the PR), plus any in-scope and not-in-scope lines, read against the issue's reported behavior and the repro evidence. Live mode: the Scope, Files, and Approach sections of `plan.md`, and `comment.md`. | The plan is **one bounded change**: each item removes the reproduced failure, tests it, or documents the fix itself (a doc line or changelog entry for the changed behavior), and nothing the plan's own cause implicates is ruled out of scope. A not-in-scope line is welcome but not required when the change list is already one change, and deferring related work to its own issue or PR passes. Fails when the plan folds in work the bug does not need (a refactor or rewrite, renames, a dependency or toolchain upgrade, a migration, CI or build-matrix changes, a new abstraction or cross-cutting redesign, a "while I'm here" cleanup), even when the bounded fix is in there too. Also fails when the plan excludes the code its cause or the repro evidence points at. | required |
| `stranger-can-start` | The plan's change or approach lines: each file, function, or code site it names, and what it says will change there. Live mode: `plan.md` only; files the draft does not name or quote do not count. | A stranger could make the first edit **without asking the author anything**: the plan names where the change goes (a file, a function, or a site the repro evidence or thread pins down) and what the change does there. Terse is fine; one line can pass. Fails on exploration in place of a plan ("poke around", "figure out where", "somewhere in the editor code"), on a change described only by the outcome it should have ("make undo work across toggles"), and on a plan that names no location at all. | required |
| `test-observes-fix` | The plan's test plan (a `Test:` line or a test-plan section), read against the steps and the Expected and Actual lines in `## Repro evidence`. Live mode: the test plan in `plan.md` against the steps and output in the student's repro comment. | The test plan **re-runs the reproduced trigger**, by hand or as an automated test, and names the result that shows the bug is gone: the repro's Expected at the step where Actual differed (a value, an output, an exit status, a timing, a visible change). A manual re-run counts as fully as an automated test, and so do the issue's own cases turned into regression tests. Extra cases are welcome but not required. Fails when the only test is running the existing suite or checking that nothing regresses; when an added test never exercises the trigger (a smoke test that only asserts setup, registration, or configuration); when no observable result is named ("it works"); and when there is no test plan. | required |
| `thread-direction-engaged` | The plan and the plan comment, read against the `## Thread highlights` entries by maintainers (OWNER, MEMBER, or COLLABORATOR) and any open or linked PR the thread names. Live mode: the issue thread via `gh`, using each comment's author association; on Path Review, classmates' claims, plans, and PRs are not maintainer direction (scope.md house rules). | When a maintainer gave **explicit direction** (isolated the culprit, named a preferred approach or location, ruled an approach out, said what they would or would not accept, posted a patch or build and asked for testing), or the thread names an open PR for the same fix, the plan follows it or the comment engages it: it says it follows, or says why it departs and how it relates. **Silence passes** when the thread has no such direction. Fails when the plan or comment proceeds as if the direction were not there: it proposes what a maintainer ruled out without addressing it, works around a culprit a maintainer isolated, or races an open PR without mentioning it. | required |
| `policy-met` | The "contribution policy" bullet under `## Repo facts`, read against the plan comment. Live mode: `docs/CONTRIBUTING.md` (Path Review keeps it there), `.github/`, and any `AI_POLICY.md`, `AI_USAGE_POLICY.md`, or `AGENTS.md`, read against `comment.md`. | When the policy **requires** something of contributors' comments or contributions, the comment meets it. The live case is AI use: a required disclosure statement must be present, and a rule that comments be written by humans in their own words is met unless the comment itself says it was generated. **Silence passes**, and so does a policy that only describes how maintainers work ("reviews outside PRs selectively", "AI-generated PRs are hard to assess"): that is context, not a requirement on the comment. Fails when a required disclosure or other stated requirement is missing. | required |
| `risks-named` | The plan's risks, unknowns, or open questions, and any statement of certainty in the plan or comment. | The plan names at least one way the fix could be wrong or incomplete, or one assumption it will check before relying on it. | preferred |

## Verdict rule

Accept only if **every `required` check passes**. A single required `fail` holds the package.

`unclear` on a required check counts as **fail**: a plan I cannot verify from the package is not
ready to build from.

`preferred` checks are reported but never change the verdict.

### Where the lines sit, and why

- **The repro evidence is the fixed point.** A plan is graded against the reproduction it builds
  on. A cause from the issue or the thread is a claim until that evidence fits it; a confident
  thread comment does not outrank a control run that rules it out.
- **Content, not headings.** A plan made of three lines (`Cause:`, `Change:`, `Test:`) can pass
  every check, and a long plan with every heading can fail. Each check reads what the plan says,
  wherever it says it.
- **A manual test is a test.** What matters is that it re-runs the trigger and names what will
  change. Requiring an automated test would fail an honest manual re-run and still pass a smoke
  test that never touches the bug.
- **Scope is judged by what the bug needs.** Small extra work is still extra. Deferring it is
  fine; folding it in is not. Leaving out the code the cause blames is a scope failure too.
- **Direction comes from maintainers.** Thread comments by people who maintain the repo, and open
  PRs for the same fix, are what a plan comment has to engage. Other contributors' guesses are
  claims for `cause-fits-repro`, not direction.
