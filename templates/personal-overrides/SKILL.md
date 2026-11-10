---
name: personal-overrides
description: "Use with every team skill. Personal rules win conflicts."
version: 1.0.0
author: <your name>
license: MIT
metadata:
  hermes:
    tags: [personal, preferences, overrides]
    related_skills: [reliable-software-delivery, evidence-based-reporting, company-brain, anti-slop-writing, reliable-web-research, independent-code-review, skill-authoring, delegation-and-model-routing]
---

# Personal overrides

## When to use

Load this skill together with any team skill. It holds this person's preferences and local details. Where it conflicts with a team skill, this skill wins, except for the rules that a team skill marks as "hard rule": those cannot be relaxed here.

This file is user-owned. It lives in this profile's skills directory (for example `~/.hermes/skills/personal/personal-overrides/SKILL.md`), it is never committed to the team repository, and team updates never overwrite it.

## Procedure

1. Before applying a team skill, read the matching section below.
2. Apply the override. If an override would break a hard rule, follow the team skill and tell your human.
3. When your human corrects how you work and the lesson is personal, add it here as one rule with its reason.
4. When a lesson would help the whole team, propose it to the team repository (see `skill-authoring`), and remove it from here after the merge.

## Overrides by skill

### General
- Reply language: <for example Czech>
- <another general preference>

### reliable-software-delivery
- Test command: <for example the repository's test runner>
- Build and lint commands: <...>

### evidence-based-reporting
- <for example: keep status updates under five lines>

### company-brain
- Brain clone path: <for example ~/company-brain>
- Personal wiki path: <for example ~/wiki>
- Git identity: Hermes (<Full Name>)

### anti-slop-writing
- <for example: preferred terms for our product areas>

### reliable-web-research
- <for example: official documentation sites to try first>

### independent-code-review
- Preferred reviewer: <a model from another family than the one you use daily>

### skill-authoring
- <...>

### delegation-and-model-routing
- routine route: <model>; complex route: <model>

## Pitfalls

- Do not copy team rules here; write only what differs.
- No secrets here. Point to where a secret is stored instead.

## Verification

- [ ] Each override sits under the team skill it changes.
- [ ] No override relaxes a hard rule.
