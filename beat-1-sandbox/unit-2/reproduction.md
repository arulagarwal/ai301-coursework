# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

arulagarwal

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54#issuecomment-5847282026

I'm picking this one up. Per the issue, its indented sample makes `_detect_sections()` in `ingestion/parsers/resume_parser.py` return `[]` instead of `Education, Skills`.

My plan: on a clean setup of my fork (following `docs/SETUP.md`), run the issue's snippet unchanged, then run `tests/unit/test_resume_parser.py` with `--runxfail` so the three tests the issue names show their real failures. Two more tests in that file, `test_parse_markdown_resume` and `test_strip_markdown_syntax`, also carry xfail markers citing #54, so I'll include them and report what they do.

I'll post the environment, the exact steps, and the output here as a separate comment. I see a reproduction is already posted above; mine will come from my own setup.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54#issuecomment-5847372669

Reproduced on a fresh clone of my fork, running the issue's snippet unchanged.

**Environment**

- PathReview at `2f4e82f` (2026-09-16)
- macOS 26.6.2 (arm64), Python 3.13.9 in `.venv`
- Set up per `docs/SETUP.md`: `cp .env.example .env`, `docker compose up -d` (db and redis healthy), then `make setup`. One deviation: my system `python3` is 3.10.11, below the `>=3.11` that `pyproject.toml` requires, and there is no `python` on my PATH, so `make setup` would build the venv with 3.10. I ran `make setup` with a `python` symlink to Homebrew's `python3.13` first on my PATH. For example:

```
mkdir -p /tmp/py313 && ln -sf "$(command -v python3.13)" /tmp/py313/python
PATH="/tmp/py313:$PATH" make setup
```

**The issue's snippet**

From the repo root:

```
$ .venv/bin/python - <<'EOF'
from ingestion.parsers.resume_parser import ResumeParser
r = ResumeParser()
res = r.parse('\n    John Smith\n    john@example.com\n\n    Education:\n    - B.S. Computer Science\n\n    Skills: Python\n')
print(res.metadata['detected_sections'])
# observed: []  (expected: Education, Skills)
EOF
[]
```

Expected, per the issue: Education and Skills. As a control, the same text with the four-space indentation removed does detect both:

```
$ .venv/bin/python - <<'EOF'
from ingestion.parsers.resume_parser import ResumeParser
res = ResumeParser().parse('\nJohn Smith\njohn@example.com\n\nEducation:\n- B.S. Computer Science\n\nSkills: Python\n')
print(sorted(res.metadata['detected_sections']))
EOF
['Education', 'Skills']
```

**The tests**

As shipped, five tests in `tests/unit/test_resume_parser.py` are xfail with the #54 reason (trimmed to the summary):

```
$ .venv/bin/pytest tests/unit/test_resume_parser.py -q -rxX -p no:cacheprovider
XFAIL ...::test_parse_single_column_resume_text - issue #54: resume section detection fails on leading whitespace
XFAIL ...::test_parse_resume_no_work_experience - issue #54: resume section detection fails on leading whitespace
XFAIL ...::test_parse_markdown_resume - issue #54: resume section detection fails on leading whitespace
XFAIL ...::test_detect_sections - issue #54: resume section detection fails on leading whitespace
XFAIL ...::test_strip_markdown_syntax - issue #54: resume section detection fails on leading whitespace
5 passed, 5 xfailed in 0.71s
```

With the markers lifted (trimmed to the one-line failures):

```
$ .venv/bin/pytest tests/unit/test_resume_parser.py --runxfail -q --tb=line -p no:cacheprovider -k "single_column_resume_text or no_work_experience or detect_sections or parse_markdown_resume or strip_markdown_syntax"
tests/unit/test_resume_parser.py:35: assert (False or False)
tests/unit/test_resume_parser.py:61: assert False
tests/unit/test_resume_parser.py:114: AssertionError: assert ('#' not in '# Jane Doe\..., PostgreSQL'
tests/unit/test_resume_parser.py:152: assert 0 > 0
tests/unit/test_resume_parser.py:175: AssertionError: assert not True
5 failed, 5 deselected in 0.13s
```

The three tests the issue names fail on detected sections: line 35 finds neither Experience nor Skills, line 61 finds no Education, and line 152 is `len([])` from `_detect_sections()` itself.

The other two fail on something the issue doesn't mention. Lines 114 (`test_parse_markdown_resume`) and 175 (`test_strip_markdown_syntax`) assert that `#` headers are stripped, and the indented `# Jane Doe` and `# Header` lines are still there. Both read text from `_strip_markdown()` (the second test calls it directly), whose header pattern is also anchored at the start of a line: `re.sub(r"^#+\s+", "", content, flags=re.MULTILINE)`. So it looks like the same leading-whitespace problem in a second function, but I haven't confirmed that yet. I'll look at both functions before proposing anything.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Four runs, in order. Only the two full runs carry a bar verdict; the two partial runs were the
cheap part of the loop.

1. **Partial**, `--include-calibration --only calib-01,calib-04`: `agreement: 0/0 scored items`,
   since calibration packages are never scored. A smoke test before spending a full run, on the
   two calibration packages most likely to expose a badly placed line: calib-01 (terse but
   complete, gold accept) and calib-04 (exact command and panic shown, but no environment record,
   gold reject). Both matched: calib-01 accept, calib-04 reject on `env-recorded` alone.
2. **Full**: `agreement: 16/20 scored items  (bar: 18/20: below the bar)`,
   `categories: clear-accept 5/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 3/4`.
   Four misses: pkg-01 and pkg-05 (gold accepts, failed `faithful-to-issue`), pkg-09 (gold accept,
   failed `claims-backed`), and pkg-16 (gold reject, graded accept with no check failing).
3. **Partial**, `--include-calibration --only pkg-01,pkg-05,pkg-09,pkg-16,pkg-11,pkg-20,calib-03`:
   `agreement: 6/6 scored items`, with calib-03 still reject. The four misses after the revision,
   plus three canaries (why each one is in Trade-offs).
4. **Full (committed)**: `agreement: 19/20 scored items  (bar: 18/20: PASS)`,
   `categories: clear-accept 7/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`.
   This is the run in `eval-run.txt`.

**Package analysis**

`pkg-03` (`BurntSushi/ripgrep#2779`). My rubric graded it **reject**; the gold label is
**accept**, category `clear-accept`, with the note: "exact-steps repro on current version with a
minus-replace control matching the owner's trigger note; version delta acknowledged; human-voiced
comment satisfies the repo's AI-comment rule".

Six of my seven checks passed it, and for the reasons gold gives: the version delta is stated
("The issue was filed against 13.0.0; behavior is unchanged on 15.2.0"), the command is shown, and
the output `1:fnord 2:boccob 3:d321fdddffff 4:clowns` is the issue's wrong line numbers. The one
failure was `claims-backed`, with the evidence line: "\"Dropping -r '$1' ... reports 1, 4, 7, 10
correctly\" is asserted as fact with no output artifact shown for that run."

That is an accurate reading of the report. The main run is a transcript, but the control run is
one sentence of prose: "Dropping `-r '$1'` from the same command reports 1, 4, 7, 10 correctly".
My check requires each statement about what happened to be "shown by an artifact in the report"
or "marked as a guess", and this one is neither. Gold reads the same sentence as a control that
supports the report, and on this package I think gold is right: the claim is small, specific, and
anyone can check it by deleting two tokens from a command that is shown. My rubric has no notion of
how cheap a claim is to verify; it only asks whether the output is on the page.

One more thing I noticed: pkg-03 agreed in the first full run. The requirement it failed on,
"shown by an artifact in the report", was in both versions of `claims-backed`, but I did edit the
check between runs, so I can't say whether the flip came from my edit or from the grader applying
the same words more strictly this time.

**Check rationale**

Quoting the pass condition of `faithful-to-issue` from `tools/repro-check/rubric.md` exactly as
uploaded:

> The report exercises **the issue's trigger**: the input, command, or condition the issue identifies as producing the failure, run on **the version the issue targets or a newer one**. Differences that do not touch the trigger pass without comment: a command alias, an output or offline flag, a different host or file name, or a **reduced input** that keeps the element the issue says triggers the failure (a minimal repro), as long as the report shows or concretely describes the input it used. Fails on an unnamed change to the trigger element itself (a swapped operator in the triggering input, a removed flag the failure depends on), on steps that stop before the trigger, on a run on an **older version** than the issue targets that the report does not name as a deviation, and on any other environment difference the issue ties to the failure that the report does not acknowledge. A different OS or platform alone does not fail unless the issue ties the failure to it. When the issue gives no exact input, a report that builds its own passes if it shows that input and it fits the issue's description.

It started as my activity check `input-faithful`, and the version I first ran said the report
must be "compared character by character" with the issue and that "A change is allowed only when
the report **names it and says why**". That was built for calib-03, where swapping `=` for `:`
turned the issue's panic into a parse error. The first full run showed it was wrong in both
directions.

Too strict: it failed two gold accepts over changes that could not affect the bug. pkg-01 failed
because "the `https`→`http` command swap is never named or justified", and pkg-05 because the
"report silently substitutes its own env.yml without naming or justifying the change", when that
env.yml was a minimal input that kept the `category:` section the issue is about.

Too loose: it passed pkg-16, a report on "pandas 1.5.3 (pip), Python 3.10.12" against an issue
where "The reporter confirmed the bug on the latest version and on the main branch". My `env-recorded` check
had also said "a different version or OS passes once it is named, because the reader can then
compare", so nothing caught it.

The fix was to make the trigger the unit of faithfulness instead of the transcript, and to give
version a direction. A newer version is still evidence about the bug, and so is another OS; five
of the clear accepts ran newer versions or other platforms and are correct. An older version is a
different program. In the committed run, pkg-16 fails with "Issue confirmed on latest release
v3.0.5/main; report ran pandas 1.5.3 without naming this as a deviation", and pkg-01 now passes
with "only differences are an offline flag and command alias, both permitted".

**Trade-offs**

`faithful-to-issue` now leaves it to the grader to decide which element of a reproduction is the
trigger. "Differences that do not touch the trigger pass without comment" is easy to apply when
the issue quotes its input, and harder when the issue never says what causes the failure. A silent
change that looks incidental but isn't could pass. I accept that in exchange for not failing
honest minimal repros.

I checked the loosening did not go too far rather than assuming it. Run 3 re-ran three canaries
alongside the four misses:

- `calib-03`, the operator-swap trap, still failed `faithful-to-issue` with "a swapped key/value
  separator, changing the triggering input itself".
- `pkg-11` is the accept most exposed to the new version rule: the same yq version as the issue,
  a different OS, and no sentence acknowledging it. It stayed accept.
- `pkg-20` is the only `disclosure` package. None of my edits touched `conventions-respected`, but
  a single-package category is where a loosening can cost the whole category floor, so I re-ran it.
  It stayed reject.

`claims-backed` gives up `pkg-03`, as described above. I am keeping it as it reads. The
`no-evidence` packages, 4 of 4 matched, are reports whose statements outrun their output, and a rule
that lets a described but unshown run pass would have to say how cheap to verify is cheap enough.
Graders would draw that line differently every time.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
