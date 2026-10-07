# Evidence guide: where evidence lives in a PR package

An eval bundle has five parts, always in this order: `## Repo facts`, `## Issue`,
`## Thread highlights`, `## Plan context`, and `## Candidate PR`. The candidate PR is split
into `### Title`, `### Description`, `### Commits`, `### Diff`, and `### Test evidence`. The plan
is the fixed point: every family below is judged by setting part of the PR against the plan, or
against the repo's stated asks. In live mode the plan is `plan.md`, the diff is
`git diff main...HEAD` on the student's branch, the title and description are `pr_draft.md`, the
captured output is `test_evidence.md`, and the repo's asks are in the working copy and on the
issue.

## Plan fidelity (harness category: silent-drift)

**Where it lives.**
- Eval: the Files list, scope and not-in-scope lines, approach, and test plan in
  `## Plan context`, set against each `--- a/` and `+++ b/` file pair in `### Diff`. The
  description's claims of fidelity ("implements the plan exactly", "no changes beyond it",
  "tests added for X and Y") sit in `### Description` and its checklist.
- Live: the Files, Scope, Approach, Test plan, and `## Deviations` sections of `plan.md`, set
  against `git diff main...HEAD --stat` and the full diff. The description's claims are in the
  Summary, Changes, and Testing sections of `pr_draft.md`. For codepath/pathreview-ai301-fa26-s3#54
  the plan names two files, `ingestion/parsers/resume_parser.py` and
  `tests/unit/test_resume_parser.py`.

**What good looks like.** Every changed file falls inside the plan's Files list or folder, or a
deviation note names it with a reason. Every change and test the plan promised is in the diff,
or the description says it was left out and why. Each claim the description makes about the
diff is true when you look. Silent drift runs both ways: in calib-03 the diff rewrites
`property.js`, which no plan line asks for, and leaves out the all-comments control test the
test plan names, while the description says "with no changes beyond it".

## Test evidence (harness category: not-tested)

**Where it lives.**
- Eval: `### Test evidence`, set against the plan's test plan and the repro evidence in
  `## Plan context`, and against any test or check command the "pull requests" bullet in
  `## Repo facts` names. A test file added in `### Diff` is a test that exists; the evidence
  section is where it was shown to run.
- Live: `test_evidence.md` and the Testing section of `pr_draft.md`. Path Review's template
  lists `make test-unit`, `make test-integration`, `make lint`, and `make typecheck`. The
  plan's test plan adds the issue's snippet, the control without indentation, and the five #54
  tests.

**What good looks like.** The repro's trigger is re-run on the branch, and the PR reports what
was observed, specific enough to check against the plan's expected-after. calib-01 shows
`parsed OK` under the configparser command; a transcribed "exit code 0, browser shows both
query parameters" is just as checkable. Each failure mode the plan's test plan names gets its
own re-run, or the PR says which one it skipped and why. The repo's own checks are named with
their outcome: passes, a count, output, or a failure with its reason. An after that names
nothing observed ("# after: matches the issue's expected output byte for byte", "works now") is
a claim, and so is "tests pass" with no suite or command named.

## Diff quality (harness category: unreviewable)

**Where it lives.**
- Eval: every hunk in `### Diff` and every line in `### Commits`.
- Live: `git diff main...HEAD` and `git log --oneline main..HEAD`.

**What good looks like.** Every hunk is the fix, its test, or its changelog or doc entry, so a
reviewer reads only the change. The debris tells are: debug prints and log lines, commented-out
code, leftover TODOs or scratch files, reformatting or reordering lines the fix does not touch,
a refactor of a neighbouring function, and a dependency or lockfile bump. calib-02 fails here
three times: a `tcell` version bump in `go.mod` and `go.sum`, and a "gofmt" commit that merges
assignments in `gui.go` that the fix never needed.

## Standards and comms (harness category: standards-wall)

**Where it lives.**
- Eval: the "pull requests" bullet in `## Repo facts` (template sections, checklist items, title
  conventions, issue-link form, changelog or whatsnew entry) and the "contribution policy"
  bullet (AI-use rules, code rules, CLA), set against `### Title`, `### Description`, and
  `### Diff`. Maintainer direction is in `## Thread highlights`, in entries marked OWNER,
  MEMBER, or COLLABORATOR.
- Live: `.github/PULL_REQUEST_TEMPLATE.md` (Summary, Issue with `Closes #`, Changes, Testing
  checklist, Screenshots / Demo, Notes for Reviewers) and `docs/CONTRIBUTING.md`, set against
  `pr_draft.md`. The course also asks for an AI-use disclosure in Notes for Reviewers. The
  thread is `gh api repos/<owner>/<repo>/issues/<n>/comments`, read with each
  `author_association`.

**What good looks like.** Every section the repo's template asks for has real content, or "N/A"
with a reason. The issue is linked in the form the repo asks for. A required changelog or
whatsnew entry is in the diff. A stated title convention is followed. When the policy requires
an AI-use disclosure, the description names the tool and what it did. A code rule the policy or a
maintainer states (no private APIs, no new features) is not broken by the diff, and explicit
maintainer direction in the thread is followed or answered. Placeholder text left in a section, a
ticked box the evidence contradicts, or a skipped required entry is the wall. (Whether the
description's claims match the diff belongs to plan fidelity, above.)
