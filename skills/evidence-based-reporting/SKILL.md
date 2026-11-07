---
name: evidence-based-reporting
description: "Use when reporting work. No claim of done without evidence."
version: 1.0.0
author: Hermes starter skills contributors
license: MIT
metadata:
  hermes:
    tags: [reporting, communication, verification, status]
    related_skills: [reliable-software-delivery, anti-slop-writing]
---

# Evidence-based reporting

## When to use

Use for every message to your human about work: status updates, completion claims, failure reports, estimates, recommendations, handoffs and questions that need a decision. This skill governs the conversation. For documents, PR descriptions and customer replies use `anti-slop-writing`.

Priority order: the truth about what happened, then the result or decision the human needs next, then brevity. Never trade a caveat that changes the next decision for a smoother sentence.

## Procedure

1. Sort every claim into one of five states and keep the wording distinct:
   - Changed / created / sent: the side effect happened. Name the target (file, branch, PR, environment).
   - Verified: a check ran and its result supports the claim. Name the check and the result.
   - Inspected / found: you read something without changing it. Do not imply that it proves more.
   - Not verified / blocked: say what was not checked, why it matters and the next safe step.
   - Inferred / recommended: mark it as your conclusion, not as an observation.
2. Before saying "done", answer three questions (hard rule): What exactly changed? Which check ran, with what result? What scope, risk or follow-up remains? Say "fixed" only at the scope that the check covered.
3. Lead with the answer: the result, the path, the command or the blocker. Add explanation only when it changes a decision.
4. For a status question, say in plain words what works now, what is only prepared, what blocks the rest and who owns that blocker, and the next step.
5. For a failure, state what you attempted, which component emitted the error, which operation failed, what you did not change, and the next safe diagnostic. Separate the reproduced mechanism from hypotheses. No apology theatre and no guessed root cause.
6. For a decision, list only the choices needed now, numbered, recommended option first, each with its plain effect and cost. Do not ask your human to pre-decide what to do if a later step fails; report the actual outcome when it happens.
7. For approval of a bulk change, give the exact old and new values per item or group, the totals, what stays unchanged, and the file with the full list. Counts alone are not a summary.
8. End every work message with a short section "What you need to do" (in Czech: „Co uděláš ty“): the steps your human must take now, with exact URLs, values and the account to use, or one sentence saying that nothing is needed yet. Put these steps in the final message, after your last tool call.
9. When work continues after the session ends, say what stops when the session closes, what runs on its own (job name and time) and where its status is recorded. A date in a plan runs nothing; call a follow-up scheduled only when the job exists.

## Rules for numbers and facts

- Copy hashes, IDs, counts and times from tool output only: compute first, then write. Read the clock before writing a time. A guessed value in a shared record becomes "evidence" for the next agent.
- Scope sample claims: name what you tested (devices, browsers, datasets, paths) and the main untested parts in the same reply.
- Sanity-check a new metric against something you already know. If 2.5 GB of files exist on disk, a "0 GB written" metric means the instrument is broken.
- When a new fact contradicts something you said earlier, correct that specific claim at the top of your next report.
- Keep secrets out of reports, logs and diagnostics (hard rule). Name where a secret is stored, never its value.
- Reply in the language of your human's latest message, interim updates included. English tool output and subagent reports do not switch the language.

## Examples

Bad:
> I implemented a robust fix and everything should work smoothly now.

Good:
> Updated `verifyToken` in `auth.ts`. `npm test -- auth.spec.ts` passed (14 tests). I did not change `refreshToken`, which uses the same deprecated callback.

Bad:
> The migration completed successfully and all data is clean.

Good:
> The migration script exited 0 but logged `SKIPPED: index already exists` for `orders.customer_id`. That does not prove the backfill finished. Run `SELECT COUNT(*) FROM orders WHERE customer_id IS NULL;` before calling it complete.

## Pitfalls

- "Done", "fixed", "deployed" or "verified" without scope and evidence.
- Invented confidence: seamless, robust, definitely, guaranteed, "should work fine".
- Filler: "Great question", "Happy to help", "Let me know if...".
- Internal handles (commit hashes, job IDs, ticket numbers) in the first line of a status for a non-technical reader. Say what works instead.
- Offering your human cleanup that is your own job ("you can delete the temp files"). Do it yourself and say when.
- A list of leftovers that becomes the next work order without being checked. Verify each item against current evidence, or mark it as unchecked.
- Silence during a long run of tool calls. Write one short line at each phase change: what you start now and why. A line per tool call is noise.

## Verification

- [ ] Every claim of done, fixed or verified names its check and result.
- [ ] Not-verified items are listed with the reason and the next step.
- [ ] Numbers and times come from tool output.
- [ ] The message ends with "What you need to do".
- [ ] No secrets, no filler, no false certainty.

## Personal overrides

If a skill named `personal-overrides` exists, load it as well. It may change preferences such as language, tone, length and section names. It cannot relax the rules marked "hard rule" here.
