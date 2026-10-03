# Evidence guide: where evidence lives in a plan package

An eval bundle has six parts, always in this order: `## Repo facts`, `## Issue`,
`## Thread highlights`, `## Repro evidence`, `## Candidate plan`, `## Candidate plan comment`.
The repro evidence is the fixed point: every family below is judged by setting the plan or the
comment against it, or against the thread and the repo facts. In live mode the same parts come
from GitHub (the issue, its thread, the student's posted repro comment, the repo's files) and from
the student's two drafts, `plan.md` and `comment.md`.

## Diagnosis and grounding

**Where it lives.**
- Eval: the cause in `## Candidate plan` (a `Cause:` line, a Diagnosis section, or the opening
  summary) and the comment's account of the bug in `## Candidate plan comment`. Set it against
  the numbered steps, the control runs, and the Expected and Actual lines in `## Repro evidence`.
  Causes in `## Issue` and `## Thread highlights` are claims to check, not evidence.
- Live: the Diagnosis in `plan.md` and the cause in `comment.md`. Set it against the student's
  posted repro comment on the issue (for codepath/pathreview-ai301-fa26-s3#54 that is
  `#issuecomment-5847372669`) and any code the drafts quote.

**What good looks like.** The cause names a mechanism, and every repro result fits it: wherever
the bug appeared, that mechanism was active, and wherever a control made the bug go away, the
control changed something the mechanism depends on. Something in the package backs it: a repro
result, a quoted code line, or a trace. A cause borrowed from the thread is fine when the repro
evidence fits it and wrong when a step shows the bug without it. In calib-03, step 3 runs with no
pager at all and still takes 25.8 s, so the pager's key bindings cannot be the cause.

## Scope

**Where it lives.**
- Eval: the change list in `## Candidate plan` (numbered changes, approach steps, files named),
  its in-scope and not-in-scope lines, and anything `## Candidate plan comment` promises for the
  PR.
- Live: the Scope, Files, and Approach sections of `plan.md`, and `comment.md`.

**What good looks like.** One change the bug needs, plus its test and any doc line for the
changed behavior. Work the fix does not need is named as out of scope or deferred to its own
issue, never folded in. The not-in-scope line never excludes the code the cause blames. A
drive-by rewrite, a dependency upgrade, a CI matrix, or a new abstraction wrapped around the fix
is scope creep, however small the core fix inside it.

## Executability

**Where it lives.**
- Eval: the change or approach lines of `## Candidate plan`: the files, functions, and code sites
  it names, and the action it states for each.
- Live: the Approach and Files sections of `plan.md`.

**What good looks like.** A stranger could open the named file and make the first edit without
asking the author anything. "The push completion callback in
`pkg/gui/controllers/sync_controller.go` adds the commits context to its post-push refresh scope"
is a plan. "Poke around the editor components and figure out where the undo history lives" is a
wish, and so is a change described only by its outcome.

## Test plan

**Where it lives.**
- Eval: the test plan in `## Candidate plan` (a `Test:` line or a test-plan section), set against
  the numbered steps and the Expected and Actual lines in `## Repro evidence`.
- Live: the Test plan section of `plan.md`, set against the steps and output in the student's
  repro comment.

**What good looks like.** The test re-runs the repro's trigger and names what will be different:
the Expected value, output, exit status, timing, or visible change at the step where Actual went
wrong. "At step 3 the color must flip without leaving the view" is decisive. A manual re-run
counts as fully as an automated test. "Run the full suite and make sure nothing regresses" says
nothing about this bug, and a test that only checks setup (a list of key bindings, a config value)
never touches the trigger.

## Honesty

**Where it lives.**
- Eval: risks, unknowns, and open questions in `## Candidate plan`, and statements of certainty in
  both candidate texts ("I traced this to", "this will fix", "the cause is").
- Live: the Risks and unknowns section of `plan.md`, its `## Deviations` section after the build,
  and `comment.md`. A build that departs from the posted plan is recorded under Deviations and
  graded again.

**What good looks like.** Certainty is the same size as the evidence. A cause that was shown
reads as found; one that was not reads as a hypothesis, with the step that would confirm it. At
least one way the fix could be wrong or incomplete is named. "I traced this to" over a cause the
repro contradicts is false confidence.

## Comms

**Where it lives.**
- Eval: `## Candidate plan comment` read against the maintainer entries (OWNER, MEMBER,
  COLLABORATOR) and open PRs in `## Thread highlights`, and against the "contribution policy"
  bullet in `## Repo facts`, which is where any AI-use policy and disclosure requirement is
  recorded.
- Live: `comment.md` read against the issue thread
  (`gh api repos/<owner>/<repo>/issues/<n>/comments`, reading each `author_association`) and the
  repo's policy files: `docs/CONTRIBUTING.md` for Path Review, plus `.github/` and any
  `AI_POLICY.md`, `AI_USAGE_POLICY.md`, or `AGENTS.md`. On Path Review the `scope.md` house rules
  apply: classmates' claims, repros, plans, and PRs are not maintainer direction and do not block
  a plan, but a plan comment that leans on one ("same approach as above") is not a plan.

**What good looks like.** When a maintainer pointed somewhere (a culprit, an approach, a
ruled-out path, a patch to test) or an open PR already targets the fix, the comment says how the
plan relates: it follows that direction, or it says why it departs. When the policy requires an
AI-use disclosure, the comment carries one. A comment that ignores what the thread settled, or a
plan that quietly does what a maintainer ruled out, is the failure here.
