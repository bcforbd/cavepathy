<p align="center">
  <img src="icon.svg" width="72" alt="cavepathy" />
</p>

<h1 align="center">Cavepathy</h1>

<p align="center">
  <strong>Terse, accurate coding replies. Zero filler. Zero silent assumptions.</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="MIT License">
  <img src="https://img.shields.io/badge/Claude%20Code-Skill-D97757" alt="Claude Code Skill">
  <img src="https://img.shields.io/badge/agents-8-6B7280" alt="8 agents">
</p>

<p align="center">
  <a href="#why-this-exists">Why</a> •
  <a href="#before--after">Before / After</a> •
  <a href="#install">Install</a> •
  <a href="#usage">Usage</a> •
  <a href="#how-it-works">How it works</a> •
  <a href="#repo-layout">Repo layout</a>
</p>

---

Cavepathy is a [Claude Code skill](https://code.claude.com/docs/en/skills) (also works in Codex, Hermes, OpenClaw, Gemini CLI, Kilo Code, OpenCode, and Cline) that merges two separate, well-known prompt skills into one:

- **[caveman](https://github.com/JuliusBrussee/caveman)** — compresses *how* Claude talks. Drop filler, articles, hedging. ~65–75% fewer output tokens. Full technical accuracy kept.
- **[karpathy-guidelines](https://github.com/multica-ai/andrej-karpathy-skills)** — governs *how* Claude thinks before it codes. Surface assumptions instead of guessing, keep changes minimal and surgical, verify before claiming done. Derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on common LLM coding failure modes.

Neither skill is novel here — both already exist, and you can install either one alone. Cavepathy is the merge, plus one rule that makes combining them actually safe.

## Why this exists

Run **caveman alone** and it will happily compress away the exact sentence that matters most — the assumption it should have asked you about, the tradeoff it should have flagged. Fast, but silently wrong.

Run **karpathy-guidelines alone** and you get careful, well-reasoned replies that surface every assumption and tradeoff — in three paragraphs, when three sentences would do. Correct, but expensive.

Cavepathy adds one rule that resolves the conflict instead of picking a side:

> **Compression may shorten a stated assumption, tradeoff, clarifying question, or pushback. It may never delete one.**

Karpathy's four principles decide *what* gets said. Caveman decides *how tersely* it gets said. Compression is a phrasing filter applied after the thinking is already correct — never a substitute for it.

## Before / After

**Caveman half — compress the phrasing:**

> **Before:** "The reason your React component is re-rendering is likely because you're creating a new object reference on each render cycle. When you pass an inline object as a prop, React's shallow comparison sees it as a different object every time, which triggers a re-render. I'd recommend using useMemo to memoize the object."
>
> **After:** "New object ref each render. Inline object prop → new ref → re-render. Wrap in `useMemo`."

**Karpathy half — keep the assumption, lose the bloat:**

> **User:** "Add rate limiting to the API"
>
> **Caveman alone:** "Done. Added rate limiting." — fast, but it silently picked an algorithm, a limit, and a scope.
>
> **Karpathy alone:** "Sure, I'd be happy to help! There are several approaches we could take, such as token bucket, sliding window, or fixed window algorithms, each with their own tradeoffs in terms of accuracy and memory usage. Could you let me know which you'd prefer, and also what limit you have in mind?" — asks the right things, costs way too many tokens to ask them.
>
> **Cavepathy:** "No algorithm/limit specified. Assuming token-bucket, 100 req/min/IP, in-memory — flag if wrong. Scope: middleware only. Plan: 1) bucket logic → verify: unit test for over-limit reject 2) wire into middleware → verify: integration test. Building now."

Same assumption stated. Same scope bounded. A fraction of the tokens.

## Install

```bash
npx skills add bcforbd/cavepathy
```

Or copy the skill folder directly:

```bash
cp -r skills/cavepathy /path/to/your/agent/skills/
```

<details>
<summary><strong>Agent-specific setup</strong></summary>

| Agent | Command |
|---|---|
| **Claude Code** | `cp -r skills/cavepathy ~/.claude/skills/` — then use `/cavepathy [lite\|full\|ultra]` |
| **OpenAI Codex** | `cp -r skills/cavepathy ~/.codex/skills/` — auto-invokes on "caveman", "cavepathy", "be concise" |
| **Hermes Agent** | `hermes skills install https://github.com/bcforbd/cavepathy` |
| **OpenClaw** | `cp -r skills/cavepathy ~/.openclaw/workspace/skills/` and `cp SOUL.md ~/.openclaw/workspace/` |
| **Gemini CLI / Kilo Code / OpenCode** | `mkdir -p .agents/skills && cp -r skills/cavepathy .agents/skills/` |
| **Cline** | Copy `cline_mcp_settings.json` into your MCP settings, or add rules to `.clinerules/` |

`AGENTS.md` documents the universal behavioral rules plus how each platform discovers and persists the skill across a session — read it if you're adding support for a new agent.

</details>

## Usage

Cavepathy auto-triggers on brevity requests ("be concise", "talk like caveman", "less tokens") and on real coding work — writing, editing, reviewing, or debugging code, where a silent wrong assumption or a 200-line solution to a 50-line problem is expensive.

```text
/cavepathy            # load with default (full) compression
/cavepathy lite        # lighter compression, sentences stay whole
/cavepathy ultra       # bare fragments, arrows for causality
"normal mode"           # drop compression — Karpathy's 4 principles stay on
```

**Boundary:** caveman-style phrasing applies to conversational replies only. Code, commit messages, and PR descriptions are always written in normal English — they're artifacts other people and tools read later, not a chat reply.

## How it works

A Claude Code skill is just a `SKILL.md` file with YAML frontmatter (`description`, `when_to_use`, `arguments`) plus markdown instructions, discovered automatically from `.claude/skills/<name>/`. Claude reads the frontmatter to decide *when* to load a skill, then loads the full body *once* — it stays in the conversation for the rest of the session rather than reloading every turn. That's why `skills/cavepathy/SKILL.md` is written as standing instructions ("compression level: `$level`, persists until changed") rather than one-time steps, and why it stays short enough that loading it doesn't cost much.

The compression level is a real argument, not a guess: `/cavepathy ultra` passes `ultra` into the skill via the `arguments: [level]` frontmatter field, substituted into the body as `$level`.

## What's inside

`skills/cavepathy/SKILL.md` has two layers:

**Thinking layer** (Karpathy, always on while the skill is active):
1. **Think before coding** — state assumptions, surface tradeoffs, ask when genuinely unclear
2. **Simplicity first** — minimum code for the stated problem, nothing speculative
3. **Surgical changes** — touch only what the task requires; clean up your own orphans, leave everything else alone
4. **Goal-driven execution** — define verification criteria, loop until met, don't just narrate steps

**Output layer** (caveman, three levels):

| Level | Behavior |
|---|---|
| `lite` | Drop filler/hedging. Sentences stay grammatically whole. |
| `full` (default) | Drop articles, filler, pleasantries. Fragments OK. Short synonyms. |
| `ultra` | Bare fragments. Abbreviate prose words only — never real code symbols. Arrows for causality. |

The hard rule sits above both: compression can shorten anything the thinking layer produces, never delete it.

## Repo layout

```text
cavepathy/
├── README.md                 — you are here
├── AGENTS.md                 — universal behavioral rules + per-agent discovery details
├── SOUL.md                   — persona bootstrap consumed by OpenClaw
├── skills.sh.json             — registry metadata consumed by the `npx skills` installer CLI
├── cline_mcp_settings.json    — MCP wiring for Cline
└── skills/
    └── cavepathy/
        └── SKILL.md          — the actual skill: frontmatter + both rulesets merged
```

## Contributing

Issues and PRs welcome — especially new agent integrations (add a row to the table above and to `AGENTS.md`) or sharper wording in `SKILL.md`. Keep edits surgical: this project tries to practice what it preaches.

## Credits

Cavepathy merges two existing MIT-licensed skills faithfully — the four Karpathy principles and three caveman compression levels are reproduced, not reinvented. The hard rule and the worked example showing why the merge beats using either skill alone are this project's own contribution.

- [**caveman**](https://github.com/JuliusBrussee/caveman) by Julius Brussee — output compression
- [**karpathy-guidelines**](https://github.com/multica-ai/andrej-karpathy-skills) (multica-ai) — coding behavior guidelines derived from [Andrej Karpathy's](https://x.com/karpathy) observations on LLM coding pitfalls

## License

MIT
