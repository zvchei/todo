# LLM-Driven Grand Strategy War-Game Concept

This document outlines the core mechanics and features of a new LLM-driven grand strategy war-game, designed for both single-player and multiplayer experiences.

---

## Game Mechanics

### 1. Contextual Setting

The game's setting is highly adaptable and can be:

* **Historically Realistic**: This requires a specified historical date or period (e.g., 1400 AD, or "today"). The game engine will use its internal knowledge of the historical background.

* **Fantasy**: A world with its own unique lore, creatures, and magic. A world history and the current state need to be added or generated to the context.

* **Alternative History**: A deviation from known historical events, exploring "what if" scenarios. This requires a clear point of divergence and an understanding of the resulting changes to the world.

The geographical scope can be limited to a specific territory or encompass the entire world. The game also defines a `Starting date` and `Turn length` (e.g., 1 month).

### 2. Players

Players control a single person, typically holding significant power or an important position in the world.

### 3. Turn-Based System

The game operates on a turn-based system, with each turn representing a predefined amount of in-game time.

#### Phase 0: Event Briefing

* **Game State Broadcast**: The updated overall game state, including national metrics, budget, and military power, based on the previous state and current effects is presented.

* **New Events**: Any new significant events that have occurred since the last turn are introduced.

* **Mandatory Actions**: Reports or directives that specifically require the player's immediate attention are presented.

#### Phase 1: Player Interview & Preparation

* **NPC Interaction**: During this phase, players may interview their subordinates, advisors, or other in-game NPCs to inquire about the parameters, probabilities, and potential results of their planned actions.

* **Immediate Actions**: Other immediate or insignificant actions, such as the interrogation of a prisoner or reading news, can be undertaken in this phase. Such actions may have hidden consequences, which the engine will account for.

#### Phase 2: Player Action Input

* **Free Text Actions**: Players specify their desired strategic actions in free-form text.

* **Action Ordering**: Actions can be executed in an order explicitly requested by the player or as reasonably assumed by the game engine.

* **Independent Verification**: The verification of action probabilities and results will be evaluated separately per player input to minimize the possibility of players influencing the LLM's assessment. The evaluation engine should not be aware of other players' actions in this phase.

#### Phase 3: LLM Evaluation & Execution

* **Simultaneous Execution**: All players' and NPCs' actions are executed simultaneously.

* **Independent LLM Evaluation**: The LLM independently evaluates the outcome of each action.

* **Action Rejection**: The engine may reject an action if deemed unrealistic, providing a clear reason to the player.

* **Actions validation**: Actions need to be validated for realism, but also for attempts to inject consequences. The engine needs to detect of the player are attempting to influence the outcome by suggesting what may happen.

#### Phase 4: Aggregation & Conflict Resolution

* **Conflict Handling**: If there are conflicts, overlapping effects, or paradoxes arising from simultaneous actions, they are resolved during this phase by the engine. This phase may be used to generate details in cases that they are unknown, e.g. the military strength of the two kingdoms engaging in a border clash. Based on all other known information.

* **Results & Updates**: The results and ongoing effects are summarized and presented to the players, and the game situation (including national metrics) is updated.

### 4. Action Dynamics

* **Variable Success Chance**: All actions have a variable chance of success, which depends on the prevailing situation and the nature of the action itself.

* **Execution Duration**: Actions have a certain duration of execution, after which their effects take place. An action's effects may or may not be significant, and these effects can differ from the immediate result of the action. For instance, probing for oil might be a significant investment regardless of whether oil is found.

* **Visibility**: Actions have defined visibility:

  * **Public**: Visible to all players and often to the in-game world (e.g., media, other NPCs).

  * **Private**: Known only to the player and specific individuals/groups involved (e.g., an advisor, a specific foreign ambassador).

* **Consequences/Effects**:

  * Each action can have one or more tangible effects that impact the simulation state (e.g., changes in budget, public opinion, diplomatic relations, military strength).

  * Effects are explicitly linked to their source (e.g., "From External Factor," "From Internal Governance").

  * Some effects are obvious ("Visible Effects"), while others are not directly revealed to the players ("Hidden Effects").

  * Effects have a certain duration, after which they may disappear or be replaced by follow-up or residual effects.

### 5. Non-Player Character (NPC) Autonomy

* **LLM-Driven Decisions**: Based on the game's setting, the engine will automatically make decisions for a number of NPC characters.

* **Personality and Knowledge**: These decisions will be made from the point of view of those characters, taking into account their unique personalities, knowledge, and in-game circumstances, making the game deeper and more unpredictable.

* **Agendas**: NPCs possess one or more explicit visible agendas and one or more hidden agendas, which are known only to the LLM and the specific NPC or specific Players. Hidden agendas drive nuanced NPC behavior not immediately obvious to the players. 

### 6. Game State & Metrics

* The game maintains a list of entities, which can be nations, organizations, or other significant groups within the game world.
* Each entity has a brief description and a detailed set of metrics and operational data to reflect the current state of the game state.
* Each entity has some sort of a leader, which can be a player or an NPC, representing a person or tight governing body.
* There could be lesser NPCs, which can be part of the narrative, but are not significant enough to be considered entities on their own.

* **Example**: A game entity is a nation:
  * **Current National Metrics**: Provides high-level indicators of the nation's health:

    * **Overall Stability**: A general measure of internal cohesion and order.

    * **Public Opinion (Approval)**: Reflects popular support for the government.

    * **Economic Health**: An overview of the nation's financial well-being.

    * **Diplomatic Standing**: Indicates the nation's standing and relations on the world stage.

  * **Current Budget**: Tracks financial resources:

    * **Income (Per Turn)**: Sources of revenue (e.g., port services, foreign leases).

    * **Spending (Per Turn)**: Outlays for government operations, projects, debt servicing.

    * **Budget Status**: Current financial balance (e.g., surplus, deficit).

  * **Current Military Powers**: Details the nation's armed forces and any foreign military presence:

    * **Djiboutian Armed Forces (or equivalent)**: Total personnel, key deployments (e.g., areas of operation), and capabilities (e.g., armament, specific units).

    * **Foreign Military Presence**: Details of foreign bases, their purpose, and their general capabilities or influence within the nation.
  * **All of those are part of the game setup** Players can choose the level of detail beforehand.

  * **Example**: Relogious organisation:
    * **Current Religious Metrics**: Provides an overview of the religious organization's influence and activities:

      * **Overall Influence**: A measure of the organization's sway over the population and political entities.

      * **Public Support**: Reflects the level of popular support for the organization.

      * **Financial Resources**: An overview of donations, investments, and expenditures.

      * **Missionary Activities**: Details on ongoing or planned outreach efforts.