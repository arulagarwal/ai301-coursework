# Plan: #54, resume headers with leading whitespace

Built from my reproduction on this issue
([#issuecomment-5847372669](https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54#issuecomment-5847372669)):
PathReview at `2f4e82f`, Python 3.13.9 in `.venv`, macOS 26.6.2 (arm64).

## Diagnosis

What my repro showed:

- The issue's indented snippet, run unchanged, prints `[]`. The same text with the four-space
  indentation removed prints `['Education', 'Skills']`. Only the indentation differs, so leading
  whitespace is the trigger.
- With the xfail markers lifted, all five tests that cite #54 fail:

```
tests/unit/test_resume_parser.py:35: assert (False or False)
tests/unit/test_resume_parser.py:61: assert False
tests/unit/test_resume_parser.py:114: AssertionError: assert ('#' not in '# Jane Doe\..., PostgreSQL'
tests/unit/test_resume_parser.py:152: assert 0 > 0
tests/unit/test_resume_parser.py:175: AssertionError: assert not True
5 failed, 5 deselected in 0.13s
```

  Lines 35, 61, and 152 fail on detected sections. Lines 114 and 175 fail because an indented `#`
  header is still in the text.

The cause is the same in two functions of `ingestion/parsers/resume_parser.py`: their patterns
only match at the very start of a line.

- `_detect_sections()` looks for each section name right after `^` or `\n` (lines 134-137):

  ```python
  rf"^{re.escape(section)}\s*$",
  rf"^{re.escape(section)}\s*[:|-]",
  rf"\n{re.escape(section)}\s*$",
  rf"\n{re.escape(section)}\s*[:|-]",
  ```

  In `    Education:` there are spaces between the line start and the name, so none of the four
  match.
- `_strip_markdown()` removes headers with `re.sub(r"^#+\s+", "", content, flags=re.MULTILINE)`
  (line 101), which also needs the `#` at the very start of a line. On the markdown path it runs
  before `_detect_sections()` (lines 82-84).

In my repro I said the `_strip_markdown()` part was not confirmed yet. I called both functions
directly with the same input, indented and flush:

```
$ .venv/bin/python - <<'EOF'
from ingestion.parsers.resume_parser import ResumeParser
p = ResumeParser()
print("strip, indented:  ", repr(p._strip_markdown("    # Header\n    Body")))
print("strip, flush:     ", repr(p._strip_markdown("# Header\nBody")))
print("detect, indented: ", p._detect_sections("    Education:\n    Skills: Python\n"))
print("detect, flush:    ", sorted(p._detect_sections("Education:\nSkills: Python\n")))
EOF
strip, indented:   '# Header\n    Body'
strip, flush:      'Header\nBody'
detect, indented:  []
detect, flush:     ['Education', 'Skills']
```

Each function fails only when the line is indented.

Why five tests and not the three the issue names: `test_parse_markdown_resume` and
`test_strip_markdown_syntax` carry the same `xfail(strict=True)` marker citing #54, and they fail
in `_strip_markdown()`. `docs/CONTRIBUTING.md` says a fix should "drop the marker from every test
that covers it", because a strict marker turns a test that starts passing into an
`XPASS(strict)` failure.

## Scope

In scope:

- `_detect_sections()` and line 101 of `_strip_markdown()` in `ingestion/parsers/resume_parser.py`.
- The five `@pytest.mark.xfail(strict=True, reason="issue #54: ...")` markers in
  `tests/unit/test_resume_parser.py`. Nothing else in the tests changes.

Not in scope:

- `tests/conftest.py`. Its indented `sample_resume_text` fixture is the bug's input; flattening
  it would hide the bug instead of fixing it.
- `ingestion/parsers/readme_parser.py` and `ingestion/chunking/structural_chunker.py`, which match
  `#` headers the same anchored way. They handle READMEs, and #70 and #71 already track their
  indented fixtures as the problem there.
- The four patterns themselves, `SECTION_HEADERS`, the other regexes in `_strip_markdown()`, and
  `scripts/issues_manifest.json` (its entry for this issue lists three tests, not five).

## Files

- `ingestion/parsers/resume_parser.py`
- `tests/unit/test_resume_parser.py`

## Approach

1. In `_detect_sections()`, match against a lowercased copy of the text with each line stripped:
   `text_lower = text.lower()` becomes
   `text_lower = "\n".join(line.strip() for line in text.lower().splitlines())`.
   The four patterns stay as they are. The copy is only used for matching, so the text the parser
   returns does not change. This is also what the example commit for #54 in
   `docs/CONTRIBUTING.md` describes ("Strip the line before matching headings"), and `str.strip()`
   covers tabs as well as spaces. Then remove the markers on the three tests that go through it:
   `test_parse_single_column_resume_text`, `test_parse_resume_no_work_experience`, and
   `test_detect_sections`.
2. In `_strip_markdown()`, change line 101 to
   `re.sub(r"^[ \t]*#+\s+", "", content, flags=re.MULTILINE)`. I'm using `[ \t]*` and not `\s*`,
   because under `re.MULTILINE` `\s*` can run across newlines and delete the blank line before a
   header. Then remove the markers on `test_parse_markdown_resume` and
   `test_strip_markdown_syntax`.

Each step removes exactly the markers its change makes pass, so the test file stays green after
each one.

## Test plan

My repro steps, re-run on the branch, with what I expect after the fix:

1. The issue's snippet, unchanged. Before: `[]`. After: a list with `Education` and `Skills`
   (order can vary, since the function returns `list(set(...))`).
2. The control without indentation. Before and after: `['Education', 'Skills']`.
3. `.venv/bin/pytest tests/unit/test_resume_parser.py -q -rxX -p no:cacheprovider`.
   Before: `5 passed, 5 xfailed`. After: `10 passed`, nothing xfailed.
4. The five #54 tests by name
   (`-k "single_column_resume_text or no_work_experience or detect_sections or parse_markdown_resume or strip_markdown_syntax"`).
   Before, with `--runxfail`: `5 failed, 5 deselected`. After, without it: `5 passed, 5 deselected`.
5. `.venv/bin/pytest tests/unit -q`: five fewer xfails than on `main`, and no new failures.
6. `ruff check .`, `black --check .`, and `mypy api/ core/ ingestion/ rag/ agent/ safety/`, as CI
   runs them: no new findings compared with `main`. On `main` today these give
   `All checks passed!`, `110 files would be left unchanged.`, and (with `--python-version 3.13`,
   see Risks) `Success: no issues found in 76 source files`.

## Risks and unknowns

- Stripping lines before matching means an indented body line that starts with a section name
  followed by `:`, `|`, or `-` now counts as a header (for example `    Skills - Python`). Before
  opening the PR I'll check that indented prose such as `    Experience with Python` and
  `    - Experience: 5 years` is still not detected. No existing test covers this.
- The two functions get different fixes: detection matches a stripped copy, while
  `_strip_markdown()` changes the text it returns. That fits what each function does, but a
  reviewer may prefer one technique for both.
- CI runs Python 3.11 and my venv is 3.13. The change uses nothing version-specific, so I don't
  expect a difference. One local catch: `pyproject.toml` pins mypy to 3.11, and the numpy stubs
  in my 3.13 venv use 3.12+ syntax, so plain `mypy` stops on numpy before checking anything. I
  run it with `--python-version 3.13` locally; CI's job still runs it on 3.11.

## Deviations

The plan held. The build is the change described above, on branch
`fix/54-indented-resume-headers` (commit `965a1f0`): the same two files, the same one-line
change in `_detect_sections()`, the same pattern on line 101, and the same five markers removed.
Each step left the test file green, as planned: after step 1 it was `8 passed, 2 xfailed`
(only the two `_strip_markdown()` tests still marked), after step 2 `10 passed`.

One small addition the plan did not spell out: a one-line comment above the new `text_lower`
line in `_detect_sections()` ("Strip each line so indented headers still match the patterns
below"), because the joined, stripped copy is not obvious at a glance. It changes no behavior.

The test plan came out as expected: the issue's snippet now prints `['Education', 'Skills']`,
the control is unchanged, the unit suite went from `375 passed, 53 xfailed` to
`380 passed, 48 xfailed`, and ruff, black, and mypy match `main`. The over-match check I promised
in the posted plan also held: `    Experience with Python and Go`, `    - Experience: 5 years`, and
`    I list my skills: Python` are still not detected, `C#` and `#1` survive stripping, and the
blank line before an indented `## Experience` is kept. The posted plan is still accurate, so I
am not adding a follow-up comment on the issue.
