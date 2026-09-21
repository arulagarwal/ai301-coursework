# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s3`

Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

I am Arul Agarwal (@arulagarwal), a Northeastern graduate student. My open-source
track record is four merged pull requests, all native mobile in Swift: three to
`mozilla-mobile/firefox-ios` (a UIKit `layoutSubviews` edit-state bug, an Auto Layout
compression-resistance tie in iPad split view, and an async stale-response race in the
search suggestions view model) and one to `skiptools/skip-ui` (`.colorset` colour parsing
across the Swift-to-Kotlin transpiler). I am comfortable with XCTest and XCUITest, git
archaeology (`git log -S`, `-L`, blame) for finding when a latent bug was armed, and the
`gh` CLI.

Python is where I have the most coursework but the least open-source mileage: graduate
computer vision and NLP projects, plus several applied-AI modules. I read and write it
fluently; I have just never shipped it to someone else's repo. This term I specifically
want Python and AI-subsystem exposure — retrieval, agent orchestration, safety filtering —
because that is the gap between what I have built for class and what I have contributed.
Rank Python issues in those areas above equivalent ones elsewhere. I can work
TypeScript/React if the issue is otherwise strong, but it is my weakest of the three.

What I execute well: a narrow, deterministic bug with an exact reproduction and a named
failing test, where the acceptance condition is unambiguous. All four of my merged PRs had
final diffs between two and five lines, with the work in the diagnosis rather than the
edit. Rank an issue higher when it ships a runnable repro snippet or names the tests that
currently fail.

What costs me time: issues where the acceptance condition is a judgement call rather than a
stated condition, and issues whose fix requires inventing an approach the thread has not
settled. On two of my three firefox-ios PRs the maintainer asked me to delete every test I
had written, because I had not read the project's testing conventions before building for
them — so rank up issues where the project has already said, in tests or in writing, what
"done" looks like.
