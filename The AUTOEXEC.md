# Autonomous Startup for Agentic Frameworks

The evolution of agentic frameworks into operating frameworks requires a deterministic entry point for autonomous execution. Named deliberately after the DOS-era **`AUTOEXEC.BAT`** — the file that ran automatically on every system boot — **`AUTOEXEC.md`** applies the same principle to agentic systems. Functioning as an automatic default prompt, the file is ingested by the framework without any user action. The precise trigger is an implementation detail: one framework may ingest it at session start, another at workspace open, another on a boot event. What is invariant is that no human needs to provide it — the agent receives its initial instructions autonomously. This transitions AI tools from reactive, chat-driven assistants into resident, independent, background services.

## Separation of Responsibility

Robust agentic architecture benefits from a clear separation between capabilities and intentions. This proposal recommends building on existing open standards rather than relying on framework-specific mechanisms:

 **`AGENTS.md`** is an emerging open standard and a de facto convention for declaring the capability layer: toolchains, permissions, and agent roles available within a workspace. Any conforming framework can read and act on it without bespoke configuration.

**`SKILL.md`** is an open convention for encapsulating reusable, domain-specific behaviours as composable modules. SKILLs can be invoked directly from **`AUTOEXEC.md`** or referenced through `AGENTS.md`, keeping the startup file concise while delegating complexity to well-defined, portable units.

**`AUTOEXEC.md`** sits above both as the operational layer, consuming their definitions and applying them to specific tasks. This layering is the strongly preferred pattern, though not a strict requirement. The alternative, to encode startup behaviour in proprietary scheduler configs or framework-specific boot hooks, couples the project to a single vendor. Keeping it in natural language files keeps it readable, version-controlled, and portable.

## Implementation and Specification

The specification MUST rely on direct natural language instructions passed into the operating framework agent's context upon startup. The following example shows an autonomous research project defined entirely in these terms:

```markdown
Update the `last_run` field in `state.yaml` with the current timestamp.

Monitor `inbox/papers/` continuously. When a new PDF appears,
run the `literature-review` SKILL on it: extract hypotheses,
flag contradictions with `corpus/established-findings.md`,
and file the result in `inbox/reviewed/`.

Every Monday, run the `synthesis` agent across `inbox/reviewed/`
and update `corpus/open-questions.md` with emerging gaps.

If any paper challenges a finding marked as `consensus: true`,
pause and request human review before continuing.
```

## Execution Patterns

All operational intent should live in natural language instruction and not in framework configuration or scheduler definitions. The instruction is the source of truth; the framework is the runtime that interprets it.

How it executes is an implementation detail. A framework may delegate tasks to agents, reasoning through the instructions at runtime. Alternatively, it may compile **`AUTOEXEC.md`** entries, `AGENTS.md` roles, and `SKILL.md` modules into deterministic scripts or cron jobs ahead of time, falling back to model inference only where static compilation falls short. The latter trades flexibility for efficiency, significantly reducing the token cost of long-running tasks.

The **`AUTOEXEC.md`** specification prescribes the interface, not the execution strategy. Anchoring intent in natural language keeps projects portable across runtimes.

## Directory Scoping

An **`AUTOEXEC.md`** file governs the directory it lives in and all of its subdirectories. If a subdirectory contains its own **`AUTOEXEC.md`,** that file takes precedence within its scope, overriding the parent. A child **`AUTOEXEC.md`** may still reference and invoke instructions from its parent (or any other instruction file) explicitly.

## Next

- **Conflict resolution** — what happens when two tasks compete for the same resource or issue contradictory instructions? Needs a model for priority, ordering, or mutual exclusion.
- **Security surface** — an auto-executed file that runs arbitrary agent actions on boot is a meaningful attack vector (repo poisoning, supply-chain compromise). Should the spec require signing, sandboxing, or explicit human approval on first run?
- **Failure semantics** — what does conforming behaviour look like when an instruction fails or errors? If the spec explicitly addresses failure (e.g. "retry three times, then escalate"), the framework must follow it. Otherwise, the framework may interpret it: silent skip, retry with back-off, escalation to a human, or structured error logging. Related: if a framework restarts mid-task, should it re-execute completed steps or resume?
- **Idempotency** — recurring tasks (e.g. a weekly synthesis run) may execute more than once against the same inputs due to retries, restarts, or scheduling overlaps. The spec does not yet define whether instructions must be written to be idempotent, whether the framework is responsible for deduplication, or whether this is left entirely to the author.
- **Trigger mechanism** — should scheduling, file-watching, and event-handling capabilities be defined as a set of portable, well-specified SKILLs, as a mandatory standard toolset every conforming framework must provide, or both? The answer determines how portable trigger definitions are across runtimes.
- **Dry-run and validation** — how does an author verify that an **`AUTOEXEC.md`** is correctly formed and will behave as intended before deploying it to a live agent? A conforming framework might expose a `--dry-run` or `--validate` mode.
- **Versioning and migration** — how do running agents handle changes to instructions mid-flight? Should there be a protocol for graceful reload, or is a full restart expected?
