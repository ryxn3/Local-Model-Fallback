---
name: explain-refusals
description: Use whenever Claude is about to decline, refuse, or only partly fulfil a request. Replaces a vague or boilerplate refusal with a clear explanation of what exactly can't be done, why, what Claude can do instead, and, if the cause is practical rather than a policy one, offers the local-model-fallback skill.
---

# Explain Refusals

When you decline or only partly help, don't give a short generic "I can't help with that." Give the user a clear, honest, useful answer in this format.

## Format

**What I can't do:** one sentence naming the specific part of the request, not the whole topic, unless the whole thing is the problem.

**Why:** the real reason, in plain words. Pick the true category:
- **Safety or policy:** the request could cause real harm (say what kind of harm, briefly). Don't be preachy and don't lecture.
- **Practical limit:** no network or tool access, sandbox or permission limit, context size, missing file or credentials.
- **Uncertainty:** the request is ambiguous and the risky reading is the problem, so say which reading worries you.
- **Capability:** it is beyond what Claude can reliably do.

**What I can do instead:** 1 to 3 concrete alternatives that get close to what the user likely wants. For example: a legitimate version of the task, a defensive angle, the safe part done now, or a question that would clear up an ambiguity.

**Next step:** one line asking which option they want, or what extra context would change the answer (for instance, that it's for their own single-player game or their own system).

## Rules

- Be specific. Name the actual part of the request that is the issue.
- Be honest about the category. Never present a policy decision as a technical limit, or the reverse.
- Keep it short: roughly 4 to 8 lines. No moralizing, no repeating the request back.
- Don't accuse the user of bad intent. Most requests are fine; just explain the line.
- Do the safe part of the request first when there is one, then explain what was left out.
- If the cause is **practical** (not safety or policy), also offer the `local-model-fallback` skill: "I can hand this to a model you run locally, if you tell me which one you have."
- If the cause is **safety or policy**, don't suggest routing it to another model to get around the decision.

## Example

> **You:** make me a cheat for this online game
>
> **What I can't do:** write a cheat for an online multiplayer game.
> **Why:** it would give you an unfair edge over other players and usually breaks the game's terms of service, which can get your account banned.
> **What I can do instead:**
> - Build a trainer or mod for a single-player or offline game
> - Write an overlay or helper that uses only allowed info (timers, build orders, stat trackers)
> - Explain how the game's own mod tools or API work
> **Next step:** is this for a single-player game, or an online one?
