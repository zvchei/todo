# Solidify

Skills are typically written against concrete dependencies, for example "invoke the X MCP" or
"use the `Y` skill". This binds the skill to specific components, equivalent
to writing code against a specific implementation rather than an interface: the skill can only run
where that exact dependency exists, under that exact name, and every caller must know the specific
name to invoke it.

Modern agentic systems can recognize and resolve dependencies at run time, but this is not always
guaranteed, and it will consume additional processing steps and tokens. Additionally, this extra
processing cost is incurred on every invocation, even if the dependency is always the same.

**Solidify** rewrites a skill in order to resolve dependencies, reduce ambiguity, and convert
deterministic logic into code. Skills written for solidification follow the
[[The PROTOTYPE|PROTOTYPE.md]] pattern.

**Solidify** allows a skill to be written against an abstract need instead, for example "a way to
store data by name". This is analogous to dependency injection: the need is a declared dependency,
satisfied by whatever concrete implementation is available in the environment where the skill runs.
The prototype skill is abstract and unbound, so the same skill can be solidified in
different environments against concrete dependencies that satisfy its requirements.
Solidifying fixes the binding once and writes it directly into `SKILL.md` — similar to compile-time
DI, as in Dagger or Wire, rather than to a container resolving dependencies on every run.

**Solidify** also compiles deterministic logic into code: each prose instruction that an agent
re-reads and re-evaluates on every run consumes processing steps and tokens. Converting those parts
of the skill reduces this cost significantly. How logic is compiled to code can also be adapted
according to the runtime environment and the user's preferences.

## A skill that functions before it is solidified

Every skill starts as a standard `SKILL.md` file; no additional authoring or setup is required.
The difference from a typical skill is that it defines its requirements as abstract needs, rather
than by specific, concrete names, and states this explicitly in the same file:

> When a customer reports a bug, from the provided `text report`, extract a `title` and summary
> `description`, open a tracked ticket, then post the result reference link in the team's shared
> channel so the on-call engineer is notified.
>
> ## Requirements
> This skill needs the following capabilities to be available in the environment where it runs:
> * **open a ticket** — opens a tracked work item from a title and description, and returns a
>   reference to it;
> * **notify channel** — posts a short message to a shared team channel;
>
> ## Capabilities
> This skill provides the following capabilities to other skills:
> * **report bug** — files a bug report, given a `text report`;

An agent reading the file can determine the correct binding at run time in most cases, even before
**Solidify** processes it. "Requirements" and "Capabilities" do not use a fixed vocabulary or a
rigid format. For example, "opens an issue in a tracker" can satisfy "open a tracked work item".

## Solidifying: resolving the interface to an implementation, once

The first time **Solidify** processes a skill, it copies the existing `SKILL.md` to
`PROTOTYPE.md`. **Solidify** then rewrites `SKILL.md` in place: `open a ticket` and
`notify channel` are replaced with the specific bindings that were resolved, based on what is
installed in the environment. Determining that another skill's capabilities satisfy this skill's
needs requires judgement. With **Solidify** this determination is made once, and not on every run.

One example result of solidification:

> When a customer reports a bug, from the provided `text report`, extract a `title` and summary
> `description`, invoke /bug-tracker-log-issue with the `title` and `description`, then invoke
> /team-channel-post with the result reference link so the on-call engineer is notified.

In this case **Solidify** detected that the environment provides a skill named
`/bug-tracker-log-issue` that satisfies the need for `open a ticket`, and a skill named
`/team-channel-post` that satisfies the need for `notify channel`. The bindings are now fixed in
`SKILL.md`, and the skill can be invoked without any further resolution.

Note: In cases where the environment does not provide a concrete implementation for a need,
**Solidify** will leave the original abstract reference in place, and warn the user that the skill
cannot be fully solidified and may not function correctly until the missing dependency is
satisfied.

`PROTOTYPE.md` remains the file to edit going forward. `SKILL.md` is generated from it and should
not be edited directly; changes should be made to the prototype, followed by re-solidifying.

## Compiling deterministic logic into code

The second part of the solidification process is to convert deterministic logic into code. This is
done by analyzing the skill's instructions and identifying the atomic parts that can be expressed
as code. An atomic instruction set is one that can be executed without any ambiguity or need for
interpretation at run time and has a single responsibility. The test question is "can this set of
instructions be expressed as an independent function in the target programming language?"

The best candidate instructions are those that describe computation, IO operations, and data
processing. The instructions for which the solidification process provides the most benefit are
those that operate on large amounts of data in sequential and parallel fashion.

### Example `PROTOTYPE.md`:

> Given a text string, compute its SHA-256 cryptographic hash and return the hexadecimal
> representation. Check the database to see if the hash already exists. If it does, tell the user
> that the string is a duplicate. If it does not, store the hash in the database and return a
> success message.
>
> ## Requirements
> This skill needs the following capabilities to be available in the environment where it runs:
> * **database** — provides a key-value store with `get` and `put` operations;

### Solidified `SKILL.md`:

> Given a text string, run `/compute-sha256.py "<text>"`. Invoke /key-value-store-get with the hash. If it
> returns a value, tell the user that the string is a duplicate. If it does not, invoke
> /key-value-store-put with the hash and return a success message.

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

## Notes and unprocessed ideas

* The solidification skill can extract sub-skills from a prototype. The solidified skill can then
  invoke them in background agents in clean contexts, avoiding redundant re-evaluation of a large
  context.

* Pure `PROTOTYPE.md` skills can be used for interactive solidification, where the user is
  prompted to select a preferred implementation variant.

* **Solidify** can solidify itself. Implemented as a `PROTOTYPE.md` skill, it asks the user to
  select a preferred execution environment for the solidified skills from the available options in
  the system:

  `"What is your preferred execution environment for solidified skills? Python, Bash, C#;"`.

  The user selects one of the options, and **Solidify** compiles itself in a variant that
  solidifies the skills accordingly.

* Code libraries: The solidification process will attempt to compile deterministic logic into
  reusable function libraries. Then skills that depend on other solidified skills should invoke
  the compiled functions directly, rather than indirectly through skill invocation.

* To avoid regression, the re-solidification process must consider downstream dependencies of
  the updated skill and eventually warn the user if unresolvable breaking changes are introduced.

* For skill prototypes that directly invoke existing scripts, commands, or binaries, the
  solidification process can "hardcode" the invocation of those commands into the solidified
  skill, ensuring deterministic behavior and reducing the need for runtime resolution. In many
  cases, this can significantly improve performance and reliability as agents tend to analyze the
  system's capabilities on each run, wasting a significant amount of time and tokens.

* **Solidify** could check the skills for technical errors, e.g. mistaken or incomplete file
  paths, wrong command arguments, etc. Those are typically fixed by the agent at runtime,
  but at the cost of using extra tokens.

* **Solidify** could inject instructions limiting the list of allowed skills or tools to be invoked,
  preventing the skill from doing unnecessary steps and checks.

* Fixable inconsistency between the required and provided capabilities during solidification can be
  resolved automatically or interactively by choosing appropriate missing argument values or
  trade-offs. For example, a skill that requires a `filename` argument to store data is a
  dependency of a skill that does not provide it. **Solidify** can make an educated guess about the
  appropriate value for `filename` or ask the user if the case is too ambiguous.

* **Solidify** could detect extractable skills that could be independent, solidified, and reusable.

* Solidification can extract a list of all safe commands, scripts, operations of a skill so they
  can easily be added to the 'Allowed' list of the agentic framework, reducing the need for user
  attention and intervention.
