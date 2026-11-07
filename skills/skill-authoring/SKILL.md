---
name: skill-authoring
description: "Use when writing or fixing skills. Lessons, not logs."
version: 1.0.0
author: Hermes starter skills contributors
license: MIT
metadata:
  hermes:
    tags: [skills, hermes, learning, maintenance]
    related_skills: [company-brain, independent-code-review, evidence-based-reporting]
---

# Skill authoring

## When to use

Use when you create or edit a skill, when your human corrects how you work, after a mistake that a rule would have prevented, after a non-trivial workflow worth repeating, and when a skill turned out to be wrong or to miss a step.

A skill is code that steers an agent. Treat it like code: small, reviewed, tried on examples.

## Rules for content

1. Write lessons, not logs. State the rule and why it exists, not the story of the incident. No dates, ticket numbers, names or client details.
2. One rule per lesson, in the imperative, with its reason: "Run the targeted tests before reporting done, because an unrun test is not evidence."
3. After a mistake, patch the skill that governs the task, with the smallest change that would have prevented it. Do not create a new micro-skill for one incident.
4. Keep skills short: `SKILL.md` within about 60 to 160 lines. Move conditional depth into `references/<topic>.md`, copyable starters into `templates/`, rerunnable actions into `scripts/`. Extend an existing reference file before adding a new one.
5. Use class-level names (`reliable-web-research`, not `fix-the-june-scraper`). Prefer extending an existing skill to creating a near-duplicate.
6. Never invent commands, flags or config keys. Check them with `hermes <command> --help` or the official documentation first.
7. Try a new rule on a negative and a positive example, so that a correction does not also forbid a legitimate action.
8. Keep facts, procedures and preferences apart: team facts go to the company brain, procedures to skills, personal preferences to memory or `personal-overrides`.
9. No secrets, personal data, customer names or internal URLs in skills (hard rule).

## Format

```text
---
name: my-skill                       # lowercase, hyphens, same as the folder name
description: "Use when <trigger>. <behaviour>."   # at most 60 characters
version: 1.0.0
author: <team or person>
license: MIT
metadata:
  hermes:
    tags: [topic, topic]
    related_skills: [other-skill]
---
# Title
## When to use
## Procedure
## Pitfalls
## Verification
```

The description is the routing signal: the skill index shows it to the agent, and only then does the agent load the body. Start with the trigger and keep behaviour details in the body.

## Procedure: team skills versus your own copy

Team skills come from the team repository and are locked in your profile (`hermes curator lock <skill>`), so neither you nor the background review can change them in place. When one of them is wrong or misses a lesson:

1. For now, apply the lesson through your human's `personal-overrides` skill, or ask them to add it.
2. Tell your human what you would change in the team skill and why.
3. If they agree, propose it to the team repository as a pull request:
   - Fetch and create a separate worktree: `git -C <team-clone> fetch`, then `git -C <team-clone> worktree add ../hermes-team-skills-pr-<topic> -b skill/<login>/<topic> origin/main`.
   - Edit the skill there and bump `version` (patch: wording or a clarified lesson; minor: a new rule or section; major: changed or removed behaviour). Add a line to `CHANGELOG.md`.
   - Check the frontmatter, the description length and the line count.
   - Commit with the agent identity and a `Co-authored-by` trailer for your human, push, and open the PR with `gh pr create`. In the description: the lesson, the generic failure it prevents, a summary of the diff, and how you tried it.
   - Never merge it yourself (hard rule). A maintainer reviews it like code.
4. After the merge your human updates the clone (`git pull --ff-only`), and you remove the duplicate rule from `personal-overrides`.

Your own skills live in your profile's skills directory (`~/.hermes/skills/`). They are private. Never copy them into the team repository without your human's explicit approval, and strip personal details first.

## Hermes mechanics

- `skill_manage` creates and patches skills. With `skills.write_approval: true` in `config.yaml`, every skill write is staged for approval: `/skills pending`, `/skills diff <id>`, `/skills approve <id>`, `/skills reject <id>`.
- `hermes curator lock <skill>` stops agent changes to one skill, and only the human can run `hermes curator unlock <skill>`. `hermes curator status` lists locked skills.
- `hermes curator ledger --skill <name>` shows who changed a skill (agent, curator or user); `hermes curator rollback <entry-id>` undoes one change.
- Editing a bundled skill marks it as user-modified, and Hermes then stops updating it. Put local rules into your own overlay skill instead.
- A skill created or removed during a conversation is announced on your next message; start a new session for a clean skill index.

## Pitfalls

- Appending incident history until the skill is too long to read. Consolidate instead.
- Rules so broad that they block legitimate work ("never retry anything").
- A description longer than 60 characters, or one that starts with background instead of the trigger.
- Editing a team skill directly in the clone: the next `git pull` conflicts, and meanwhile your agent runs an unreviewed version.
- Installing a stranger's skill without reading it. Read `SKILL.md` and every script; for hub skills run `hermes skills inspect <identifier>` first. A security scan is not a review.

## Verification

- [ ] The frontmatter parses, `name` equals the folder name, and the description has 60 characters or fewer.
- [ ] `SKILL.md` stays within about 160 lines; depth lives in `references/`.
- [ ] Every new rule has a reason and was tried on a positive and a negative example.
- [ ] Commands and keys in the skill were checked against `--help` or the docs.
- [ ] `hermes skills list` shows the skill, and a new session loads it.
- [ ] Team skill changes went through a PR, not a local edit.

## Personal overrides

If a skill named `personal-overrides` exists, load it as well. It may change preferences such as naming and where your personal skills live. It cannot relax the rules marked "hard rule" here.
