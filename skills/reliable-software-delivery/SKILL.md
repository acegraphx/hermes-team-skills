---
name: reliable-software-delivery
description: "Use when changing code: plan, test, review, deploy safely."
version: 1.0.0
author: Hermes starter skills contributors
license: MIT
metadata:
  hermes:
    tags: [software-delivery, git, testing, review, deployment, rollback]
    related_skills: [independent-code-review, evidence-based-reporting, delegation-and-model-routing]
---

# Reliable software delivery

## When to use

Use for every change to a code repository: bug fixes, features, refactors, dependency updates, migrations, releases and deployments. Also use it when you continue work that another person or agent started. A one-line fix still follows steps 1, 3 and 6 in miniature.

## Procedure

1. Ground the task. Read the repository's own instructions first (`AGENTS.md`, `README*`, `CONTRIBUTING*`, `CLAUDE.md`), then `git status`, the current branch and the recent commits. Separate fixed requirements from guesses and examples. Ask only the questions whose answer changes the result. Reason: a plan built on stale or assumed context produces the wrong change, however well it is tested.
2. Plan in proportion to risk. For anything larger than a trivial edit, write a short plan: the outcome, the constraints, the files or components touched, the acceptance check (a command or an observation that proves it works), the risks and the rollback. For bigger work use `/plan`, which writes the plan to `.hermes/plans/` instead of executing it. Size the checks to the boundary you touch: a documentation change needs a link and diff check, not the whole application suite; a database migration needs a tested restore.
   A plan that fits in a PR description:

   ```text
   Outcome:    <what the user can do afterwards>
   Constraints: <no API change, keep backwards compatibility, ...>
   Touches:    <files, components, data>
   Acceptance: <command or observation, with the expected result>
   Risks:      <what could break, and for whom>
   Rollback:   <the exact undo>
   ```

3. Isolate the work (hard rule: never commit to `main` directly). Work on a branch. For parallel work, or to give a reviewer a stable view, use a separate worktree: `git worktree add ../<repo>-<topic> -b <branch>`. Keep one writer per set of files. Never switch branches or leave uncommitted edits in a checkout that a running service or a scheduled job uses. Reason: isolation keeps unrelated work intact and makes the review and the rollback exact.
4. Reproduce before fixing. For a bug, first capture a failing test or a failing command built from a real input (a real payload, a real file shape), not a fixture you invented. Reason: invented fixtures can pass while every real record still fails.
5. Make the smallest change that delivers the outcome. Extend the existing code path instead of building a parallel one. Do not add speculative hardening or features nobody asked for; offer them with their cost instead.
6. Test before you claim anything (hard rule). Run the tests that cover the changed behaviour, including the negative and error paths, plus the repository's lint, type and build checks. Run the full suite when you touched shared code or the repository requires it. Record each command and its result. A test you did not run is "not run", never "passing".
7. Get an independent review for consequential changes. Security, permissions, money, personal data, migrations, public behaviour and anything hard to undo get one review on a frozen commit, ideally by a model from another family. Follow `independent-code-review`. Re-review once after fixing a blocker or major finding; fix low and info findings yourself. A PASS ends the loop.
8. Deploy only when your human or the team's release process authorizes it (hard rule), then verify live. Confirm that the deployed version is the reviewed commit (version endpoint, release tag, build hash). Check the user-visible path itself, not only the CI status or an exit code. Watch errors and logs for a short window.
9. Keep the rollback ready. Before deploying, know the exact undo: a revert commit, the previous release tag, a feature flag, or a database restore you have tested. If live verification fails, roll back first and diagnose second.
10. Report with evidence. Use `evidence-based-reporting`: what changed, what was verified and how, what was not verified, and what the human must do.

## Pitfalls

- A helper that passes its unit test does not prove the real caller uses it. Trace from the actual entry point to the outcome.
- Exit code 0 or a green summary is not the result. Read the output; a reviewer that exits 0 may still request changes.
- An old green log is not evidence for new code. Rerun the checks after every change to their inputs.
- Running a formatter over whole files buries your change in unrelated lines. Format only what you changed, or files that were already clean.
- Weakening an assertion to make a test pass hides the defect. Fix the code, or record the test as a known failure with its reason.
- In Python, a test inserted between a decorator and its function moves the decorator (a skip or a live-test gate) onto the wrong target.
- Shell state leaks between parallel tasks. Set git identity and other variables per command (`git -c user.name=... commit`, `VAR=value cmd`); do not `export` them.
- A running process or a successful timer does not prove that the new version executed. Check the version that actually ran.
- A partial backup is not a recovery point. Prove the restore before a migration.
- When your human narrows the scope, keep everything the main goal needs on the normal path and drop only the extras.
- Polishing beyond the requested outcome burns time and tokens. After the outcome is verified, offer further improvements with their cost instead of starting them.

## Verification

- [ ] The change lives on a branch or worktree, not on `main`.
- [ ] A failing test or command reproduced the bug before the fix, and it passes now.
- [ ] Targeted tests, lint and build ran; the commands and results are in the report.
- [ ] Consequential changes had an independent review on a frozen commit, and blockers and majors are closed.
- [ ] After a deploy, the live version matches the reviewed commit and the user-visible path works.
- [ ] The rollback is written down and possible.
- [ ] The report separates verified, not verified and inferred.

## Personal overrides

If a skill named `personal-overrides` exists, load it as well. It may change preferences such as language, tools, test commands and thresholds. It cannot relax the rules marked "hard rule" here.
