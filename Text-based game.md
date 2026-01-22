= Experimental text-based game

== Concept

A text-based game, similar to a traditional MUD, although its spatial building blocks are not rooms, but roughly defined
areas that represent locations, streets, and other places. Objects in the locations have no definite position, but are
relative to one another. Moving from one location to another takes specific time. Moving from one object to another too.
Locations are "connected" with entrances that behave as objects within the location. Apart from that, in the game are
various objects that can be picked up, dropped, and used. The game is played by typing commands in a natural langage.

All object are impermanent, and can be destroyed, or created, given the player has time, skills, and resources to do so.

The NPCs are controlled by a simple AI, based of another _idea_ about an AI imitating the behavior of living souls, and 
express learning, personal preferences, emotional reactions, and other behaviors.

Alternative, an NPC is controlled by an LLM, that makes decisions about the available actions, based on the context.
For example, by prompting the LLM to choose from a list of actions, by giving a description of the state of the NPC, 
its memories, relevant to the current situation, etc.

The core of the game is a state encoded in an approproate data structure. The state is modified by the player's actions,
and by the NPCs' actions, that are constrained by a state machine that defines the possible actions, and their effects.

The player prompts the game to perform actions. The intent of the action is extracted with the help of NLP techniques,
and given to the state machine for evaluation. The state machine responds with the new game state, or with an error 
response that indicates the reason for the failure of the action.

The new game state is then transformed to a text, and presented to the player. Various techniques are used for the task,
generally, the state is converted to text using NLP generative methods, and then passed trough an LLM for enchancement.

== Implementation

=== Game sate

The game state is represented as a graph, where the nodes are objects, and the edges are relations between the objects.
The relations are complex, and can be of various types. One type of relation is a _distance_ relation, that defines the
amount of time it takes to move from one object to another and in what manner.

Note: Location in the game a defined as a set of objects, and not as a single object, so to better represent them in 
the game state, a special type of relation is used: _belongs_to_.

To facilitate this mechanism a graph database is employed.

=== Game engine

The game engine is a state machine that takes the current game state, and the player's action, and returns a new game
state, or an error message. A number of heuristics are used to prune the parts of game state graph that is not relevant 
to the current action.

The game engine calculates the difference between the current state and the future state, given the action, and 
determines if, based on the presented resources, that transition is possible.

=== Game presentation

The game presentation is a text generator that takes the game state, and produces a text that describes the state in a
natural language. The text generator is based on NLP techniques, and uses an LLM to enhance the result. The LLM is 
carefuly grounded to avoid the generation of text that does not represent the game state. An NLP post-processing is
used to determine if the LLM hasn't added or removed objects from the game, for example.

=== User input

The player enters their intent in a natural language. NLP techniques are used to extract the intent.
