# Procedure: how this skill grades a plan package

Both modes follow these steps. Eval mode reads one bundle and nothing else. Live mode reads
GitHub and the student's two drafts; the steps marked **Live mode** say where each part comes
from there.

## Read order

1. Read `## Repro evidence` first, before anything the plan says. Write down the environment;
   each numbered step and what it showed; every control run (the same trigger with one factor
   changed) and its result; the Expected and Actual lines; and which factor the evidence shows
   switching the bug on or off.
2. Read `## Issue`. Write down the reported behavior and its trigger. If the reporter guesses a
   cause, write it down marked as a guess.
3. Read `## Thread highlights`. List each comment by an OWNER, MEMBER, or COLLABORATOR with the
   direction it gives: a culprit isolated, an approach or location preferred, an approach ruled
   out, a patch or build to test, what they will or will not accept. List every open or linked
   PR the thread names. A cause offered by anyone else is a claim, not evidence.
4. Read `## Repo facts`. Copy the "contribution policy" bullet word for word, and write down any
   requirement it places on comments or contributions (an AI-use disclosure, human-written
   comments, a template).
5. Read `## Candidate plan`.
6. Read `## Candidate plan comment` last.

Why this order: the repro evidence and the thread are in my notes before I read the plan's
story, so a confident plan cannot reframe them. A procedure that reads the plan alone passes
calib-03, whose cause the repro had already ruled out.

**Live mode.** The same order, from these sources:

1. The student's own repro comment on the issue (`gh issue view <n> --repo <owner>/<repo> --comments`).
   A student on a house issue has none; use the repro pack as the drafts quote it.
2. The issue body.
3. The thread with author associations:
   `gh api repos/<owner>/<repo>/issues/<n>/comments --jq '.[] | {user: .user.login, association: .author_association, created: .created_at, body}'`.
   On Path Review, classmates' claims, repros, plans, and PRs are not maintainer direction
   (`scope.md` house rules).
4. `docs/CONTRIBUTING.md`, `.github/`, and any `AI_POLICY.md`, `AI_USAGE_POLICY.md`, or
   `AGENTS.md` in the repo.
5. `plan.md`.
6. `comment.md`.

Grade the drafts the way a stranger on the thread would read the posted comment: only what the
drafts contain or quote counts, not other files in the working directory.

## Evidence gathering

Find each part by what it says, not by its heading. A cause may sit under "Diagnosis", after
"Cause:", under "What I found", or only in the comment.

1. `cause-fits-repro`: copy the plan's cause sentence and name the mechanism it blames. For each
   repro step and control in my read-order notes, write two marks: was the blamed mechanism
   active, and did the bug appear? Write down what supports the cause (a repro result, a quoted
   code line, a trace), or that the plan labels it a hypothesis.
2. `bounded-scope`: list every change item the plan and comment commit to, including files named
   and anything promised for the PR. Tag each item: removes the failure, tests it, documents the
   fix, or beyond the issue. Copy any in-scope and not-in-scope lines, and check the not-in-scope
   line against the mechanism the cause blames.
3. `stranger-can-start`: list each location the plan names (file, function, or code site) with
   the action it states for that location.
4. `test-observes-fix`: copy the test plan. From the repro notes, find the step where Actual
   differed from Expected. Write down whether the test plan re-runs that trigger and whether it
   names that Expected, or an equivalent observable, as the sign the bug is gone.
5. `thread-direction-engaged`: for each maintainer direction and open PR from read-order step 3,
   find the sentence in the plan or comment that follows or answers it, or write "none".
6. `policy-met`: for each requirement copied in read-order step 4, quote the sentence in the
   comment that meets it, or write "none".
7. `risks-named`: copy the plan's risks, unknowns, or open questions, if it has any.

## Check execution

1. Run the checks in rubric-table order: `cause-fits-repro`, `bounded-scope`,
   `stranger-can-start`, `test-observes-fix`, `thread-direction-engaged`, `policy-met`, then
   `risks-named`.
2. Grade each check only against its own pass condition and its own gathered evidence. Do not
   fail a later check because an earlier one failed: a wrong cause fails `cause-fits-repro`, and
   every other check passes or fails on its own condition.
3. Grade `pass`, `fail`, or `unclear`, with one line quoting the fact or text that decided it.
   "Looks fine" is not evidence.
4. If the part a check needs is absent (no cause, no change location, no test plan), grade
   `fail` with the evidence "absent: <what is missing>". Use `unclear` only when the part exists
   but the package does not hold enough to decide, and say what is missing.
5. When the pass condition and a gut feeling disagree, the pass condition wins. Note the tension
   in the summary; the fix belongs in the rubric, not in this run.

## Verdict assembly

1. Apply the rubric's verdict rule: `accept` only if every `required` check is `pass`.
2. Count `unclear` on a required check as `fail`.
3. Report `preferred` checks, but never let them change the verdict.
4. Write the summary, one line per check. For a `reject`, name every required check that did not
   pass, with its quote. For an `accept`, quote the deciding evidence for `cause-fits-repro` and
   `test-observes-fix`.
5. **Live mode:** in the summary, also check `comment.md` against `voice-guide.md` and list each
   rule it breaks, quoting the rule. This never changes the verdict.
6. End with the fenced JSON block from SKILL.md, last in the output, with each check's name
   exactly as it reads in the rubric table.
