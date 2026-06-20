# Cavepathy

Terse, accurate coding replies. Zero filler. Zero silent assumptions.

Merges [caveman](https://github.com/JuliusBrussee/caveman) output compression with [Karpathy's guidelines](https://github.com/multica-ai/andrej-karpathy-skills) for LLM coding.

## Install

```bash
npx skills add cavepathy/cavepathy
```

Or manually copy `skills/cavepathy/` to your agent's skill directory.

## Agent-specific setup

### Claude Code

```bash
cp -r skills/cavepathy ~/.claude/skills/
```

Then invoke with `/cavepathy [lite|full|ultra]`.

### OpenAI Codex

```bash
cp -r skills/cavepathy ~/.codex/skills/
```

Auto-invokes on triggers: "caveman", "cavepathy", "be concise".

### Hermes Agent

```bash
hermes skills install https://github.com/cavepathy/cavepathy
```

### OpenClaw

```bash
cp -r skills/cavepathy ~/.openclaw/workspace/skills/
cp SOUL.md ~/.openclaw/workspace/
```

### Gemini CLI / Kilo Code / OpenCode

```bash
mkdir -p .agents/skills
cp -r skills/cavepathy .agents/skills/
```

### Cline

Copy `cline_mcp_settings.json` to your MCP settings, or add rules to `.clinerules/`.

## What it does

1. **Surface assumptions** - never silently pick an approach
2. **Minimum code** - no speculative features or abstractions
3. **Surgical edits** - touch only what's asked
4. **Verify before claiming done** - write tests, run them, loop
5. **Compress output** - no filler, no hedging, no narration

## Compression levels

| Level | Behavior |
|---|---|
| `lite` | Drop filler/hedging. Sentences stay whole. |
| `full` (default) | Drop articles, filler, pleasantries. Fragments OK. |
| `ultra` | Bare fragments. Abbreviate prose. Arrows for causality. |

## Before / after

> **Before:** "The reason your React component is re-rendering is likely because you're creating a new object reference on each render cycle. When you pass an inline object as a prop, React's shallow comparison sees it as a different object every time, which triggers a re-render. I'd recommend using useMemo to memoize the object."

> **After:** "New object ref each render. Inline object prop -> new ref -> re-render. Wrap in `useMemo`."

## License

MIT
