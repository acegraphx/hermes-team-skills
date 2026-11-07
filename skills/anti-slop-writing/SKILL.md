---
name: anti-slop-writing
description: "Use when writing docs, PRs or replies. Cut slop, keep facts."
version: 1.0.0
author: Hermes starter skills contributors
license: MIT
metadata:
  hermes:
    tags: [writing, editing, documentation, czech, anti-slop]
    related_skills: [evidence-based-reporting, humanizer]
---

# Anti-slop writing

## When to use

Use when you draft, rewrite or review prose that people will read: documentation, READMEs, PR descriptions, release notes, commit messages, customer replies, proposals, internal announcements and posts, in English or Czech. Also use it when someone says a text sounds generic, robotic or translated. For your own status reports use `evidence-based-reporting`. If Hermes's bundled `humanizer` skill is installed, use it as a longer catalogue of patterns; this skill wins where they differ.

The goal is true, specific, natural prose, not text that fools a detector. A rough true sentence beats a smooth one that adds claims or hides uncertainty. Do not replace AI patterns with a new formula of clipped, punchy fragments.

## Procedure

1. Choose the mode before editing.
   - Strict evidence mode: facts other people will rely on (docs, PRs, release notes, customer replies, proposals, anything published). This is the default.
   - Draft mode: brainstorms and rough variants. Freer style is fine; mark invented details as placeholders.
   - Review mode: when asked whether a text is good. Give the exact span, a label (filler, unsupported claim, promise drift, evidence gap, calque, formatting artifact), why it fails, and the fix or the missing input.
2. List the preservation set before rewriting: names, product names, numbers, dates, versions, prices, URLs, quotes, commands, identifiers, scope limits, uncertainty, obligations and the author's tone. After rewriting, compare. A changed or lost item is a blocker.
3. Write or rewrite. In strict mode (hard rule): do not add features, metrics, owners, deadlines, promises, causes or customer facts that the source does not support; do not turn "may help" into "helps"; do not add quality labels (secure, easy, robust, proven) without evidence. Where a fact is missing, leave a slot such as `[number]` or `[source]`, or ask.
4. Run the pattern pass below, then ask yourself what still sounds generated, and fix that.
5. Run the density checks. Delete sentences that only carry rhythm or transition. If a paragraph still works after you swap the product name for another one, it is too generic. If the text can shrink by half without losing an idea, it was scaffolding.
6. Compare with the preservation set once more before you hand the text over.

## Patterns to cut

- Significance inflation: "plays a pivotal role", "marks a turning point", "a testament to", "the evolving landscape".
- Promotional words: seamless, robust, powerful, innovative, cutting-edge, vibrant, groundbreaking.
- Vague authorities: "experts say", "industry reports", with no named source.
- Tails that fake depth: "..., highlighting its importance", "ensuring a smooth experience".
- Copula avoidance: "serves as", "stands as", "boasts" where "is" or "has" works.
- "Not just X, but Y", "from X to Y" without a real range, and forced groups of three.
- Chatbot artifacts: "Great question", "I hope this helps", "Let me know if", "Here is an overview".
- Knowledge-cutoff disclaimers and stacked hedging: "it could potentially be argued that it might".
- Signposting: "Let's dive in", "Here's what you need to know". Do the thing instead.
- Rhetorical questions answered at once, punchy one-line kickers, reassurance ("And that's okay").
- Generic upbeat endings: "The future looks bright".
- Formatting tics: em dashes everywhere, bold label bullets, Title Case Headings, decorative emoji, citation debris left by tools.
- Filler: "in order to" (to), "due to the fact that" (because), "at this point in time" (now).

One signal word is not a defect. It becomes one in a cluster, or when it weakens truth or clarity.

## Context rules

- PR descriptions: what changed and why, how it was verified (commands and results), risks and rollback, and what the reviewer should look at. No marketing.
- Documentation: the task first, then prerequisites, exact commands and the expected output. Avoid "simply" and "just".
- Customer replies: the answer first, then the next step that is actually available. Never promise an action, a date or a fix that the team has not agreed (hard rule). Leave out internal jargon and ticket handles, and reply in the customer's language.
- Commit messages: an imperative subject that says what changed; the body says why.

## Czech

Established technical terms are fine in Czech: API, commit, branch, pull request, deploy, build, release, bug, frontend, backend, workflow, prompt, token. The problem is Czech words arranged in English logic. Fix these calques:

- „adresovat problém“ → řešit, vyřešit, zabývat se
- „doručit výsledky“ → dodat, přinést, udělat
- „udělat rozhodnutí“ → rozhodnout se
- „mít dopad na“ → ovlivní, zasáhne, změní
- „v rámci“ as filler → při, během, v, pro, or nothing
- „je to o tom, že“ → state the claim directly
- „na denní bázi“ → denně; „v tuto chvíli“ → teď, nyní
- „řešení“ as a universal noun → aplikace, nástroj, postup, úprava
- „umožňuje uživatelům efektivně…“ → say what they can now do
- the frames „Nejen X, ale také Y“, „Od X po Y“, „Ať už jste X, nebo Y“, „Toto zajišťuje…“ used as filler
- chains of verbal nouns in status lines („Načítání nastavení…“) → a short sentence with a verb („Načítáme nastavení…“)

Priorities: meaning first; verbs over abstract nouns; concrete nouns over containers; Czech word order for emphasis; read it aloud. Make counts agree in case: 1 den, 2–4 dny, 5 dní, but před 2 dny, před 5 dny, za 5 dní. In text that a page or an app renders, keep a one-letter preposition or conjunction (k, s, v, z, o, u, a, i) on the same line as the next word with a non-breaking space.

Bad: „V rámci našeho workflow doručíme kvalitní výstupy na pravidelné bázi.“
Better: „V našem workflow pravidelně dodáváme výstupy, které se dají zkontrolovat a nasadit.“

## Pitfalls

- Flattening a text that was already good. Sometimes the right edit is to leave it alone.
- Deleting a caveat that changes the reader's next action.
- Adding a confident claim to make the prose sound stronger.
- Claiming that a text is "undetectable" or "guaranteed human". Detector results are weak, dated evidence at best.
- Treating a lint warning as proof. Check the matched span before rewriting.

## Verification

- [ ] Every item of the preservation set survived unchanged.
- [ ] No new facts, promises or quality claims without a source.
- [ ] The pattern pass and the "what still sounds generated?" pass are done.
- [ ] Czech text reads naturally aloud and the counts agree.

## Personal overrides

If a skill named `personal-overrides` exists, load it as well. It may change preferences such as tone, register, formatting and preferred terms. It cannot relax the rules marked "hard rule" here.

## Sources

The pattern list condenses Wikipedia's "Signs of AI writing" guide and Hermes's bundled `humanizer` skill (MIT licence).
