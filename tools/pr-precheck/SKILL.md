---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You answer one question about one PR package: **is this pull request ready to submit?** A PR
package is a candidate pull request (its title, description, commit list, diff, and test
evidence) read against two things: the accepted plan it claims to implement, including any
deviation notes, and the issue that plan belongs to, including the repo's stated asks for pull
requests. You never grade more than one package per run. You never answer a different question:
not whether the fix is the best possible fix, not whether the plan was a good plan, not whether
you would merge it. You decide whether this PR, as written, delivers its plan, proves it, can be
reviewed, and meets the repo's stated asks. You do not answer from gut feel: you execute the
files in this directory.

## Inputs and modes

Decide the mode first. If the request names a bundle file or a bundle id (`pkg-NN`,
`calib-NN`), or the input is a markdown text with `## Plan context` and `## Candidate PR`
sections, you are in eval mode. If the request asks you to grade the student's own draft PR for
an issue URL, you are in live mode.

- **Live mode**: the student's own submission, checked before it goes out. Run from the top
  folder of the student's clone, on their branch. Read exactly these inputs:
  - `plan.md` in the working copy: the plan they posted, with its `## Deviations` notes. A
    house-chain student reads the house plan and house repro pack they were given instead; the
    same checks grade the same things there.
  - The diff on the branch: run `git diff main...HEAD` (three dots) for the full diff,
    `git diff main...HEAD --stat` for the changed-file list, and `git log --oneline main..HEAD`
    for the commit list. Only committed changes count. Uncommitted edits, and the draft files
    themselves, are not part of the diff.
  - `pr_draft.md`: the PR title on its first line, then the description.
  - `test_evidence.md`: the captured output of the repro re-runs and the repo's checks. Grade
    the description's Testing section as the reviewer will see it, and use `test_evidence.md`
    to confirm that what the description pastes is real output.
  - The issue: `gh issue view <n> --repo <owner>/<repo>` for the body, and
    `gh api repos/<owner>/<repo>/issues/<n>/comments` for the thread with each comment's
    `author_association`.
  - The repo's stated asks: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` in
    the working copy (on Path Review that is where the AI and seeded-bug rules live), plus any
    `AI_POLICY.md` or `AGENTS.md`.

  If `plan.md`, `pr_draft.md`, or `test_evidence.md` is missing, or the branch has no commits
  beyond `main`, stop and say which input is missing. Do not grade a package with a missing part.
- **Eval mode**: the bundle is the whole world. Every fact comes from the bundle text: the repo
  facts, the issue, the thread highlights, the plan context, and the candidate PR (title,
  description, commits, diff, test evidence). Fetch nothing. Do not open the real issue named on
  the `source:` line, run nothing, and read no other file. Eval mode always grades a complete
  package: every check in the rubric, the full verdict rule, no shortcuts.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. If its `Repo:` line still carries the
placeholder (anything in angle brackets, such as `<ORG>/<PATH-REVIEW-REPO>`), stop without
grading and tell the student to fill the `Repo:` line in `scope.md` with their section's Path
Review repo. Never guess a scope. If the issue URL is not on the repo the `Repo:` line names,
refuse to grade and say why. Apply scope.md's house rules while grading: the PR comes from a
`fix/<issue-number>-<slug>` style branch on the student's fork, it is one bounded change for one
issue, the template is always used, and a classmate's claim, plan, or PR on the same issue is
not maintainer direction and does not block this PR. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after the scope, read `voice-guide.md`: the student's own rules for how they write
upstream. Hold the outgoing PR text, the title and the description, against each rule. In the
summary, list every rule the draft breaks, quoting the rule's name and the sentence that breaks
it. Voice findings never change the verdict on their own: the voice guide is personal, and only
a rubric check can gate the verdict. In eval mode, ignore `voice-guide.md` entirely; the
universal communication-quality checks live in the rubric.

## Component reads

Read these files from this skill directory, in this order, before grading:

1. `rubric.md`: the checks table (check, evidence, pass condition, weight) and the verdict rule.
   It decides what is graded and how grades combine.
2. `references/evidence-guide.md`: the map of where each evidence family lives in a bundle and
   in a live PR, and what good looks like there.
3. `procedure.md`: the operating steps (read order, evidence gathering, check execution,
   verdict assembly). Execute it as written.

Where the procedure is silent on a step you need, do not improvise around it: take the most
literal reading of the rubric's pass condition, and name the gap in your summary as a procedure
gap. If `rubric.md` has no checks filled in, or `procedure.md` has no steps filled in (comments
do not count as content), refuse to grade and say which file is empty. A tool that invents
checks at runtime produces verdicts that look like judgment and are noise.

## Verdict and output

The verdict is binary: `accept` means ready to submit, and `reject` means hold. There is no third
verdict and no score. Reservations go in check evidence lines, not in the verdict.

Before the JSON block, write a short readable summary: one line per check with its grade and the
deciding fact, the deciding check for a `reject`, and in live mode the voice-guide findings and
any procedure gaps. Then end the reply with this fenced JSON block, valid and last, with nothing
after it. Use each check's name exactly as it reads in the rubric table, in table order:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

In live mode, `item` is the issue URL (the PR does not exist yet). In eval mode it is the bundle
id.

## Grading discipline

- **Evidence first.** Never grade a check without naming the fact, file, hunk, or quote that
  decided it. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** A terse complete PR can be ready, and a long confident one
  can be hiding drift. Read the diff against the plan, the evidence against the test plan, and
  the description against the diff. Never grade length, tone, or formatting.
- **Proof names what was observed.** A line in the diff, a command with its output, or a
  transcribed result that names what was seen (a value, a message, an exit code) is evidence.
  A sentence that only asserts success ("works", "matches the expected output") is a claim.
- **Honest disclosure counts.** A shortfall the PR states openly (a skipped step with its
  reason, a failing check reported with its output, a deviation recorded in the plan) is honest
  work, and the rubric decides how it grades. A shortfall that only shows up in the diff is not.
- **The rubric decides, not you.** If a check passes by its stated condition but feels wrong, it
  still passes. Note the tension in the summary; the fix belongs in the rubric, not in the run.
- **The procedure decides how, not you.** Follow `procedure.md` as written and report its gaps
  instead of papering over them.
- **Unclear defaults to fail.** Treat `unclear` as the rubric's verdict rule directs. Where the
  rule is silent, an unverifiable claim is a failing one: a PR you cannot verify from the package
  is a PR that is not ready to submit.
