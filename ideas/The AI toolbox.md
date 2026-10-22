# The AI toolbox

* Building blocks:
  - Structured input and output.
  - Composable.
  - Fluidware or software.
* Recipes:
  - Pre-constructed patterns.
  - E.g.: List of items processed in parallel, or sequentially in a loop.
* The interactive:
  - Orchestrator agent building a pipeline.
  - Temporary and residual artefacts.
  - Context control by the operator.
  - Catalogue of prompts.
* Autonomy
  - Typical memory patterns are fragile. Memory should be part of the project.
  - Skills should not be injected implicitly. Any allowed skill is listed in AGENTS.md.
* Chain of authority:
  - Objectives sit at the top — the reason the project exists.
  - Directives are general and instructional: a strategy for reaching the objectives. They must not contradict the objectives they serve.
  - Rules fill in and specify a directive, covering concrete cases. They must not conflict with each other, because they must not conflict with the directives they spawned from.
  - Skills are aside, not part of the chain: guidelines for *how* to implement a rule, never rules themselves.
  - Rules and directives are not essentially different entities, only a difference of altitude: a directive is a rule for writing rules.
  - Only rules and skills apply directly to a session — load only the ones relevant to the task at hand.
  - Directives describe how to create, maintain, and evolve rules; being general, they are portable across projects.
  - Writing and revising rules is ongoing, essential project work, not a one-off setup step.
  - Every rule should record why it was created. Documenting this practice is itself a directive, so it travels with the rest of them to other projects.
* Recipes and procedures:
  - The industry now uses agentic skills mostly as procedures — step-by-step routines to execute. This drifts from the traditional meaning of a skill: knowing *how* to do something.
  - The framework splits the two notions apart.
  - Recipes are know-how, e.g. "recipe for making an airplane". Close to the traditional meaning of a skill.
    - Loaded on demand, when an agent needs to know how to carry out an instruction it is already working on.
    - Never "run" on their own: a recipe has no meaning without the context of the task that needs it. Running one in a sub-agent makes no sense.
  - Procedures are executable, usually step-by-step instruction lists. Closer to software functions.
    - They may take arguments.
    - They can be run in a sub-agent, because the context they need is passed to them explicitly instead of being inherited.
  - The content of the two can look alike; the difference is in how they are used: a recipe is consulted from inside a context, a procedure is invoked with a context handed to it.

## Next

- Reconcile naming: the "Recipes" building block above (pre-constructed patterns such as parallel or looped processing) is closer to a procedure than to a recipe in the new sense.
- Place recipes and procedures in the chain of authority: recipes look like what is there called skills; procedures are not covered yet.
