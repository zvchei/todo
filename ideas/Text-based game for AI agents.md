## Project Core:
* A text-based survival game for AI agents.
* No hard-coded objectives; agents are driven by emergent needs and inherent/encoded biases (e.g., hunger, safety).

## World Model & Structure:
* The world is text-based, divided into discrete "rooms" (locations).
* It's represented as a graph within the engine.
* Locations (Rooms) are graph nodes with textual descriptions and lists of objects.
* Objects have an id, name, description, type (static/movable), a list of traits (capabilities like "openable," "lockable," "consumable"), and an initial_state (current status for its traits, e.g., open: false, locked: true).
* Interactions/Actions are also nodes in the graph, defining the verb, target_object, prerequisites, and consequences.

## Engine's Role & Intelligence (Central Orchestrator):
* Event-Driven Tasking: Reacts to internal agent states (e.g., hunger) or external events, offering agents changes in their primary task (e.g., "look for food").
* Goal & Path Management: Presents agents with high-level quests/goals (e.g., "find food"), then calculates and offers possible routes/paths (sequences of actions from the graph).
* Hierarchical Sub-Goals: Breaks down chosen routes into intermediate goals/sub-quests (e.g., "exit the hut") and can present even lower-level challenges (e.g., "find a key for the door") without providing explicit step-by-step solutions.
* Dynamic Action Presentation: Generates context-specific lists of immediate actions and their valid target_ids directly within the prompt. This list is based on the current location, game_state, and object traits. This avoids "tool explosion" by constraining options in the prompt.
* Validation: Performs post-LLM-call validation of the chosen action against the current game_state and action prerequisites.
* Game State Management: Tracks dynamic elements like agent location, inventory, and the current states/locations of movable objects.

## LLM Agent's Role & Interaction:
* The LLM forms the "mind" of the agent, making decisions based on comprehensive prompt input.
* Prompt Input: Includes narrative description, its personality/biases, core memories, current main/sub-goals, suggested paths, valid independent actions (with target IDs), and current challenges.
* Decision-Making:
  * The LLM uses structured output/tool calling to select actions (e.g., open(target_id='wooden_door_hut')).
  * It decides to accept/reject engine-offered task changes, chooses high-level routes, and selects independent actions.
  * Crucially, agents may autonomously decide to switch goals based on internal events (e.g., looking for food if hungry).
  * Agents are expected to "figure out" solutions for challenges presented by the engine without explicit step-by-step guidance.
* Decision Explanation: Every decision made by the agent's LLM will be accompanied by a reason, which will be logged. This log can later be used to interrogate the agent and provide a narrative of its actions.
* Multi-Step Choice Strategy: To manage complexity, a multi-step approach might be implemented where the agent first focuses on an object, then is presented with actions applicable to that object.

## Undecided Parts:
* Learning and Evolving Behavioral Traits: The exact mechanism for how agents' traits (like risk-taking, aggressiveness, confidence) or their biases dynamically change based on their experiences and the outcomes of their actions.
* Core Memory Formation & Utilization: The precise process by which new "core memories" (e.g., "John is a friend") are created, stored, and how the LLM specifically leverages these memories in its decision-making in future prompts.
* Detailed LLM Problem-Solving for Challenges: While the engine presents challenges, the granular process of how the LLM "figures out" solutions when specific steps aren't explicitly provided by the engine remains to be defined.
* Initial Graph Population Method: How the initial textual world description is specifically translated into the structured graph data (e.g., entirely manual JSON/YAML, a semi-automated parser, etc.).

## Streamlining Agent Choices
A multi-step approach like focusing on an object first and then presenting relevant actions (e.g., "Door" -> then "Open," "Examine," "Break Down") is a needed to combat "choice explosion." This is a common pattern in text adventures and makes sense for limiting the LLM's context window. It might require the agent to make a few more choices to complete an objective, but it offers a more structured interaction. We can definitely experiment to find the right balance for the LLM.

## Multi-Layered Agent Goals and Sub-Quests
The concept of multi-layered goals (main goal, secondary goals, and sub-quests) is required for depth. It allows the engine to guide the agent towards broad objectives like "find food," offering high-level paths (e.g., "go to the orchard"), but then letting the agent tackle the necessary intermediate steps (e.g., "exit the hut") and even granular challenges (e.g., "find a key") on its own.
This hierarchy means the agent's current prompt can always clearly define its immediate focus, even if that focus is just a small step towards a much larger objective. This design will be key to managing complexity and enabling sophisticated problem-solving within the text-based world.
