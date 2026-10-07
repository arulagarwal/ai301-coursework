# Voice guide: how I talk upstream

## Who I am in threads

I'm Arul, a graduate student in CodePath AI301. I have four merged upstream PRs, all in Swift
(firefox-ios and skip-ui); Path Review is my first Python repo. In a thread I report what I ran
and what I saw, and I say plainly when I have not confirmed something yet.

## Rules I write by

### Rule: Promise the investigation, not the fix

Before I have reproduced anything, the only thing I can promise is the next step I will take and
the report I will post. No fix, no date, no guarantee.

- Wrong: "I'll have a fix up for this by the weekend."
- Right: "I'll reproduce this on a fresh clone of my fork and post what I find here."

### Rule: Name the thing

Every comment names something only this issue has: the function, the input, the test, or the
observed value. If the sentence would fit under any other issue, it is not ready.

- Wrong: "I'd like to work on this issue."
- Right: "I'd like to take this one. The issue's indented sample makes `_detect_sections()` return `[]`, and I'll start by running that sample and the tests it names."

### Rule: Say what I ran, not what I believe

A claim about the code is either backed by output or a quoted line I can point to, or it is
labelled as a guess. I don't state my own understanding as evidence.

- Wrong: "I'm confident I understand the root cause."
- Right: "The header patterns in `_detect_sections()` are anchored at the start of a line; I haven't confirmed yet that this is the whole cause."

### Rule: No em dashes

I write short sentences joined with periods, commas, or colons. An em dash in my draft usually
means two thoughts that should be two sentences.

- Wrong: "Reproduced on macOS — same output as the issue."
- Right: "Reproduced on macOS. The output matches the issue."

### Rule: My environment, my words

Even on a shared issue, my comment comes from my own run and says it in my own words. I never
lean on someone else's report as my proof.

- Wrong: "Same as above, can confirm."
- Right: "Reproduced independently at commit 2f4e82f on macOS 26.6.2 with Python 3.13; my output is below."

### Rule: State the approach, not the outcome

A plan commits me to an approach, not to a result. I say what I will change and what I will run
to check it. The tests decide whether it worked, not my comment.

- Wrong: "This change will fix section detection for indented resumes."
- Right: "I plan to strip each line before the header patterns run, then re-run the issue's snippet and the five #54 tests."

### Rule: Say what I'm leaving out

A plan names its edges. One line says what the change does not touch, so nobody has to guess how
far it reaches.

- Wrong: "I'll tidy up the parser while I'm in there."
- Right: "I'm not changing the README parser, the section names, or the test fixtures."

### Rule: On a shared issue, my plan stands on my own repro

Other plans and PRs can sit on the same thread. I can say they exist, but my plan comes from my
reproduction and is written in my words. I don't compare it with theirs or borrow from them.

- Wrong: "Same approach as the plan above."
- Right: "Other plans and a PR are already up here; this one is built from my reproduction above."

### Rule: The description promises exactly the diff

A PR description is read next to the diff. Every change I list is a hunk a reviewer can find,
and nothing in the diff is missing from the list.

- Wrong: "Fixes section detection and cleans up the parser."
- Right: "Changes two lines in `resume_parser.py`: `_detect_sections()` matches a stripped copy of each line, and `_strip_markdown()` allows spaces or tabs before `#`."

### Rule: Paste the output, don't describe it

Test results go in as the command and what it printed. A sentence saying a test passed is a
claim; the output is the evidence.

- Wrong: "All tests pass and the snippet works now."
- Right: "`pytest tests/unit/test_resume_parser.py -q` printed `10 passed in 0.12s`."

### Rule: Say a shortfall once, plainly

When something failed or I left something out, I say what and why in one sentence, without
apologizing or burying it.

- Wrong: "Sorry, I wasn't totally able to get typecheck working, hopefully that's ok."
- Right: "`make typecheck` fails on my Python 3.13 venv before it reaches this code (numpy stubs use 3.12+ syntax); with `--python-version 3.13` mypy reports no issues."

## Things I never post

- A date or a guarantee for a fix.
- "+1", "same here", or "can confirm" without my own output under it.
- A cause stated as fact before I have shown it.
- An answer to a maintainer's question that I did not write myself.
- An @-mention of a maintainer who is not already in the thread.
- Praise of my own rigor ("thorough", "complete", "rigorous") in place of the evidence.
- "This fixes it" before the tests have shown it.
- A change list wider than the bug: drive-by cleanup, renames, or upgrades folded into the fix.
