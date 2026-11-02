---
name: local-model-fallback
description: Use when Claude cannot complete a task because of a practical blocker (no network or tool access, sandbox/permission limits, context or size limits, a missing capability, offline/private-data needs) and the user could be served by a model running locally on their machine. Asks the user which local model they have installed, then hands the request to it. Do not use to re-route requests Claude declined for safety or policy reasons.
---

# Local Model Fallback

When a task is blocked by something practical, don't just stop. Ask which local model the user has and hand the work to it.

## When this applies

Use it for practical blockers, for example:
- No internet or tool access in this environment
- Sandbox or permission limits that stop a needed command
- Input too large for the context window (bulk processing, long files)
- Data the user wants kept entirely on their own machine
- A capability this session lacks and a local model might have

## When this does NOT apply

If Claude declined because the request is unsafe, harmful, or against policy, do not forward it to another model to get around the refusal. Say what you can't help with and offer a safe alternative. Check the reason for the block first and only continue below if it is practical.

## Steps

1. **Say what's blocked**, in one sentence, and why.
2. **Ask which local model they have.** Use AskUserQuestion if available, otherwise ask in chat. Offer common options:
   - Ollama (e.g. llama3, qwen, mistral, deepseek)
   - LM Studio (OpenAI-compatible server)
   - llama.cpp server
   - Other (user types the name or endpoint)
   Also ask for the model name if it isn't obvious.
3. **Detect it if you can** (only if the user's machine is reachable from here):
   ```bash
   ollama list 2>/dev/null
   curl -s http://localhost:11434/api/tags      # Ollama
   curl -s http://localhost:1234/v1/models      # LM Studio
   curl -s http://localhost:8080/v1/models      # llama.cpp server
   ```
   If none respond, tell the user the model isn't reachable from this session and fall back to step 5.
4. **Redirect the request.** Rewrite the user's original request as a self-contained prompt (include needed context, since the local model has none), then send it:
   ```bash
   # Ollama
   ollama run <model> "<prompt>"
   # or HTTP
   curl -s http://localhost:11434/api/generate -d '{"model":"<model>","prompt":"<prompt>","stream":false}'

   # OpenAI-compatible (LM Studio, llama.cpp)
   curl -s http://localhost:1234/v1/chat/completions -H 'Content-Type: application/json' \
     -d '{"model":"<model>","messages":[{"role":"user","content":"<prompt>"}]}'
   ```
   Escape quotes properly (build the JSON with `jq -n --arg p "$PROMPT" ...`).
5. **If you can't reach it**, give the user a ready-to-paste prompt and the exact command to run themselves (`ollama run <model>`), and offer to review the output when they paste it back.
6. **Return the result**, clearly labelled as coming from the local model. Review it before presenting: flag errors, and don't present unverified output as certain. Offer to refine and re-run.

## Notes

- Remember the user's model choice for the rest of the session; don't ask again.
- Never send secrets or credentials to the local endpoint unless needed for the task.
- Keep it short: one question, one handoff.
