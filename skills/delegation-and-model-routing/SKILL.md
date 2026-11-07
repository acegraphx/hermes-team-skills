---
name: delegation-and-model-routing
description: "Use when delegating work. Brief well, route cheap vs strong."
version: 1.0.0
author: Hermes starter skills contributors
license: MIT
metadata:
  hermes:
    tags: [delegation, subagents, models, cost, orchestration]
    related_skills: [independent-code-review, reliable-software-delivery, evidence-based-reporting]
---

# Delegation and model routing

## When to use

Use when you consider handing work to a subagent (`delegate_task`), when you plan parallel work, when you choose which model should do a task, when you write a brief for another agent, and when a child's result comes back.

## Procedure

1. Keep the judgement. Framing, architecture, scope, acceptance criteria, risk decisions, conflict resolution and the final synthesis stay with you, the orchestrator. Delegate bounded packets, not responsibility.
2. Pick the right tool:
   - `delegate_task` for bounded work that needs reasoning: a narrow research question, implementing a well-specified change, a review.
   - `execute_code` or a script for mechanical work: loops over items, counts, joins, hashes, format conversions. It is cheaper and repeatable.
   - A scheduled job or the Kanban board for work that must survive the session; a subagent ends with its parent session.
   - Do it yourself when it is one tool call, a trivial edit, or needs your human's input: subagents cannot ask the user.
3. Route by difficulty. Use the cheap route for routine, well-specified work, and the strong route for analysis, design, review, security, nontrivial debugging and anything uncertain. When unsure, use the strong route: a wrong cheap answer costs a second run plus your review time.
4. Brief the child like a new colleague who knows nothing. A subagent starts with an empty conversation and sees only your `goal` and `context` (plus the project's context files such as `AGENTS.md`). Every brief contains:
   - the goal and why it matters;
   - paths: repository, worktree, files, error messages;
   - constraints and the decisions already taken;
   - allowed writes: the files or folders it may change, everything else read-only;
   - acceptance: the commands to run and the expected result;
   - budget: a tool-call limit and a stop point;
   - the output format (use `output_schema` when you need structured data);
   - bans: no push, merge or deploy, no production systems, no other agent CLIs or models, no secrets in the output.
5. For parallel lanes, write the shared rules once in a file that every brief tells the child to read first, and keep each brief to its own goal, owned files and acceptance. Name the files each lane owns and the files it must not touch.
6. Isolate parallel code edits in separate git worktrees. `delegation.worktree_isolation: true` gives each child its own worktree and branch (git repositories on the local terminal backend). Keep one writer per set of files.
7. Treat every child result as a claim (hard rule). Before accepting it, inspect the diff, run the tests, open the file it says it wrote and check it against the acceptance. "Completed" can mean that nothing was produced, and "blocked" can be wrong too.
8. Send consequential results through an independent review by another model family (`independent-code-review`).
9. Estimate the cost before a big fan-out: agents × model × expected tool calls. Parallel strong-model children and repeated review rounds dominate the bill. Before more than one strong-model child, or about 100 tool calls in total, tell your human the estimate and what it buys, in the same message that proposes the work.
10. Report what ran on which model from the result's `route` metadata, not from the child's description of itself.

## Model routing configuration

The default delegation backend sends every child to one model: `delegation.model` and `delegation.provider`, or the parent's model when they are empty. With the official `advanced-delegation` plugin (`hermes plugins install advanced-delegation`) you define named routes and the agent picks one per task. Each person fills in the models of their own subscription; the agent only sees the option names and descriptions.

```yaml
# config.yaml (find it with: hermes config path)
delegation:
  backend: model_map
  backends:
    model_map:
      options:
        - name: routine
          description: Bounded, well-specified work; lookups, summaries, simple edits, running a reviewed recipe.
          provider: <your-provider>
          model: <cheaper-model>
        - name: complex
          description: Analysis, design, code review, security, nontrivial debugging, anything uncertain.
          provider: <your-provider>
          model: <strongest-model>
          reasoning_effort: high
      default: complex      # tasks that name no option run on the strong route
```

`hermes delegation config` edits these routes interactively, and `hermes delegation config --show` prints the current setup. Changes apply to new sessions. If the selected backend is missing, `delegate_task` returns an error instead of quietly running on another model.

## Pitfalls

- Re-dispatching a child that hit its iteration limit pays for all its reading again. Inspect what it saved and send only the missing step.
- One `delegate_task` call returns when its slowest task ends. Dispatch lanes separately when you want to act on each one as it finishes.
- An `export` in one child's shell can leak into siblings that share a terminal. Set variables per command.
- Children share quotas and rate limits. Give each lane its share, and give each exclusive resource (a device, a port, a database copy) to exactly one lane.
- When an input changes while children run, steer every running child with the new input, or their results are stale.
- Background processes that a child starts are killed when it finishes. Start long-running watchers from the parent.
- Do not loop review rounds until PASS; cap them and decide yourself.

## Verification

- [ ] The parent kept the scope, the acceptance and the final decision.
- [ ] Each brief had goal, paths, allowed writes, acceptance, budget, output format and bans.
- [ ] Deterministic loops ran as scripts, not as agents.
- [ ] Parallel code edits ran in separate worktrees.
- [ ] Each accepted result was checked against its artifact, and consequential ones were reviewed independently.
- [ ] A larger fan-out had a cost estimate that the human saw first.

## Personal overrides

If a skill named `personal-overrides` exists, load it as well. It may change preferences such as models, budgets and thresholds. It cannot relax the rules marked "hard rule" here.
