# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/103

**Branch**

`fix/54-indented-resume-headers`

## Eval iterations

**Run history**

1. Calibration smoke run (`--include-calibration --only calib-01,calib-02,calib-03,calib-04`, unscored): 4/4 calibration packages agreed.
2. Full run 1: **16/20**, below the bar. Categories: clear-accept 3/7, not-tested 4/4, silent-drift 4/4, standards-wall 2/2, unreviewable 3/3. All four misses were clear-accept packages (pkg-05, pkg-08, pkg-11, pkg-13) rejected on `fix-shown`, two also on `checks-run`.
3. Partial re-grade after loosening `fix-shown` and `checks-run` (`--only` the four misses, the four not-tested packages as canaries, and calib-03 and calib-04 as traps): 8/8 scored agreed, both calibration packages still rejected.
4. Full run 2: **19/20** (bar: 18/20: PASS). Categories: clear-accept 7/7, not-tested 4/4, silent-drift 4/4, standards-wall 1/2, unreviewable 3/3. This is the run in `eval-run.txt`.

**Package analysis**

pkg-20 (standards-wall), ghostty-org/ghostty. Gold label: reject. My rubric's verdict in the saved run: accept.

The repo facts say Ghostty's AI_POLICY.md requires that "All AI usage in any form must be disclosed, stating the tool used and the extent of the assistance". The candidate PR's description has no AI-use statement at all. Everything else about it is clean: one file, the mechanism the plan names, and a before/after of `?997;2n` to `?997;1n`. My `policy-and-direction-honored` check passed it with the evidence "no evidence of AI use to disclose; maintainer jcollie's diagnosis/patch shape is followed exactly". My rubric reads a disclosure rule as conditional: it asks for a disclosure only when the package shows that AI was used. Ghostty's rule is stricter than that, because a reviewer can't tell from a PR whether AI was used, which is why the policy wants the statement up front. A PR to a repo with a mandatory disclosure policy and no statement either way should fail.

Run 1 also rejected pkg-20, but for the wrong reason. It failed on `fix-shown` and `checks-run` (the old "pasted output only" condition), while `policy-and-direction-honored` passed with the same "no evidence of AI use" reasoning. So run 1's match was luck, not a check that sees the problem. The standards-wall floor still holds through pkg-01.

**Check rationale**

From `tools/pr-precheck/rubric.md`, the `fix-shown` pass condition as it reads now:

> The evidence **re-runs the reproduced trigger on the branch and reports a specific observed result** that is the plan's expected-after: a value, message, row, exit code, or visible state someone could check against the plan. Printed output, a transcribed session, or a note after the command all count when they name what was observed. The before may come from the plan's repro evidence. Every distinct failure mode the test plan names is re-run, or the PR says which one was not and why. Fails when the after names nothing observed (only "works now", "fixed", or "matches the expected output" with no observed content), when no evidence is given, when the evidence never exercises the trigger, or when a named failure mode is silently skipped.

My first version came straight from the activity's staff answer on calib-03, where notes typed after `#` were "not output". So it required a command followed by printed output, and it called a `#` note a claim. Run 1 showed that line was in the wrong place. pkg-11's evidence is `$ rg hi  # after: searches normally, no error`, the same shape as calib-03, and its gold label is accept. pkg-13's after is a transcribed `{ code = 0, stderr = "" }` and the full URL in the browser. What separates them from calib-03 is not formatting. Their notes name what was observed, while calib-03's says only that it "matches the issue's expected output byte for byte", which names nothing. So the check now asks for a specific observed result someone could check against the plan, in any form. I kept the "every distinct failure mode" clause, because that is what still catches calib-04, which re-runs only the first of the two crashes its plan names.

**Trade-offs**

Loosening `fix-shown` and `checks-run` flipped pkg-20 from agree to disagree. Run 1 rejected it only through those two checks, and with them loosened nothing else in my rubric catches it (see Package analysis). I ran the four not-tested packages as canaries in the `--only` re-grade, because that is the category a looser evidence check could leak. All four still rejected, and run 2 kept not-tested at 4/4. I did not include a standards-wall canary, which is how this flip reached the full run instead of a $0.25 partial. The case I now accept missing is a PR that never mentions AI, sent to a repo whose policy requires a disclosure statement either way. The fix would be a rule in `policy-and-direction-honored` that a mandatory disclosure policy fails a PR with no statement. I left it for a later revision rather than re-tuning after a passing run.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
