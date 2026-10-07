# Procedure: how this tool grades a PR package

Both modes follow these steps. Eval mode reads one bundle and nothing else. Live mode reads the
student's working copy, their branch's committed diff, and the issue on GitHub; the steps marked
**Live mode** say where each part comes from there.

## Read order

1. Read `## Plan context` first, before anything the PR says about itself. Write down:
   - the repro evidence: the trigger, each failure mode it showed, the control, and the
     expected-after;
   - the Files list, word for word;
   - each change the approach commits to, numbered;
   - the not-in-scope line;
   - the test plan, split into one item per run or test it names, with the expected result for
     each;
   - any deviation notes.
2. Read `## Repo facts`. Copy the "pull requests" bullet and the "contribution policy" bullet
   word for word, then list each separate ask they make (a template section, a checklist item,
   an issue-link form, a title convention, a changelog or whatsnew entry, a test or lint
   command, an AI-use disclosure, a rule the code must follow).
3. Read `## Issue` and `## Thread highlights`. List each comment by an OWNER, MEMBER, or
   COLLABORATOR with the direction it gives (an approach preferred or ruled out, a rule
   restated, a request to the contributor). Anyone else's comment is context, not direction.
4. Read the candidate PR's `### Diff`. List every file it changes. For each hunk, write one line
   saying what it does.
5. Read `### Commits`.
6. Read `### Test evidence`. For each block, write the command, and next to it whether printed
   output follows it or only a note or sentence describing it.
7. Read `### Title` and `### Description` last. List every claim they make about the PR.

Why this order: the plan, the repo's asks, and the diff are in my notes before I read the PR's
own story, so a confident description cannot tell me what the diff does. calib-03's description
says "with no changes beyond it"; read first, that sentence hides the `property.js` rewrite.

**Live mode.** The same order, from these sources:

1. `plan.md` in the working copy, including its `## Deviations` section.
2. `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and any `AI_POLICY.md` or
   `AGENTS.md` in the working copy.
3. `gh issue view <n> --repo <owner>/<repo>` and
   `gh api repos/<owner>/<repo>/issues/<n>/comments --jq '.[] | {user: .user.login, association: .author_association, body}'`.
   On Path Review, classmates' claims, plans, and PRs are not maintainer direction (`scope.md`).
4. `git diff main...HEAD --stat`, then `git diff main...HEAD`.
5. `git log --oneline main..HEAD`.
6. `test_evidence.md`, then the Testing section of `pr_draft.md`.
7. `pr_draft.md`: the title on its first line, then the description.

## Evidence gathering

Find each part by what it says, not by its heading. A plan's files may sit after "Files:", in a
Scope section, or inside the approach steps.

1. `files-in-plan`: make a two-column table. Left: each file from the diff. Right: the line of
   the plan, deviation note, or description that asks for it, quoted, or "none". A file in a
   folder the plan names counts as named.
2. `plan-delivered`: for each numbered change and each test-plan item from read-order step 1,
   write the diff hunk that delivers it, or the sentence in the description or deviation notes
   that says it was left out, or "missing". For each change, write whether the hunk uses the
   mechanism the plan named.
3. `description-true`: for each claim from read-order step 7, write the hunk, file, or evidence
   block that makes it true, or what contradicts it. For a claim that something was not added,
   search the diff for it.
4. `fix-shown`: for each test-plan run from read-order step 1, find its re-run in the evidence.
   Write the command, whether real printed output follows it, and whether that output is the
   expected-after. Mark any failure mode the plan names that has no re-run, and whether the PR
   says why.
5. `checks-run`: list every suite or check the plan's test plan or the repo's asks name. For
   each, write the outcome the PR reports (output, a count, a pass or fail, a failure with its
   reason) or "not mentioned".
6. `diff-clean`: from the hunk notes in read-order step 4, mark each hunk as fix, test,
   changelog or doc, or other. For each "other", quote the lines (debug output, commented-out
   code, reformatting, a refactor, a dependency bump).
7. `repo-asks-met`: for each pull-request ask from read-order step 2, quote where the PR meets
   it, write "n/a: <reason the PR gives>", or write "missing".
8. `policy-and-direction-honored`: for each policy requirement from read-order step 2 and each
   maintainer direction from step 3, quote the description sentence or diff hunk that meets or
   answers it, or write "none". For a rule the code must follow, search the diff for anything
   that breaks it.
9. `title-names-change`: copy the title.

**Live mode.** The diff is only what `git diff main...HEAD` shows. If the description quotes test
output, find the same output in `test_evidence.md`; output in the description that is not in
`test_evidence.md` is a claim, not proof.

## Check execution

1. Run the checks in rubric-table order: `files-in-plan`, `plan-delivered`, `description-true`,
   `fix-shown`, `checks-run`, `diff-clean`, `repo-asks-met`, `policy-and-direction-honored`,
   then `title-names-change`.
2. Grade each check only against its own pass condition and its own gathered evidence. One
   problem can fail more than one check when each check's condition is broken (an unrelated file
   can fail both `files-in-plan` and `diff-clean`), but never fail a check because a different
   check failed.
3. Grade `pass`, `fail`, or `unclear`, with one line quoting the fact that decided it. Name the
   file, the hunk, or the sentence. "Looks fine" is not evidence.
4. If the part a check needs is absent (no test evidence, no description, no plan Files list),
   grade `fail` with the evidence "absent: <what is missing>". Use `unclear` only when the part
   exists but the package does not hold enough to decide, and say what is missing.
5. Before failing any check for a gap, look for a disclosure: a sentence in the description or a
   deviation note that names the gap and gives a reason. If one exists, apply the pass
   condition's disclosure clause.
6. When the pass condition and a gut feeling disagree, the pass condition wins. Note the tension
   in the summary; the fix belongs in the rubric, not in this run.

## Verdict assembly

1. Apply the rubric's verdict rule: `accept` only if every `required` check is `pass`.
2. Count `unclear` on a required check as `fail`.
3. Report `preferred` checks, but never let them change the verdict.
4. The deciding check for a `reject` is the first required check in rubric-table order that did
   not pass. Quote its evidence first in the summary, then list every other required check that
   did not pass. For an `accept`, quote the deciding evidence for `files-in-plan` and
   `fix-shown`.
5. **Live mode:** in the summary, check the title and description against `voice-guide.md` and
   list each rule they break, quoting the rule's name and the sentence. This never changes the
   verdict. Also list what the student should fix before opening the PR, one line per failing
   check.
6. End with the fenced JSON block from SKILL.md, last in the output, with each check's name
   exactly as it reads in the rubric table and the checks in table order.
