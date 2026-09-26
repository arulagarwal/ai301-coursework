# Evidence guide: where proof lives in a reproduction package

An eval bundle has five parts, always in this order: `## Repo facts`, `## Issue`,
`## Thread highlights`, `## Candidate claim comment`, `## Candidate repro report`. Read the issue
first; every family below is judged by setting the candidate text against it. In live mode the
same parts come from GitHub (the issue page and the repo's files) and from my two drafts.

## Environment

**Where it lives.**
- Eval: the environment line or block of `## Candidate repro report`, usually its first lines
  ("Environment: ..."). Set it against the issue's own environment in `## Issue` (an "Environment:"
  or "Operating system / version" line, or a sentence saying the behavior depends on the build,
  platform, or runtime) and against the `bug reports:` bullet under `## Repo facts`, which lists
  what the repo's template asks for.
- Live: the environment block of my draft repro comment. Set it against the issue body on GitHub,
  the repo's bug-report template in `.github/ISSUE_TEMPLATE/`, and its setup doc (for Path Review,
  `docs/SETUP.md`, which names the supported Python, Node, and Docker versions).

**What good looks like.** A reader can tell what code ran and on what: the project's version,
release, or commit; the OS or platform; and the value of any factor the issue says changes the
failure (debug or release build, runtime version, a config flag). The version the issue targets
is the one it names, or the latest release under `## Repo facts` when the issue says it was
confirmed on the latest version or main. A newer version or another OS is fine, and good reports
say so ("the issue was filed against 2.1; still present on 2.4"). An older version than the issue targets has
to be named as a deviation, because the code under test may not be the same. A report that never
says where it ran has no environment record, however clean its output looks.

## Steps

**Where it lives.**
- Eval: the commands and actions in `## Candidate repro report` (code blocks with `$` prompts,
  numbered steps, inline actions such as "press `s`, type a name, Enter"), compared with the
  steps or minimal reproduction in `## Issue`.
- Live: the steps in my draft repro comment and nothing else. A file in my working directory
  that the draft does not quote is invisible to a stranger on the thread.

**What good looks like.** Starting from the recorded environment, a stranger reaches the trigger
by doing only what is written: every command is shown, every input the trigger needs is quoted or
its contents given, every UI action is concrete. The trigger is the issue's own: the element the
issue says causes the failure is present and unchanged. Incidental differences (a command alias,
an output or offline flag, a file name) are fine, and so is a reduced input that keeps the
triggering element. Terse is fine. "Set up the project and run it" is not a step, and a changed
triggering input is a different experiment, however small the edit.

## Behavior shown

**Where it lives.**
- Eval: the output excerpts, tracebacks, test results, logs, and described screenshots in
  `## Candidate repro report`, set against the observed or "actual" behavior and any quoted error
  in `## Issue`.
- Live: the output blocks in my draft repro comment, set against the observed output the issue
  body records (its error text, printed value, or named failing tests).

**What good looks like.** An artifact shows the issue's failure signature: the same exception
type or message, panic text, exit status, wrong value, or missing effect. Compare the artifact to
the issue, never to the report's own description of the artifact. An artifact of a different
failure (a parse error where the issue has a panic, a different exception at the same line) is an
adjacent symptom, not a reproduction. A control run, the same trigger with the suspected factor
removed, strengthens a report but is not required. An honest cannot-reproduce shows the actual
output of a faithful attempt.

## Honesty

**Where it lives.** Every sentence of fact in both candidate texts, in `## Candidate claim comment`
and `## Candidate repro report` (live: my two drafts): "reproduced", "confirmed", "same as the
issue", "every time", "on all my machines", "the cause is". Set each one against the artifact it
would need.

**What good looks like.** Claims are the same size as the evidence. "Reproduced" sits on an
output that shows the issue's behavior; "every time" sits on repeated runs that are shown; a
cause is either shown (a quoted code line, a trace frame) or marked as a guess. "I could not
reproduce this on X; output below" is a complete and honest report. Warning signs: emphatic words
doing the work artifacts should ("definitely", "obviously", "exactly the class of failure"), a
cause copied from the issue's own guess and presented as found, and confidence about
understanding in place of output.

## Comms

**Where it lives.**
- Eval: `## Candidate claim comment` read against `## Issue`; both candidate texts read against
  the `contribution policy (...)` bullet under `## Repo facts`, which is where any AI-use policy
  and disclosure requirement is recorded. `## Thread highlights` is context only.
- Live: my draft claim comment against the issue body and thread (`gh issue view <n> --comments`),
  and against the repo's policy files, which often sit one directory down: `CONTRIBUTING.md` in the
  root, `.github/`, or `docs/` (Path Review keeps it at `docs/CONTRIBUTING.md`), plus any
  `AI_POLICY.md`, `AI_USAGE_POLICY.md`, or `AGENTS.md`. For Path Review, `scope.md`'s house rules
  also apply: a classmate's claim does not block mine, and every repro goes up in its author's own
  words.

**What good looks like.** The claim names something only this issue has (a function, command,
error, or symptom) and a next step the author controls. It may describe the planned approach; it
does not promise an outcome, a date, or its own rigor. Where the policy requires AI disclosure, a
comment carries one. Boilerplate that fits any issue ("I'd like to work on this, please assign
me"), a "+1", and pressure on maintainers are the failures here.
