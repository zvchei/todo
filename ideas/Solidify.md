# Solidify

Skills are typically written against concrete dependencies, for example, "invoke the X MCP" or "use the `Y` skill". This binds the skill to specific components, like writing code against a specific implementation rather than an interface: the skill can only run where that exact dependency exists, under that exact name, and every caller must know the specific name to invoke it.

Modern agentic systems can recognise and resolve dependencies at run time, but this is not always guaranteed, and this process consumes additional processing steps and tokens. Additionally, this extra processing cost is incurred on every invocation, even if the dependency is always the same.

**Solidify** rewrites a skill to resolve dependencies, reduce ambiguity, and convert deterministic logic into code. Skills written for solidification follow the [[The PROTOTYPE|PROTOTYPE.md]] pattern.

**Solidify** allows a skill to be written against an abstract need instead, for example, "a way to store data by name". This is analogous to dependency injection: the need is a declared dependency, satisfied by whatever concrete implementation is available in the environment where the skill runs. The prototype skill is abstract and unbound, so the same skill can be solidified in different environments against concrete dependencies that satisfy its requirements. Solidifying fixes the binding once and writes it directly into `SKILL.md` — similar to compile-time DI, as in Dagger or Wire, rather than relying on a container to resolve dependencies on every run.

**Solidify** also compiles deterministic logic into code: each prose instruction that an agent re-reads and re-evaluates on every run consumes processing steps and tokens. Converting those parts of the skill reduces this cost significantly. How logic is compiled to code can also be adapted according to the runtime environment and the user's preferences.

## A skill that functions before it is solidified

Every skill starts as a standard `SKILL.md` file; no additional authoring or setup is required. The difference from a typical skill is that it defines its requirements as abstract needs, rather than specific concrete names, and states this explicitly in the same file:

> When a customer reports a bug, from the provided `text report`, extract a `title` and a summary `description`, open a tracked ticket, then post the result reference link in the team's shared channel so the on-call engineer is notified.
>
> ## Requirements
> This skill needs the following capabilities to be available in the environment where it runs:
> * **open a ticket** — opens a tracked work item from a title and description, and returns a reference to it;
> * **notify channel** — posts a short message to a shared team channel;
>
> ## Capabilities
> This skill provides the following capabilities to other skills:
> * **report bug** — files a bug report, from a `text report`;

An agent reading the file can determine the correct binding at run time in most cases, even before **Solidify** processes it. "Requirements" and "Capabilities" do not use a fixed vocabulary or a rigid format. For example, "opens an issue in a tracker" can satisfy "open a tracked work item". Solidify may use semantic matching for this initial resolution, but it records the selected implementation explicitly once the skill has been solidified.

## Solidifying: resolving the interface to an implementation, once

The first time **Solidify** processes a skill, it creates a copy of the existing `SKILL.md` as `PROTOTYPE.md`, but only if `PROTOTYPE.md` does not already exist. The existence of `PROTOTYPE.md` can therefore serve as a clue that the skill has already been solidified and that the original instructions are stored in that file. **Solidify** then rewrites `SKILL.md` in place: `open a ticket` and `notify channel` are replaced with the specific bindings that were resolved, based on what is installed in the environment. Determining that another skill's capabilities satisfy this skill's needs requires judgement. With **Solidify**, this determination is made once, not on every run. A solidified `SKILL.md` does not need to have any requirements listed unless it is only partially solidified. The bindings are now fixed, and the skill can be invoked without any further resolution.

An example result of solidification:

> When a customer reports a bug, from the provided `text report`, extract a `title` and summary `description`, invoke /bug-tracker-log-issue with the `title` and `description`, then invoke /team-channel-post with the result reference link so the on-call engineer is notified.

In this case **Solidify** detected that the environment provides a skill named `/bug-tracker-log-issue` that satisfies the need for `open a ticket`, and a skill named `/team-channel-post` that satisfies the need for `notify channel`. The bindings are now fixed in `SKILL.md`, and the skill can be invoked without any further resolution.

Note: In cases where the environment does not provide a concrete implementation for a need, **Solidify** will leave the original abstract reference in place and warn the user that the skill is only partially solidified. Runtime resolution may still allow it to function, but execution is not guaranteed until the missing dependency is satisfied.

`PROTOTYPE.md` remains the file to edit going forward. `SKILL.md` is generated from it and should not be edited directly; changes should be made to the prototype, followed by re-solidification.

## Compiling deterministic logic into code

Dependency solidification and deterministic compilation are the two core functions of Solidify. Solidify must identify deterministic logic and compile it into a reusable code library.

This is done by analysing the skill's instructions and identifying atomic instruction sets that can be expressed as a single function in code. An atomic instruction set is one that can be executed without ambiguity or interpretation at run time and has a single responsibility. The test question is "can this set of instructions be expressed as an independent function in an appropriate implementation language?"

The best candidates describe computation, I/O operations, and data processing that are repeated, expensive, error-prone, or difficult to test reliably in prose. The implementation language should be selected from the environment's existing capabilities and the user's preferences.

### Example `PROTOTYPE.md`

> Given a text string, compute its SHA-256 cryptographic hash and return the hexadecimal representation. Check the database to see if the hash already exists. If it does, tell the user that the string is a duplicate. If it does not, store the hash in the database and return a success message.
>
> ## Requirements
> This skill needs the following capabilities to be available in the environment where it runs:
> * **database** — provides a key-value store with `get` and `put` operations;

### Solidified `SKILL.md`:

> Given a text string, run `/compute-sha256.py "<text>"`. Invoke /key-value-store-get with the hash. If it returns a value, tell the user that the string is a duplicate. If it does not, invoke /key-value-store-put with the hash and return a success message.

### Compiled code in `/compute-sha256.py`:

```python
import hashlib
import sys

def compute_sha256(text):
    hash_object = hashlib.sha256(text.encode('utf-8'))
    print(hash_object.hexdigest())

if __name__ == "__main__":
    compute_sha256(sys.argv[1])
```

In this example, **Solidify** reuses the environment's standard-library SHA-256 implementation to build a small script. The skill's instructions are rewritten to process the non-deterministic operation of extracting the user's intent and then invoke the deterministic operation of computing the hash, passing the result to an external (not yet solidified) skill.

## Light solidification

The simplest form of solidification is to resolve the skill's requirements to concrete implementations and rewrite the instructions accordingly. This is the minimum that **Solidify** must do to produce a solidified skill. The result is a `SKILL.md` file that can be invoked without any further resolution, along with a `PROTOTYPE.md` file that can be edited (followed by re-solidification) to change the skill's behaviour.

## Deep solidification: compiling the whole skill ecosystem

When a skill contains deterministic logic, **Solidify** can compile that logic into code and generate a library of reusable functions. If the dependencies are also solidified, there is no need to invoke them as skills; the compiled functions can be invoked directly, providing a significant performance improvement.

The only overhead is in the non-deterministic parts of the user-facing skill.

## Full solidification: compiling into a single executable

Instead of wrapping the compiled functions in an orchestrating skill, the solidification process may also compile the entire skill into a single executable that can be invoked directly. In this case, `SKILL.md` is a minimal wrapper that invokes the compiled instructions and provides an interface for the agentic system. Those parts of the prototype skill that are not deterministic could be transpiled into functions that call the agentic system in non-interactive mode. **Solidify** must generate the resulting code so that the user can choose the agentic system when calling the skill as a standalone executable. The selected agentic system and its invocation command should be stored in the generated configuration. When the solidified skill is invoked from another skill, that caller should pass the configured command explicitly rather than relying on automatic selection.

### Capabilities needed for automation

* **Non-interactive execution** — Running prompts programmatically without spawning a UI or awaiting human approval.
* **Bare mode (context isolation)** — Isolating the session from default workspace rules, historical memory, or auto-discovered configurations when needed.
* **Directory injection** — Designating a specific path to load targeted agent skills or plugins for a single run.
* **Format enforcement** — Mandating the CLI output structure (plain text or structured JSON) for downstream script consumption.

### Headless automation flags

* **Claude Code** (claude): -p (non-interactive), --bare (context isolation), --add-dir (skill injection), --output-format (format enforcement).

* **GitHub Copilot CLI** (copilot): -p combined with --no-ask-user (non-interactive), --no-custom-instructions (isolation), --add-dir=PATH (injection), --output-format=FORMAT (formatting).

* **Cursor CLI** (agent): -p (non-interactive), --output-format (formatting). Native flags for context isolation and directory injection are unsupported.

## Solidification principles

* The code style must be flat, functional, and modular, with a clear separation of concerns.
* Individual functions should be small, focused, and reusable and, in most cases, exported for use by other skills.
* The generated components and functions should be documented, with a description based on the original prose instructions used to generate them.
* The solidification should produce skills following the [Agent Skills](https://agentskills.io) style guide — use the 'scripts/' sub-directory for code, 'assets/' for static resources, 'references/' for configuration and documentation.
* **Solidify** itself should keep its own artefacts in its relative sub-directories.
* A manifest file should be kept in **Solidify**'s 'references/' sub-directory to describe the compiled functions — for quick reference to their purpose, inputs, and outputs — to facilitate their use by other solidified skills. **Solidify** should refer to the manifest file in all steps of the solidification process.
* Generated code must be unit-testable, and unit tests should be generated automatically for each function. External capabilities and agentic systems must be replaced with test doubles in unit tests.
  - Functions with side effects should be tested in isolation and with special care to avoid unintended consequences.
* When deep solidification is applied, an end-to-end test should be generated for the entire skill, using isolated test doubles or a sandbox for external capabilities, to verify that the solidified skill behaves as expected. The same security and safety considerations should be applied when testing the skill.
* A refinement process could be applied periodically and after solidification to the generated code to identify and merge similar functions, reducing duplication and improving maintainability. The refinement process should be applied iteratively, with each iteration producing a new version of the skill that is more efficient and easier to maintain.

## Notes and unprocessed ideas

* The solidification skill can extract sub-skills from a prototype. The solidified skill can then invoke them in background agents in clean contexts, avoiding redundant re-evaluation of a large context.

* Pure `PROTOTYPE.md` skills can be used for interactive solidification, where the user is prompted to select a preferred implementation variant.

* **Solidify** can solidify itself. Implemented as a `PROTOTYPE.md` skill, it asks the user to select a preferred execution environment for the solidified skills from the available options in the system:

  `"What is your preferred execution environment for solidified skills: Python, Bash, or C#?"`.

  The user selects one of the options, and **Solidify** compiles itself in a variant that solidifies the skills accordingly.

* Code libraries: The solidification process will attempt to compile deterministic logic into reusable function libraries. Then skills that depend on other solidified skills should invoke the compiled functions directly, rather than indirectly through skill invocation.

* To avoid regression, the re-solidification process must consider downstream dependencies of the updated skill and eventually warn the user if unresolvable breaking changes are introduced.

* For skill prototypes that directly invoke existing scripts, commands, or binaries, the solidification process can "hardcode" the invocation of those commands into the solidified skill, ensuring deterministic behaviour and reducing the need for runtime resolution. In many cases, this can significantly improve performance and reliability as agents tend to analyse the system's capabilities on each run, wasting a significant amount of time and tokens.

* **Solidify** could check the skills for technical errors, e.g. mistaken or incomplete file paths, wrong command arguments, etc. Those are typically fixed by the agent at runtime, but at the cost of using extra tokens.

* **Solidify** could inject instructions limiting the list of allowed skills or tools to be invoked, preventing the skill from doing unnecessary steps and checks.

* Fixable inconsistency between the required and provided capabilities during solidification can be resolved automatically or interactively by choosing appropriate missing argument values or trade-offs. For example, a skill that requires a `filename` argument to store data is a dependency of a skill that does not provide it. **Solidify** can make an educated guess about the appropriate value for `filename` or ask the user if the case is too ambiguous.

* **Solidify** could detect extractable skills that could be independent, solidified, and reusable.

* Solidification can extract a list of all safe commands, scripts, and operations of a skill so they can easily be added to the 'Allowed' list of the agentic framework, reducing the need for user attention and intervention.
