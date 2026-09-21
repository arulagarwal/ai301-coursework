# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `repo-alive` | The `archived:` flag on the repo line, and the newest date in the "last 5 default-branch commits" list under Repo facts. Live mode: the archive banner, and the newest commit date above the file list on the repo front page. | `archived: no`, **and** the newest default-branch commit is dated within **90 days** of the capture date stamped at the top of the bundle (in live mode, within 90 days of today). Release recency is deliberately not used here: a repo can publish no releases at all and still be alive, so commits carry liveness. | required |
| `unclaimed` | The `this issue:` line under Repo facts (`assignees:` and `linked PRs:` with a state per PR), plus the Comments section. | `assignees: none`, **and** no linked PR in state `open`, **and** no comment claiming the work ("I'll take this", "working on this") that is both **dated within 90 days** of capture and **unanswered by a maintainer**. A linked PR in state `closed` or `merged` is an abandoned or already-landed attempt, not a claim, and does not fail this check. | required |
| `bounded-work` | The issue title and body. | The issue asks for work that could land as **one pull request**. It fails on exactly two shapes: (a) the body lists **references to other GitHub issues** (`#1234`, `#5678`) as the work to be done, or the title or body calls it a megaissue / umbrella / tracking / epic issue; (b) the ask applies to a **whole class of code** rather than named sites ("add type annotations to the library", "write the missing pages"). Nothing else fails it. In particular, an issue that touches **several files**, fixes **several root causes of one symptom**, or carries a **list of suggestions or acceptance criteria** inside a single task is bounded and **passes** — that is one pull request, not many. A terse body, a bare checklist, or a bug report with no reproduction steps also passes: grade the size of the work asked for, not the polish of the writeup. | required |
| `maintainer-vetted` | The `labels:` field on the `opened by` line, the opener's association in parentheses, and the author_association of each comment. | At least one of: the issue carries **≥1 label**; the opener is `OWNER`, `MEMBER`, or `COLLABORATOR`; or a commenter with one of those associations has engaged with the request rather than deflecting it. An unlabelled issue opened by a `NONE` account that no maintainer has answered fails. | required |
| `settled-design` | The Comments section: total count, author associations, and whether a maintainer states a resolution. Plus any prerequisite the body leaves open. | No unresolved design debate. Fails when the thread runs past **20 comments** without a maintainer settling the approach, when a maintainer says the fix reaches core internals, or when the body defers a prerequisite the work depends on ("asset TBD", "design TBD"). Pure usage questions ("how do I get this to work?") fail here too: they are support requests, not contributions. | required |
| `ai-policy-permits` | The "contribution policy" line under Repo facts. Live mode: `CONTRIBUTING.md` in the repo root or `.github/`, the contributor docs it links to, and any `AI_POLICY.md` / `AI_USAGE_POLICY.md`. | No outright ban on AI-generated or AI-assisted contributions. **Conditions are not bans** — disclosure, personal understanding, testing, and human-review requirements all pass, they are terms to follow. **Silence passes**: most repos state nothing, and that is not a restriction. Only a stated refusal ("we do not accept AI-generated code") fails. | required |
| `maintainer-responsive` | The "maintainer first-response sample" block under Repo facts (days to first owner/member/collaborator comment on 5 recently updated issues). | At least two of the sampled issues got a first maintainer reply within 30 days. | preferred |
| `small-diff-signal` | The issue body and labels. | The issue names the file, function, or UI surface to change, or carries a maintainer-applied difficulty label (`good first issue`, `easy`, `Easy to Fix`). | preferred |
| `reproducible` | The issue body. | The body gives a runnable reproduction — a command, a code snippet, a numbered click-path, or an observed-vs-expected pair. | preferred |

## Verdict rule

Accept if **every `required` check passes**. A single required `fail` rejects the issue.

`unclear` on a required check counts as a **fail**: a first issue whose evidence I cannot verify is
not a first issue I should take.

`preferred` checks never change the verdict. They rank the issues that were accepted, and a
`preferred` fail is never a reason to reject.

### Three signals deliberately kept out of the required set

- **Maintainer response latency is `preferred`, not `required`.** I first wrote it as required at
  "two of five sampled issues answered within 30 days". Against the eval set that condition would
  have rejected five of the eight issues the gold labels accept — conda/conda samples as
  `32.9 days, none, none, none, none` while committing daily. Most issues in a healthy repo never
  get a maintainer reply at all, so the sample measures thread luck, not liveness. `repo-alive`
  carries liveness on commit recency instead, where the separation is clean.
- **A `good first issue` label is not evidence that an issue is free or small.** It appears on
  issues this rubric rejects under `unclaimed`, `bounded-work`, and `ai-policy-permits`. It is
  scored only as a `preferred` signal, under `small-diff-signal`.
- **Issue age is absent from every required check.** An issue can sit open for years in a healthy
  repo simply because nobody got to it, and one abandoned closed PR in its history does not make it
  hard. Age is graded only indirectly, through `settled-design` (is the thread still arguing?) and
  `unclaimed` (is someone on it *now*?).
