# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54

**Verdict output**

Live-mode output for all three candidates, pasted verbatim. The fenced JSON array at the end
records `"verdict": "accept"` for issue #54 (second object in the array).

I now have all the evidence needed. Here is the graded read-out.

---

## Summary

**Repo-level facts (shared by all three issues)**
- `isArchived: false`; last push `2026-09-16` (5 days ago — within 90 days of today 2026-09-21). Repo is **alive**.
- No `CONTRIBUTING.md` anywhere in root or `.github/`. No `AI_POLICY.md` or `AGENTS.md`. Silence → **ai-policy permits** for all three.
- Issue author `Aburke225` has `author_association: COLLABORATOR` on every issue. All three carry ≥1 label. `maintainer-vetted` passes for all.
- All three: `assignees: none`, `linked PRs: none`, `comments: 0`. `unclaimed` passes for all.
- All three issues have 0 comments → no debate → `settled-design` passes for all.
- `maintainer-responsive` (preferred): sampled 5 recently updated issues; 0 had any maintainer-association reply. Fails the preferred threshold of 2/5 — noted for all, but preferred checks do not affect verdicts.

---

### Issue #54 — "Resume section detection fails on text with leading whitespace"

| Check | Grade | Evidence |
|---|---|---|
| `repo-alive` | **pass** | Not archived; last push 2026-09-16, 5 days before today |
| `unclaimed` | **pass** | assignees: none, linked PRs: none, comments: 0 |
| `bounded-work` | **pass** | Single function `_detect_sections()` in `resume_parser.py`; one regex anchor fix |
| `maintainer-vetted` | **pass** | Opener is COLLABORATOR; labels: bug, good first issue, ingestion, tier-1 |
| `settled-design` | **pass** | 0 comments; body states the problem and approach unambiguously |
| `ai-policy-permits` | **pass** | No CONTRIBUTING.md or AI policy file; silence passes |
| `maintainer-responsive` *(preferred)* | **fail** | 0 of 5 sampled recently-updated issues had a maintainer reply |
| `small-diff-signal` *(preferred)* | **pass** | "good first issue" + "tier-1" labels; function and file named in body |
| `reproducible` *(preferred)* | **pass** | Runnable Python snippet + observed `[]` vs expected `Education, Skills`; 3 named failing tests |

**Verdict: ACCEPT**

---

### Issue #59 — "Faithfulness checker scores claims unsupported when the context uses different words"

| Check | Grade | Evidence |
|---|---|---|
| `repo-alive` | **pass** | Not archived; last push 2026-09-16 |
| `unclaimed` | **pass** | assignees: none, linked PRs: none, comments: 0 |
| `bounded-work` | **pass** | Single function `_is_supported()` in `faithfulness_checker.py`; one algorithm change |
| `maintainer-vetted` | **pass** | Opener is COLLABORATOR; labels: bug, rag, tier-1 |
| `settled-design` | **pass** | 0 comments; no deferred prerequisite or open design question in body |
| `ai-policy-permits` | **pass** | Silence passes |
| `maintainer-responsive` *(preferred)* | **fail** | 0 of 5 sampled issues had a maintainer reply |
| `small-diff-signal` *(preferred)* | **pass** | Function and file named in body; "tier-1" label |
| `reproducible` *(preferred)* | **pass** | `pytest tests/unit/test_faithfulness_checker.py -q` with named assertion failure |

**Verdict: ACCEPT**

---

### Issue #72 — "`verify_password` raises `UnknownHashError` on malformed stored hashes instead of returning False"

| Check | Grade | Evidence |
|---|---|---|
| `repo-alive` | **pass** | Not archived; last push 2026-09-16 |
| `unclaimed` | **pass** | assignees: none, linked PRs: none, comments: 0 |
| `bounded-work` | **pass** | Single function in `core/security.py`; catch one exception, remove one xfail marker |
| `maintainer-vetted` | **pass** | Opener is COLLABORATOR; labels: bug, good first issue, api, tier-1 |
| `settled-design` | **pass** | 0 comments; fix approach stated explicitly (fail-closed on `UnknownHashError`) |
| `ai-policy-permits` | **pass** | Silence passes |
| `maintainer-responsive` *(preferred)* | **fail** | 0 of 5 sampled issues had a maintainer reply |
| `small-diff-signal` *(preferred)* | **pass** | "good first issue" + "tier-1"; both files named; "1–2 hours" effort stated |
| `reproducible` *(preferred)* | **pass** | xfail marker references manifest id H-05; test is named; observed vs expected stated |

**Verdict: ACCEPT**

---

## Ranking (all three accepted — fit order)

1. **#72** ← top pick. The acceptance condition is the most unambiguous of the three: the xfail marker comes off when the test passes. The fix is `try/except UnknownHashError: return False` — almost certainly 2–4 lines. Both relevant files are named. Effort estimated at 1–2 hours by the COLLABORATOR who wrote it. This is exactly the shape of Arul's four merged PRs: narrow deterministic bug, named test tells you when you're done, diff in the single digits.

2. **#54**. Python ingestion subsystem (resume parser), runnable snippet, three named failing tests. The regex anchor fix is deterministic. Slightly larger footprint than #72 because three tests fail (more surface to understand) and the fix may touch multiple regex patterns rather than one code path, but still firmly tier-1.

3. **#59**. The bug is real and the test is named, but the fix approach is less settled than the other two: correcting word-overlap similarity could mean anything from case-folding to introducing an embedding call. Without a maintainer note on how far to go, the acceptance condition is a judgment call — the thing that costs Arul the most time by his own account.

---

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "isArchived: false; last push 2026-09-16, 5 days before today 2026-09-21"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none, closedByPullRequestsReferences: [], comments: 0"},
      {"name": "bounded-work", "grade": "pass", "evidence": "Single function verify_password in core/security.py; catch UnknownHashError, remove one xfail marker"},
      {"name": "maintainer-vetted", "grade": "pass", "evidence": "Opener Aburke225 is COLLABORATOR; labels: bug, good first issue, api, tier-1"},
      {"name": "settled-design", "grade": "pass", "evidence": "0 comments; fix direction stated explicitly in body (fail-closed on UnknownHashError)"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "No CONTRIBUTING.md or AI policy file in repo root or .github/; silence passes"},
      {"name": "maintainer-responsive", "grade": "fail", "evidence": "0 of 5 recently-updated issues had any maintainer-association comment (preferred only)"},
      {"name": "small-diff-signal", "grade": "pass", "evidence": "'good first issue' + 'tier-1' labels; both core/security.py and tests/unit/test_security.py named; '1–2 hours' effort"},
      {"name": "reproducible", "grade": "pass", "evidence": "xfail marker references manifest id H-05; test name given; observed raise vs expected False stated"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "isArchived: false; last push 2026-09-16, 5 days before today 2026-09-21"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none, closedByPullRequestsReferences: [], comments: 0"},
      {"name": "bounded-work", "grade": "pass", "evidence": "Single function _detect_sections() in resume_parser.py; fix regex anchors for leading whitespace"},
      {"name": "maintainer-vetted", "grade": "pass", "evidence": "Opener Aburke225 is COLLABORATOR; labels: bug, good first issue, ingestion, tier-1"},
      {"name": "settled-design", "grade": "pass", "evidence": "0 comments; problem and expected outcome stated unambiguously in body"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "No CONTRIBUTING.md or AI policy file in repo root or .github/; silence passes"},
      {"name": "maintainer-responsive", "grade": "fail", "evidence": "0 of 5 recently-updated issues had any maintainer-association comment (preferred only)"},
      {"name": "small-diff-signal", "grade": "pass", "evidence": "'good first issue' + 'tier-1' labels; function and file named in body"},
      {"name": "reproducible", "grade": "pass", "evidence": "Runnable Python snippet; observed [] vs expected [Education, Skills]; 3 named failing tests in tests/unit/test_resume_parser.py"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/59",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "isArchived: false; last push 2026-09-16, 5 days before today 2026-09-21"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none, closedByPullRequestsReferences: [], comments: 0"},
      {"name": "bounded-work", "grade": "pass", "evidence": "Single function _is_supported() in faithfulness_checker.py; one algorithm change"},
      {"name": "maintainer-vetted", "grade": "pass", "evidence": "Opener Aburke225 is COLLABORATOR; labels: bug, rag, tier-1"},
      {"name": "settled-design", "grade": "pass", "evidence": "0 comments; no deferred prerequisite stated in body"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "No CONTRIBUTING.md or AI policy file in repo root or .github/; silence passes"},
      {"name": "maintainer-responsive", "grade": "fail", "evidence": "0 of 5 recently-updated issues had any maintainer-association comment (preferred only)"},
      {"name": "small-diff-signal", "grade": "pass", "evidence": "Function _is_supported() and file faithfulness_checker.py named; 'tier-1' label"},
      {"name": "reproducible", "grade": "pass", "evidence": "pytest command given; named test test_multiple_context_chunks; observed assert 0.0 > 0.5 failure stated"}
    ],
    "verdict": "accept"
  }
]
```

---

## Eval iterations

**Run history**

Four runs, in order. Only the two full runs carry a bar verdict; the partial runs were the
cheap revise loop.

1. **Partial** — `--only issue-09,issue-12,issue-20`: `agreement: 3/3 scored items`. A smoke test
   on the three bundles I judged riskiest before spending a full run: issue-12 (the only `policy`
   item, so the category floor depends on it), issue-20 (the `scope` reject with no labels and no
   linked PRs, so no other check would catch it), and issue-09 (a gold accept opened in 2018 with
   an abandoned closed PR, the item most likely to be over-rejected). All three matched.
2. **Full** — `agreement: 18/20 scored items (bar: 18/20: PASS)`,
   `categories: claimed 4/4  clear-accept 6/8  dead-repo 3/3  policy 1/1  scope 4/4`.
   Both misses were `bounded-work` failing gold accepts: issue-01 ("Body enumerates four distinct
   deliverables") and issue-19 ("Body enumerates 5 separate deliverables: 2 causes to fix + 3
   additional suggestions").
3. **Partial** — `--only issue-01,issue-19,issue-05,issue-10`: `agreement: 4/4 scored items`.
   The two issues I had just changed the check for, plus issue-05 and issue-10 as canaries — the
   two real umbrella issues that `bounded-work` still had to catch after being loosened.
4. **Full (committed)** — `agreement: 19/20 scored items (bar: 18/20: PASS)`,
   `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 3/4`.
   This is the run in `eval-run.txt`.

**Issue analysis**

`issue-15` (`zulip/zulip#19589`). My rubric graded it **accept**; the gold label is **reject**,
category `scope`, with the note: "years of design debate and two abandoned PRs behind a friendly
label".

Two clauses of mine produced the accept, and both did what I wrote them to do.

My `unclaimed` check says "A linked PR in state `closed` or `merged` is an abandoned or already-
landed attempt, not a claim, and does not fail this check." The bundle's repo facts read
`assignees: none; linked PRs: zulip/zulip#20840 (closed); zulip/zulip#23123 (closed)`. Both are
closed, so the check passed. Those are precisely the "two abandoned PRs" the gold note treats as
the reason to reject.

My `settled-design` check fails a thread that "runs past 20 comments without a maintainer settling
the approach". The thread has 97 comments, but the model found a maintainer had settled it —
"eeshangarg (MEMBER) confirmed approach by comment ~10 after sample payload provided; 80+ later
comments are bot claim/unclaim cycles, not design debate" — so the check passed on its stated
condition. The gold label reads the same 97-comment thread as "years of design debate."

So this is not a threshold that landed in the wrong place; it is a disagreement about what a long
thread on a five-year-old issue means. Gold treats a queue of people who started and quit as
evidence the work is harder than it looks. My rubric treats only *current* occupancy as
disqualifying, on purpose — see Trade-offs.

**Check rationale**

Quoting `bounded-work` from `tools/issue-select/rubric.md` exactly as uploaded:

> The issue asks for work that could land as **one pull request**. It fails on exactly two shapes:
> (a) the body lists **references to other GitHub issues** (`#1234`, `#5678`) as the work to be
> done, or the title or body calls it a megaissue / umbrella / tracking / epic issue; (b) the ask
> applies to a **whole class of code** rather than named sites ("add type annotations to the
> library", "write the missing pages"). Nothing else fails it. In particular, an issue that touches
> **several files**, fixes **several root causes of one symptom**, or carries a **list of
> suggestions or acceptance criteria** inside a single task is bounded and **passes** — that is one
> pull request, not many. A terse body, a bare checklist, or a bug report with no reproduction
> steps also passes: grade the size of the work asked for, not the polish of the writeup.

It did not start this specific. My first version said it fails when the body "enumerates sub-issue
references as a list of work to be split up." Run 2 showed that wording was read far more broadly
than I meant it: issue-01 failed with "Body enumerates four distinct deliverables: new task page +
update manage-pkgs.rst + update pip-interoperability.rst + update new-features.md", and issue-19
failed with "Body enumerates 5 separate deliverables: 2 causes to fix + 3 additional suggestions."
Both are gold accepts. Both are one pull request that happens to touch several files.

The fix was to name the two failing shapes exhaustively ("fails on exactly two shapes", "Nothing
else fails it") and then to say out loud which superficially-similar shapes still pass. A check
that only lists what fails leaves the boundary to the grader's judgement, and the grader drew it
somewhere I did not intend.

**Trade-offs**

`bounded-work` now gives up the ability to reject a genuinely oversized issue that is written as a
single coherent request — one that names no sibling issues and makes no class-wide claim, but
would still take three weeks. Nothing in the check's two shapes catches that; I am relying on
`settled-design` to pick up the ones where a maintainer says the fix reaches core internals, and
accepting that a quietly huge, well-written, unremarked issue gets through.

I checked the loosening did not go too far rather than assuming it. Run 3 re-ran `--only
issue-01,issue-19,issue-05,issue-10`: the two gold accepts flipped to accept as intended, and the
two real umbrella issues still failed — issue-10 is literally titled "Documentation request
megaissue" and lists 37 issue references, issue-05 asks for type annotations across the library.
Both are shape (a) and shape (b) respectively, so both stayed caught. Full-run agreement went from
18/20 to 19/20 with no category regressions.

The `unclaimed` clause discussed above is the other live trade-off, and it is the one I would
defend hardest. Treating closed PRs as abandoned attempts rather than claims is what earns
`issue-09` — a 2018 conda issue with a closed linked PR that gold accepts — and it is what loses
`issue-15`. I took that deal deliberately: a closed PR is evidence that the issue is *free*, and
the alternative rule would have me reject healthy issues in active repos because someone once
tried and stopped. Adding issue age or abandoned-attempt count as a required check would recover
issue-15 and put issue-09 at risk, trading a clear-accept for an arguable scope call.

---

## Selection rationale

**Selection rationale**

**1. Fit to my interests and to the time available.** My four merged PRs are all Swift — three to
`mozilla-mobile/firefox-ios`, one to `skiptools/skip-ui` — and every one of them was a narrow
deterministic bug with a final diff between two and five lines, where the work sat in the diagnosis
rather than the edit. Issue #54 is that shape in a language I want mileage in: `_detect_sections()`
in `resume_parser.py` anchors its section-header patterns at the start of a line, so PDF-extracted
text with leading indentation produces no sections at all. The issue ships a runnable snippet with
observed `[]` against expected `Education, Skills`, and names three currently-failing tests in
`tests/unit/test_resume_parser.py`. I can reproduce it in one command and I will know when I am
done. Python is where I have the most coursework and the least open-source mileage, and ingestion
is close enough to the text-parsing work I have already done that the unfamiliarity is the
language's conventions, not the problem domain.

**2. What the verdict identified correctly, and what I weighed that the rubric could not.** The
rubric got the mechanical facts right: repo alive (last push five days ago), nobody on it (no
assignee, no linked PRs, no comments), bounded to one function, opened by a `COLLABORATOR` with
four labels, no AI-contribution ban. Three things I weighed that it could not:

- *The skill ranked #72 above #54 and I overrode it.* Its reasoning was sound — #72 is a
  `try/except` around one passlib call, probably four lines, with the single cleanest acceptance
  condition of the three. But the fit profile the ranking reads has no notion that this issue also
  has to carry Units 2, 3 and 4. A four-line fix leaves very little to write a reproduction, a
  plan, and a PR narrative about. #54's three failing tests and multiple regex patterns give me
  more to actually analyse without making the acceptance condition any less certain.
- *A `preferred` check failed on all three candidates and I ignored it, correctly.*
  `maintainer-responsive` graded `fail` across the board: nobody has replied to any recent issue in
  this repo. In a normal repo that would worry me. This is a course sandbox seeded three weeks ago
  where the only `COLLABORATOR` is staff, so response latency measures the course calendar, not
  project health — which is exactly why that check is `preferred` and cannot sink a verdict.
- *The skill's evidence for `ai-policy-permits` was slightly wrong, though its grade was right.* It
  reported "No `CONTRIBUTING.md` anywhere in root or `.github/`" and passed the check on silence.
  There is a contribution guide, at `docs/CONTRIBUTING.md`; the skill did not look one directory
  down. It says nothing about AI use, so silence still passes and the verdict stands, but the
  evidence line is wrong and I would rather record that than let it sit. The evidence guide does
  warn that "the policy often hides one click away from the repo."

**3. Anticipated difficulty in claiming it.** Low. The issue is unassigned with zero comments, and
I re-verified that immediately before writing this with `gh issue view 54 --json
state,assignees,comments` rather than trusting a search listing — a habit I picked up last term
after claiming a `woocommerce-ios` issue that had closed two and a half hours earlier, because
`gh issue list --search` reads a lagging index. Beyond that, `scope.md`'s Path Review house rule
removes the usual risk: classmates' claim comments "do not block an issue," and credit attaches to
the pull request I open rather than to whether it merges. Five of the 73 open issues already carry
claim comments and #54 is not one of them, so even the collision I am not required to avoid has not
happened yet. The real difficulty is downstream, not in claiming: the covering tests are marked
`@pytest.mark.xfail(strict=True)`, so CI turns red with `XPASS` the moment my fix works, and
deleting those markers is part of the fix rather than an afterthought.

---

Related paths: `eval-run.txt` in this directory; your skill's files in `tools/issue-select/`.
