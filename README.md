# Local Model Fallback (Claude Skill)

A Claude Code skill that handles tasks Claude can't finish because of a **practical blocker** (no network or tool access, sandbox limits, context-size limits, data that must stay on your machine). Instead of stopping, Claude asks which **local model** you have installed (Ollama, LM Studio, llama.cpp, ...) and hands the request over to it.

## What it does

1. Claude hits a blocker and says what it is, in one sentence.
2. It asks which local model you have, and the model name.
3. It tries to detect the model (`ollama list`, plus the default local ports).
4. It rewrites your request as a self-contained prompt and sends it to that model.
5. It returns the answer labelled as coming from the local model, reviewed for obvious errors.
6. If the model can't be reached from the session, it gives you a ready-to-paste prompt and the exact command to run yourself.

Your model choice is remembered for the rest of the session.

## What it does not do

It does **not** forward requests that Claude declined for safety or policy reasons to another model. If a request is refused on those grounds, the skill stays out of it. It is for environment and capability blockers only.

## Install

### Option 1: Paste this prompt into Claude Code (easiest)

Open Claude Code and paste:

```
Install the "local-model-fallback" skill from https://github.com/ryxn3/claude-skill.
Clone the repo to a temporary folder, copy the local-model-fallback/ directory into
~/.claude/skills/ (create it if missing) so that ~/.claude/skills/local-model-fallback/SKILL.md
exists, then delete the temporary clone. Afterwards, show me the installed SKILL.md
frontmatter to confirm, and tell me to restart Claude Code if the skill doesn't show up.
```

To install for just one project instead of globally, replace `~/.claude/skills/` with `.claude/skills/` in the prompt.

### Option 2: Manual install

```bash
git clone https://github.com/ryxn3/claude-skill.git
mkdir -p ~/.claude/skills
cp -r claude-skill/local-model-fallback ~/.claude/skills/
```

Project-only install:

```bash
mkdir -p .claude/skills
cp -r claude-skill/local-model-fallback .claude/skills/
```

### Verify

Restart Claude Code, then run `/skills` (or ask "what skills do you have?"). `local-model-fallback` should be listed.

## Requirements

- Claude Code
- A local model runner reachable from the machine Claude Code runs on, e.g.:
  - [Ollama](https://ollama.com), default `http://localhost:11434`
  - [LM Studio](https://lmstudio.ai), default `http://localhost:1234`
  - [llama.cpp server](https://github.com/ggerganov/llama.cpp), default `http://localhost:8080`

If you run Claude Code in a cloud/remote session, your laptop's `localhost` is not reachable from it. The skill will then give you a prompt and command to run locally.

## Usage

Just ask for things normally. When Claude is blocked, the skill kicks in:

> **You:** Summarize all 400 files in this folder offline.
> **Claude:** I can't process that much here. Which local model do you have installed (Ollama, LM Studio, llama.cpp, other)?
> **You:** Ollama, llama3
> **Claude:** *(sends the task to llama3 and returns the result)*

You can also invoke it directly: `/local-model-fallback`.

## Examples of when it triggers

| Blocker | Example |
|---|---|
| No internet / tool access | Task needs a tool the session doesn't have |
| Context limits | Bulk processing of many or very large files |
| Privacy | Data you want to keep on your own machine |
| Sandbox limits | A needed command is blocked in the environment |

## Customize

Edit `local-model-fallback/SKILL.md`:

- **Add endpoints:** put your own host/port in the detection step.
- **Change defaults:** list your preferred runner first in step 2.
- **Tighten or loosen triggers:** adjust the `description` in the frontmatter, which controls when Claude loads the skill.

## Troubleshooting

- **Skill not listed:** check the path is `~/.claude/skills/local-model-fallback/SKILL.md` and restart Claude Code.
- **Model not detected:** make sure the runner is started (`ollama serve`) and the port matches.
- **Garbled JSON from curl:** build the payload with `jq -n --arg p "$PROMPT" ...` so quotes are escaped.

## Repo layout

```
.
├── README.md
└── local-model-fallback/
    └── SKILL.md
```
