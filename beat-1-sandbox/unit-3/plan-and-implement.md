# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

arulagarwal

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54#issuecomment-5965178637

Plan for #54, built from my reproduction above (commit `2f4e82f`).

**Cause.** Two functions in `ingestion/parsers/resume_parser.py` only match at the very start of a line. `_detect_sections()` looks for each section name right after `^` or `\n`, so `    Education:` never matches and `detected_sections` comes back `[]`. `_strip_markdown()` removes headers with `^#+\s+`, so an indented `# Header` keeps its `#`. That second function is why `test_parse_markdown_resume` and `test_strip_markdown_syntax` fail too. My repro said I hadn't confirmed it yet, so I called both functions with the same input indented and flush:

```
strip, indented:   '# Header\n    Body'
strip, flush:      'Header\nBody'
detect, indented:  []
detect, flush:     ['Education', 'Skills']
```

**Change.** In `_detect_sections()`, match against a copy of the text with each line stripped, the approach the #54 example commit in `docs/CONTRIBUTING.md` describes. The text the parser returns stays the same. In `_strip_markdown()`, let the header pattern allow leading spaces or tabs: `^[ \t]*#+\s+`. Then remove the five `xfail(strict=True)` markers that cite #54. I'm not changing `tests/conftest.py`, the README parser, or the section names.

**Test.** Re-run my repro steps. The issue's snippet should list Education and Skills instead of `[]`, the unindented control should not change, and `tests/unit/test_resume_parser.py` should go from `5 passed, 5 xfailed` to `10 passed`. Then the full unit suite, plus ruff, black, and mypy as CI runs them.

**Unknown.** No test checks that indented prose is not read as a header. I'll try a few lines like `    Experience with Python` before opening the PR.

Other plans and a PR are already up on this issue; this one is built from my own reproduction.

---

## Your branch

**Branch**

fix/54-indented-resume-headers

**Evidence**

My Unit 2 repro steps, plus the test file, the unit suite, and the CI lint and type checks, run
by the same script before and after the change. Steps 1 and 2 run these two snippets, unchanged
from my Unit 2 repro comment:

```
$ .venv/bin/python - <<'EOF'
from ingestion.parsers.resume_parser import ResumeParser
r = ResumeParser()
res = r.parse('\n    John Smith\n    john@example.com\n\n    Education:\n    - B.S. Computer Science\n\n    Skills: Python\n')
print(res.metadata['detected_sections'])
# observed: []  (expected: Education, Skills)
EOF

$ .venv/bin/python - <<'EOF'
from ingestion.parsers.resume_parser import ResumeParser
res = ResumeParser().parse('\nJohn Smith\njohn@example.com\n\nEducation:\n- B.S. Computer Science\n\nSkills: Python\n')
print(sorted(res.metadata['detected_sections']))
EOF
```

Before, on `main` at `2f4e82f`:

```
$ git rev-parse --abbrev-ref HEAD; git log -1 --format="%h %cs %s"
main
2f4e82f 2026-09-16 chore: track five more manifest entries against the tracker
$ .venv/bin/python --version
Python 3.13.9

### 1. The issue's snippet, unchanged
$ .venv/bin/python - <<'EOF' ... EOF
[]

### 2. Control: the same text with the leading indentation removed
$ .venv/bin/python - <<'EOF' ... EOF
['Education', 'Skills']

### 3. The test file
$ .venv/bin/pytest tests/unit/test_resume_parser.py -q -rxX -p no:cacheprovider
xx.x..xx..                                                               [100%]
=========================== short test summary info ============================
XFAIL tests/unit/test_resume_parser.py::TestResumeParser::test_parse_single_column_resume_text - issue #54: resume section detection fails on leading whitespace
XFAIL tests/unit/test_resume_parser.py::TestResumeParser::test_parse_resume_no_work_experience - issue #54: resume section detection fails on leading whitespace
XFAIL tests/unit/test_resume_parser.py::TestResumeParser::test_parse_markdown_resume - issue #54: resume section detection fails on leading whitespace
XFAIL tests/unit/test_resume_parser.py::TestResumeParser::test_detect_sections - issue #54: resume section detection fails on leading whitespace
XFAIL tests/unit/test_resume_parser.py::TestResumeParser::test_strip_markdown_syntax - issue #54: resume section detection fails on leading whitespace
5 passed, 5 xfailed in 0.10s

### 4. The five #54 tests by name
$ .venv/bin/pytest tests/unit/test_resume_parser.py --runxfail -q --tb=line -p no:cacheprovider -k "single_column_resume_text or no_work_experience or detect_sections or parse_markdown_resume or strip_markdown_syntax"
/Users/arulagarwal/Documents/CodePath/AI301/Fall2026/pathreview-ai301-fa26-s3/tests/unit/test_resume_parser.py:175: AssertionError: assert not True
=========================== short test summary info ============================
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_single_column_resume_text
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_resume_no_work_experience
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_markdown_resume
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_detect_sections
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_strip_markdown_syntax
5 failed, 5 deselected in 0.08s

### 5. The unit suite
$ .venv/bin/pytest tests/unit -q -p no:cacheprovider
375 passed, 53 xfailed, 1 warning in 4.96s

### 6. CI lint and type checks
$ .venv/bin/ruff check .
All checks passed!
$ .venv/bin/black --check .
110 files would be left unchanged.
$ .venv/bin/mypy api/ core/ ingestion/ rag/ agent/ safety/ --ignore-missing-imports --python-version 3.13
Success: no issues found in 76 source files
```

After, on `fix/54-indented-resume-headers` at `965a1f0`:

```
$ git rev-parse --abbrev-ref HEAD; git log -1 --format="%h %cs %s"
fix/54-indented-resume-headers
965a1f0 2026-10-02 fix(ingestion): allow indented headers in resume parsing
$ .venv/bin/python --version
Python 3.13.9

### 1. The issue's snippet, unchanged
$ .venv/bin/python - <<'EOF' ... EOF
['Education', 'Skills']

### 2. Control: the same text with the leading indentation removed
$ .venv/bin/python - <<'EOF' ... EOF
['Education', 'Skills']

### 3. The test file
$ .venv/bin/pytest tests/unit/test_resume_parser.py -q -rxX -p no:cacheprovider
..........                                                               [100%]
10 passed in 0.08s

### 4. The five #54 tests by name
$ .venv/bin/pytest tests/unit/test_resume_parser.py -q --tb=line -p no:cacheprovider -k "single_column_resume_text or no_work_experience or detect_sections or parse_markdown_resume or strip_markdown_syntax"
.....                                                                    [100%]
5 passed, 5 deselected in 0.08s

### 5. The unit suite
$ .venv/bin/pytest tests/unit -q -p no:cacheprovider
380 passed, 48 xfailed, 1 warning in 4.98s

### 6. CI lint and type checks
$ .venv/bin/ruff check .
All checks passed!
$ .venv/bin/black --check .
110 files would be left unchanged.
$ .venv/bin/mypy api/ core/ ingestion/ rag/ agent/ safety/ --ignore-missing-imports --python-version 3.13
Success: no issues found in 76 source files

### 7. Over-match probe (not committed): indented prose must not count as a header
$ .venv/bin/python - <<'EOF' ... EOF
'    Experience with Python and Go' -> []
'    - Experience: 5 years' -> []
'    I list my skills: Python' -> []
'Learned C# and F#' -> 'Learned C# and F#'
'    Ranked #1 in the class' -> 'Ranked #1 in the class'
```

The mypy line runs with `--python-version 3.13` because `pyproject.toml` pins mypy to 3.11 and
the numpy stubs in my 3.13 venv use 3.12+ syntax, so plain `mypy` stops on numpy before checking
anything. CI runs the same command on Python 3.11. Section 7 of the after run is the over-match
check my plan comment promised; it is not committed.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Two runs, in order. Only the full run carries a bar verdict.

1. **Partial**, `--include-calibration --only calib-01,calib-02,calib-03,calib-04`:
   `agreement: 0/0 scored items`, since calibration packages are never scored. A smoke test on
   the four worksheet packages before spending a full run. All four matched: calib-01 accept
   (gold accept); calib-02 reject on `cause-fits-repro` and `stranger-can-start`; calib-03 reject
   on `cause-fits-repro`, `bounded-scope`, and `thread-direction-engaged`; calib-04 reject on
   `test-observes-fix` alone. Gold for the last three is reject.
2. **Full (committed)**: `agreement: 19/20 scored items  (bar: 18/20: PASS)`,
   `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.
   One miss, pkg-14 (gold accept, graded reject). This is the run in `eval-run.txt`. It already
   passed with every category matched, so I froze my files there and made no second full run.

**Package analysis**

`pkg-14` (`zellij-org/zellij#5174`, OSC color sequences leaking into the terminal on SSH
reattach). My rubric graded it **reject**; the gold label is **accept**, category
`clear-accept`, with the note: "honestly scoped-down: reattach handshake fix with a
regression-window repro; defers the untestable Windows variant and says so; arguable on the
deferral, ready as scoped".

Four of my six required checks passed it, including the deferral gold calls arguable:
`bounded-scope` read the Windows variant as "explicitly deferred, not folded in". It failed on
two.

`stranger-can-start` quoted the plan's Files line, "exact functions to be pinned in the PR after
tracing the query issuance with debug logs", and concluded "no file or function is actually named
yet." That is an accurate reading of my check, which asks the plan to name "where the change goes
(a file, a function, or a site the repro evidence or thread pins down)". The plan names two crates
and a code path, "the client attach/reattach path in `zellij-server` (session connection
handling)", and then says itself that the functions are not known yet.

`cause-fits-repro` failed on one supporting sentence, the plan's explanation of the cache control:
"the plan's bridge for this ('refetched along the fresh-attach path once') is asserted with no
quoted code or trace."

Where I land: on `stranger-can-start`, I think gold is right. "The reattach handshake" before
"pane input is wired" is a site a stranger could find, and the plan says how it will pin the
function, from `zellij --debug` output it already has. On `cause-fits-repro`, the cause itself is
backed by the package (fresh attach is clean, 0.44.1 is clean, every reattach from 0.44.2 on
leaks), and the cache sentence explains a control instead of contradicting it. My "grounded"
condition was written for the cause, and the grader applied it to a supporting sentence.

I did not loosen either check. Each one carries a whole category in this run:
`stranger-can-start` fails all three `unbuildable` packages and `cause-fits-repro` fails all four
`wrong-cause` packages. "Functions to be pinned later" is exactly the gap an unbuildable plan
would walk through.

**Check rationale**

Quoting `test-observes-fix` from `tools/plan-check/rubric.md` exactly as uploaded, its Evidence
and Pass condition cells (weight `required`):

> The plan's test plan (a `Test:` line or a test-plan section), read against the steps and the Expected and Actual lines in `## Repro evidence`. Live mode: the test plan in `plan.md` against the steps and output in the student's repro comment.

> The test plan **re-runs the reproduced trigger**, by hand or as an automated test, and names the result that shows the bug is gone: the repro's Expected at the step where Actual differed (a value, an output, an exit status, a timing, a visible change). A manual re-run counts as fully as an automated test, and so do the issue's own cases turned into regression tests. Extra cases are welcome but not required. Fails when the only test is running the existing suite or checking that nothing regresses; when an added test never exercises the trigger (a smoke test that only asserts setup, registration, or configuration); when no observable result is named ("it works"); and when there is no test plan.

It started as my group's third check in the activity worksheet: "test proves fix (required):
passes if the plan adds an automated test suite." I rejected that wording because the calibration
packages show it is wrong in both directions.

- Too strict: calib-01's whole test is a manual re-run, "repro steps above; at step 3 the color
  must flip without leaving the view", and calib-01 is the activity's correct "ready". An
  automated-test rule holds it.
- Too loose: calib-03 plans to "Add a pager-integration smoke test asserting the default binding
  set is registered." That is an automated test, so the worksheet rule passes it, but it never
  times shift+G on a large file, which is the bug. calib-04's "Run the full test suite
  (`cargo test --workspace`) and make sure nothing regresses" runs a whole automated suite and
  says nothing about the absolute-path glob.

So the check stopped asking whether a test is automated and started asking whether it re-runs the
trigger and names the Expected at the step where Actual went wrong. The smoke run shows both
directions fixed: calib-01 passes with "\"at step 3 the color must flip without leaving the view\"
re-runs the repro trigger and names the Expected result.", and calib-04 is held on this check
alone, with evidence starting "Test plan: \"Run the full test suite (cargo test --workspace) and
make sure nothing regresses\"". In the full run it also fails pkg-10 ("names no observable result
matching the repro's Expected (~350ms ballpark)") and pkg-17 ("names no repro step or observable
result").

**Trade-offs**

`test-observes-fix` gives up three things.

- It is lenient on short outcome lines. calib-02's "undo works after toggling" passed, and so did
  pkg-18's "golangci-lint should not panic on the reproduction anymore", because each names the
  opposite of the Actual at the trigger, with no steps written out. A plan whose only weakness is
  a thin test line would get through. I accept that: asking for written-out steps would make this
  a check on the write-up's shape, and calib-01, the activity's "ready", has a one-line test. Both
  were held anyway, calib-02 on `stranger-can-start` and `cause-fits-repro`, pkg-18 on four other
  required checks.
- It never asks for a negative test. A fix that over-matches passes as long as the re-run shows
  the bug gone. My own #54 plan is the example: this check passed it, and the over-match risk (an
  indented `    Skills - Python` now counts as a header) shows up only under `risks-named`, which
  is preferred.
- Nothing changed elsewhere, and here is how I know: I made no revision after the full run, so no
  package could flip, and in that run this check never decided a verdict alone. Each package it
  failed (pkg-04, pkg-10, pkg-17) also failed at least two other required checks
  (`results-run1.json`), so the score does not depend on where this line sits.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
