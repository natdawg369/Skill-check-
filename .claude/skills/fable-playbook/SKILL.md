---
name: fable-playbook
description: A working method for planning a task, breaking it into steps, and checking your own work before reporting. Use at the start of any task that is more than a single obvious edit — multi-file changes, bug hunts, refactors, research questions, anything with unclear requirements — and again before you say something is done.
---

# Fable playbook

This is a working method, handed from one model to the next. It covers three
things: how to plan, how to break work into steps, and how to check the result.
The parts that matter most are the ones that are easy to skip when a task feels
simple, so the playbook spends its words there.

Scale everything here to the task. A one-line fix needs ten seconds of planning
and one check. A migration needs a written plan and several checks. Applying the
full method to a trivial task is as much a failure as skipping it on a hard one.

## 1. Plan

### Work out what is being asked

Before touching anything, state the request to yourself in one or two sentences:
what will exist or be true when you are finished, and for whom. If you cannot,
you do not yet understand the task.

Then separate three things:

- **The stated request**: the words the user wrote.
- **The goal behind it**: why they want it. "Make this test pass" usually means
  "make the code correct", and deleting the assertion satisfies the first but
  not the second.
- **The constraints**: what must not change, what conventions the project
  follows, what the user has already decided. A decision the user made is
  settled; do not reopen it.

### Look before you decide

Most bad plans come from planning against an imagined codebase. Read the real
one first.

- Read the files you will change, and the code that calls them.
- Find how the project already solves similar problems, and follow that pattern
  unless there is a reason not to.
- Find out how the project is built, run, and tested, because that determines
  how you will check your work later.
- Check the state of things: git status, the current branch, failing tests that
  were already failing before you started.

Stop exploring once you can name the files you will change and how you will
verify the change. Further reading after that point is procrastination.

### Decide what you do not know

List your assumptions and sort them:

- **Checkable**: you can find out by reading code or running a command. Do so.
  Never ask the user something the repository can answer.
- **A sensible default exists**: pick it, proceed, and mention the choice in
  your report.
- **Only the user can decide**, and the answer changes what you build: ask,
  once, with a specific question and your recommendation.

### Define done before you start

Write down the observable evidence that will show the task is complete: a test
that passes, a command whose output changes, a page that renders, a number that
matches. If you cannot name the evidence, you will not know when to stop, and
you will be tempted to declare success because the code looks right.

### Choose the approach

Prefer the smallest change that fully achieves the goal. If two approaches are
plausible, pick one, with a one-line reason; do not present a survey. Raise an
alternative only when the trade-off is the user's to make.

## 2. Break it into steps

### Shape of a good step

Each step should:

- produce one result you can verify on its own;
- leave the project in a working state when it is finished;
- be small enough that if it fails, the cause is obvious.

"Implement the feature" is not a step. "Add the `expires_at` column and
migration; confirm the migration runs up and down" is one.

### Ordering

1. **Riskiest or least certain first.** If the whole plan depends on whether an
   API supports something, test that before building around it. Finding out at
   step one costs minutes; finding out at step six costs the whole task.
2. **Reproduce before fixing.** For a bug, the first step is always to make the
   failure happen on demand. A fix for a bug you have not reproduced is a guess.
3. **Thin end-to-end path before breadth.** Get one case working through every
   layer, then widen it. This exposes integration problems early.
4. **Dependencies before dependents**, and independent steps run in parallel
   where the tools allow.

### While executing

- Verify each step before starting the next. Errors caught one step later are
  cheap; errors caught five steps later are tangled with everything since.
- Keep a visible task list for anything over about three steps, and keep it
  current. Mark a step done only when its check has passed.
- Change one thing at a time when diagnosing. Two simultaneous changes tell you
  nothing about which one mattered.
- Stay inside the task. If you notice an unrelated problem, note it for the
  report and leave it alone.

### When the plan stops matching reality

A surprise is information about the plan. When something behaves in a way you
did not predict, stop and work out why before continuing, because the same
wrong assumption is probably underneath later steps too.

- If the same approach has failed twice, do not try it a third time with small
  variations. Step back, re-read the error in full, and question the assumption
  you have been holding longest.
- Read error messages completely, including the part after the first line.
- If the task has grown well beyond what was asked, or needs something
  destructive or hard to undo, stop and tell the user before proceeding.

## 3. Check your own work

Checking means gathering evidence that you could be wrong and failing to find
it. Rereading your own code and agreeing with it is not a check.

### Run it

- Run the code path you changed. Compiling is not running; tests passing is not
  the feature working, if the tests do not cover what you changed.
- Run the project's own tests, linter, and type checker, and read the output
  to the end instead of trusting the exit code or the first screen.
- For a bug fix, confirm the reproduction from earlier now passes, and that it
  fails again without your change if that is cheap to show.
- For anything with a user interface, look at it: open the page, take the
  screenshot, run the command as a user would.

### Try to break it

Test the cases you did not design for: empty input, one item, very many items,
missing or malformed values, the second run after the first, the error path.
Ask what else calls the code you changed, and check those callers.

### Read the diff as a reviewer

Before reporting, read the complete diff as if someone else wrote it:

- Does every changed line serve the task? Remove debugging output, stray
  edits, commented-out code, and files you created only for scratch work.
- Does it match the surrounding code's naming, structure, and conventions?
- Did you change anything you were not asked to change?
- Did you weaken a check to get to green, such as a skipped test, a loosened
  assertion, a swallowed exception, or a suppressed type error? If so, undo it
  and fix the real problem.

### Check against the original request

Go back to the user's message and read it again, word for word. Tasks drift
during execution, and the thing you finished is sometimes not quite the thing
asked for. Confirm each part of the request has been addressed, including
small ones mentioned in passing.

### Report what is true

The report is part of the work. The reader was not watching, so it has to stand
alone.

- Lead with the outcome: what now works, or what you found.
- State what you verified and how, and separately what you did not verify and
  why. "Tests pass" and "I could not run the tests, so this is untested" are
  both acceptable reports; presenting the second as the first is not.
- If something failed, say so and include the relevant output.
- Flag anything the user needs to decide or check, and any assumption you made
  on their behalf.
- Leave out the story of how you got there unless it changes what the reader
  should do.

## Quick reference

Before starting:

1. Can I state the goal in two sentences?
2. Have I read the code I am about to change?
3. Do I know what evidence will show it is done?

Before each step:

4. Is the previous step verified?
5. Is this the riskiest remaining unknown, or am I avoiding it?

Before reporting:

6. Did I run it, and read all of the output?
7. Did I read the full diff?
8. Did I re-read the original request?
9. Does my report distinguish verified from assumed?
