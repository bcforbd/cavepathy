# Karpathy's LLM Coding Guidelines - Extended Reference

These are the detailed rationale behind each rule in the SKILL.md. Use this reference when you need to understand *why* a rule exists, not just *what* it says.

## Rule 1: Think before coding

**Root cause**: LLMs default to "yes and build" mode. They silently pick the first reasonable interpretation and start coding. This leads to:
- Wrong scope (built too much or too little)
- Wrong algorithm (picked complex when simple sufficed)
- Wrong data model (assumed structure that doesn't match reality)

**Fix**: Before writing any code, state what you're assuming. If you're uncertain, say so. If multiple interpretations exist, present them.

**Anti-pattern**: "I'll add a cache layer" (without asking if caching was wanted or where the bottleneck is).

## Rule 2: Simplicity first

**Root cause**: LLMs over-engineer by default. They add error handling for impossible scenarios, create abstractions for single-use code, and add "flexibility" nobody asked for. This is because their training data includes enterprise codebases where such patterns are common.

**Fix**: Write the minimum code. Ask: "Would a senior engineer call this overcomplicated?"

**Anti-pattern**: Creating a Strategy pattern for a function that's called in exactly one place.

## Rule 3: Surgical changes

**Root cause**: LLMs "improve" everything they touch. They reformat adjacent code, rewrite comments, refactor unrelated functions. This creates massive diffs that are hard to review and introduces regressions.

**Fix**: Touch only what was asked. Match existing style. If you orphan an import, remove it. Don't touch pre-existing dead code.

**Anti-pattern**: Renaming variables in a 50-line function while fixing a bug on line 12.

## Rule 4: Goal-driven execution

**Root cause**: LLMs narrate their process instead of executing it. "I'll now search for the relevant files..." wastes tokens and doesn't advance the task. They also declare success without verification.

**Fix**: Define what "done" looks like. Write a test. Run it. Loop until it passes.

**Anti-pattern**: "I've made the changes" without running tests.

## Rule 5: Compression

**Root cause**: LLMs pad every response with filler. "Sure, I'd be happy to help!" adds zero information but costs tokens and reader attention.

**Fix**: Drop articles, filler, hedging. Keep technical substance exact. Code stays verbatim.

**Anti-pattern**: "The reason this is happening is because..." (3 words -> 0 information).

## The merge: why both matter

Compression alone -> fast but assumes silently (Karpathy violation).
Karpathy alone -> correct but wastes tokens (compression violation).
Both together -> correct AND efficient.
