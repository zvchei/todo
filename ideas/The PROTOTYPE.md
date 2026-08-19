# PROTOTYPE.md

A skill using the **PROTOTYPE** pattern declares what functionality it needs and what functionality it offers in abstract terms, without naming concrete tools or skills. Callers and environments bind those needs later — either by [[Solidify|Solidify]] once, or at run time when no solidification skill is available.

The authoring pattern is portable: the same skill can move between environments as long as something satisfies its requirements. A skill that *requires* "store a document by name" and *provides* "save a note" can run against whichever store is present, without rewriting the workflow.

## Skill shape

A prototype `SKILL.md` (or a dedicated `PROTOTYPE.md` when using Solidify) typically has the following sections:

1. **Requires** - abstract capabilities the environment must provide, with enough detail for an agent to match them against an existing provider — a tool, an MCP, another skill.
2. **Provides** - capabilities this skill exposes to other skills or agents to use.
3. **Methods** - a collection of all actions that the skill provides. The methods are described in detail. To comply with the **PROTOTYPE** pattern, they must use abstract references when delegating work to other skills or tools. Example: "store the document with the given name" instead of "call `/store` with the given name" or "save the document in a file with the given name". This allows the skill to run in any environment that provides a matching capability, and be agnostic to the concrete implementation.

### With Solidify

[[Solidify]] copies the prototype to `PROTOTYPE.md`, resolves requirements to concrete bindings once, and rewrites `SKILL.md` so each run skips resolution. Later edits go to the prototype; `SKILL.md` is regenerated.

### Standalone (without Solidify)

The same authoring pattern works without a solidify skill. Resolution then happens at run time, which costs tokens on every invocation unless the project records bindings and reuses them. Add a standing instruction to `AGENTS.md` (or a small companion file it points to) along these lines:

> When a skill that declares abstract **Requires** is invoked, resolve each need to a concrete tool or skill available in this environment and **record the mapping**.
>
> Prepend that mapping to the context prior to each skill invocation, so the agent can use the concrete bindings without re-resolving.

This instruction remains useful when Solidify *is* present: solidify writes bindings into `SKILL.md`; the agent's rule covers the gap before solidification and can be used as a reference by the solidification process.

## Significance

Without a prototype contract, skills hard-code `/specific-skill` or a named MCP. They run where that exact dependency exists, and even if the agent can find a workaround, it comes at additional cost each run. Declaring needs as interfaces keeps the workflow portable and makes both compile-time and run-time resolution possible.
