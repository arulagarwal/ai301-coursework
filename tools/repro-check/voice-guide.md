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

## Things I never post

- A date or a guarantee for a fix.
- "+1", "same here", or "can confirm" without my own output under it.
- A cause stated as fact before I have shown it.
- An answer to a maintainer's question that I did not write myself.
- An @-mention of a maintainer who is not already in the thread.
- Praise of my own rigor ("thorough", "complete", "rigorous") in place of the evidence.
