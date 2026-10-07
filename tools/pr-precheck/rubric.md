# Rubric: is this pull request ready to submit?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `files-in-plan` | Every file in the candidate PR's diff (each `--- a/` and `+++ b/` pair), read against the plan's Files list, its scope and approach lines, and any deviation note. Live mode: `git diff main...HEAD --stat` against the Files, Scope, Approach, and `## Deviations` sections of `plan.md`. | Every changed file is **asked for**: the plan names the file or its folder, the plan's change or test plan clearly needs it (a test file in the test folder for a plan that promises tests, a changelog entry for a plan that promises one), or a deviation note or the PR description names the extra file and says why the fix needed it. Fails when the diff changes a file the plan never names and nothing in the PR explains it, even when the change looks harmless. Explaining an unrelated cleanup does not make it asked for. | required |
| `plan-delivered` | Every change and every test the plan commits to (approach steps, files named, tests named in the test plan, a promised changelog or doc entry), each looked for in the diff. Live mode: the Approach, Files, and Test plan sections of `plan.md` against `git diff main...HEAD`. | Each item the plan commits to is **in the diff, or its absence is disclosed**: the description or a deviation note says it was left out or changed, and why. The change in the diff does what the plan says at that site (the same mechanism, the same boundary), not a different fix under the plan's name. Fails when a promised change, test, or entry is missing with no word about it, and when the diff swaps in a different approach than the plan without saying so. | required |
| `description-true` | Each claim in the PR title and description (what changed, which files, which tests were added, "no other changes", "implements the plan exactly", "no options added", a ticked checklist box), read against the diff and the commit list. | Every claim the PR makes about itself is **true of the diff**. A claim that something was not added is true when nothing in the diff adds it. Fails on any false claim: a test named as added that the diff has no file or hunk for, a "nothing beyond the plan" next to a diff that does more, a ticked box the diff or evidence contradicts, or a summary that describes a different change than the diff makes. | required |
| `fix-shown` | The candidate PR's test evidence, read against the plan's test plan and the reproduction's trigger and expected result. Live mode: the Testing section of `pr_draft.md`, checked against `test_evidence.md`. | The evidence **re-runs the reproduced trigger on the branch and reports a specific observed result** that is the plan's expected-after: a value, message, row, exit code, or visible state someone could check against the plan. Printed output, a transcribed session, or a note after the command all count when they name what was observed. The before may come from the plan's repro evidence. Every distinct failure mode the test plan names is re-run, or the PR says which one was not and why. Fails when the after names nothing observed (only "works now", "fixed", or "matches the expected output" with no observed content), when no evidence is given, when the evidence never exercises the trigger, or when a named failure mode is silently skipped. | required |
| `checks-run` | The test evidence and description, read against the suite or checks the plan's test plan names and the checks the repo facts ask for (a test command, a linter, a type checker, pre-commit). Live mode: the four commands the Path Review PR template lists, in `test_evidence.md` and the Testing section. | Each suite or check the plan or the repo asks for is **reported as run, with its outcome**: the command or suite is named and the PR says how it came out (passes, a count, printed output, or a failure with its reason). A failing check that is disclosed passes this check. Fails when an asked-for suite or check is never mentioned, when the PR says it was not run without a reason, and when the only word on testing is a generic "tests pass" naming no suite or command. When neither the plan nor the repo names any suite or check, this check passes. | required |
| `diff-clean` | Every hunk in the diff and every entry in the commit list. | Every hunk **serves the change**: the fix, its tests, or its changelog or doc entry. Fails on debris (a debug print or log line, commented-out code, a leftover TODO or scratch file), on churn unrelated to the fix (reformatting, reordering, or renaming lines the fix does not touch), on a drive-by refactor of code the fix does not need, and on a dependency, lockfile, or toolchain bump the plan did not call for. | required |
| `repo-asks-met` | The "pull requests" bullet under the bundle's repo facts (template sections, checklist items, title conventions, an issue link, a changelog or whatsnew entry), read against the PR title, description, and diff. Live mode: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` against `pr_draft.md`. | Each ask the repo states for a pull request is **met or marked not applicable with a reason**: every template section has real content, the issue is linked the way the repo asks (`Closes #N`, `fixes #N`), a required entry file is in the diff, and a stated title convention is followed. When the repo has no template and no stated asks, this check passes. Fails when a stated section, checklist item, link, or entry is missing, left as the template's placeholder text, or ticked while the diff or evidence contradicts it. | required |
| `policy-and-direction-honored` | The "contribution policy" bullet under the repo facts and the maintainer entries (OWNER, MEMBER, COLLABORATOR) in the thread highlights, read against the PR description and the diff. Live mode: `docs/CONTRIBUTING.md`, any `AI_POLICY.md` or `AGENTS.md`, and the issue thread with author associations; on Path Review, classmates' comments and PRs are not maintainer direction. | Every rule the repo's policy **requires** of a contribution is met, and every explicit maintainer direction in the thread is followed or answered in the description. The live case is AI use: when the policy requires a disclosure (the tool used, how much it did), the description carries one; when it requires human-written text, nothing in the PR says the text was generated. A rule the diff can break (no private APIs, no new features, no new linters without an approved discussion) is met when the diff does not break it. **Silence passes**: no stated policy and no maintainer direction means nothing to meet. Fails when a required disclosure is missing, when the diff does what the policy or a maintainer ruled out, or when the PR proceeds as if a maintainer's direction were not there. | required |
| `title-names-change` | The PR title. | The title names the behavior the PR changes, specific enough that a maintainer scanning the PR list could tell it from other fixes in the repo. | preferred |

## Verdict rule

Accept only if every required check passes. A single required fail holds the package, because a
maintainer can only merge what they can verify and every required check names a way the PR
cannot be verified or should not be merged as it stands.

An unclear grade on a required check counts as fail: a PR I cannot verify from the package is a
PR that is not ready to submit.

Preferred checks are reported in the summary and the JSON block but never change the verdict.

An honestly disclosed shortfall is not a fail by itself. A skipped test, a check that failed, or
a step changed from the plan passes the check that reads it when the description or a deviation
note says what happened and why. Less than everything, said plainly, can be ready to submit;
less than everything, said nowhere, is silent drift.

### Where the lines sit, and why

- **Proof names what was observed.** A transcribed after ("toast shown, file still listed",
  "exit code 0, both parameters present") is checkable against the plan and counts. calib-03's
  after says only that it "matches the issue's expected output byte for byte", which names
  nothing observed, so it fails. My first draft demanded pasted output and rejected four ready
  PRs whose after-results were specific but transcribed.
- **Every changed file needs a line in the plan.** calib-03's `property.js` rewrite was tidy and
  explained nowhere. An unrelated change fails `files-in-plan` and `diff-clean` even when it is
  explained, because it buries the fix; an extra file the fix needed passes once it is
  explained.
- **Missing work fails only when it is silent.** `plan-delivered` and `fix-shown` both accept a
  disclosed gap, so the honest PR that says what it left out is not graded like the one that hid
  it.
- **The repo's asks are the bar.** Template sections, issue links, changelog entries, and
  AI-use disclosure are graded only where the repo states them, so a repo with no template is not
  held to one.
