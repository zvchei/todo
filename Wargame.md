# LLM-Driven Grand Strategy War-Game Concept

This document outlines the core mechanics and features of a new LLM-driven grand strategy war-game, designed for both single-player and multiplayer experiences.

---

## Game Mechanics

### 1. Contextual Setting

The game's setting is highly adaptable and can be:

- **Historically Realistic**: This requires a specified historical date or period (e.g., 1400 AD, or "today"). The game engine will use its internal knowledge of the historical background.

- **Fantasy**: A world with its own unique lore, creatures, and magic. A world history and the current state need to be added or generated to the context.

- **Alternative History**: A deviation from known historical events, exploring "what if" scenarios. This requires a clear point of divergence and an understanding of the resulting changes to the world.

The geographical scope can be limited to a specific territory or encompass the entire world.

### 2. Players

Players control a single person, typically holding significant power or an important position in the world.

### 3. Turn-Based System

The game operates on a turn-based system, with each turn representing a predefined amount of in-game time.

#### Phase 1: Player Interview & Preparation

- **NPC Interaction**: During this phase, players may interview their subordinates, advisors, or other in-game NPCs to inquire about the parameters, probabilities, and potential results of their planned actions.

- **Immediate Actions**: Other immediate or insignificant actions, such as the interrogation of a prisoner or reading news, can be undertaken in this phase. Such actions may have hidden consequences, which the engine will account for.

#### Phase 2: Player Action Input

- **Free Text Actions**: Players specify their desired actions in free-form text.

- **Action Ordering**: Actions can be executed in an order explicitly requested by the player or as reasonably assumed by the game engine.

- **Independent Verification**: The verification of action probabilities and results will be evaluated separately per player input to minimize the possibility of players influencing the LLM's assessment. The evaluation engine should not be aware of other players' actions in this phase.

#### Phase 3: LLM Evaluation & Execution

- **Simultaneous Execution**: All players' actions are executed at the same time.

- **Independent LLM Evaluation**: The LLM independently evaluates the outcome of each action.

- **Action Rejection**: The engine may reject an action if deemed unrealistic, providing a clear reason to the player.

#### Phase 4: Aggregation & Conflict Resolution

- **Conflict Handling**: If there are conflicts, overlapping effects, or paradoxes arising from simultaneous actions, they are resolved during this phase by the engine. The results and ongoing effects are summarized and presented to the players, and the game situation is updated.

### 4. Action Dynamics

- **Variable Success Chance**: All actions have a variable chance of success, which depends on the prevailing situation and the nature of the action itself.

- **Execution Duration**: Actions have a certain duration of execution, after which their effects take place. An action's effects may or may not be significant, and these effects can differ from the immediate result of the action. For instance, probing for oil might be a significant investment regardless of whether oil is found.

- **Consequences**:
  - Each action can have one or more consequences.
  - Some consequences are obvious, while others are hidden and not directly revealed to the players.
  - Some consequences may be hidden from certain players while being visible to others, requiring the engine to evaluate and manage this visibility.
  - Consequences have a certain duration of effect, after which they may disappear or be replaced by follow-up or residual effects.

### 5. Non-Player Character (NPC) Autonomy

- **LLM-Driven Decisions**: Based on the game's setting, the engine will automatically make decisions for a number of NPC characters.

- **Personality and Knowledge**: These decisions will be made from the point of view of those characters, taking into account their unique personalities, knowledge, and in-game circumstances, making the game deeper and more unpredictable.