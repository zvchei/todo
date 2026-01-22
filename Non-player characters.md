The aim of the experiment is to gather and develop new ideas related to the creation of an intelligent non-human agent
within a virtual environment.

The primary measure of an agents' performance is not their intelligence, but how convincingly realistic their
characters feel. It is acceptable for an agent to make mistakes, as long as these mistakes are not unexpected, given
the context. The agents should demonstrate reasoning skills, biases, and planning abilities. They should coherently
explain their motives when questioned. Faulty reasoning is quite acceptable, given that the agents possess limited
knowledge and inherent biases.

The agents exist in a simplified virtual environment and perceive their surroundings as a stream of events. The events
they personally experience are stored as memories in the form of a narrative. Based on this narrative, the agents
choose their actions by predicting how the narrative will evolve.

The ability to move from place to place presents the agents with the unique challenge of uncovering hidden variables in
their environment. To make accurate predictions, the agents must reconstruct a probable sequence of events leading to
the current state. They need to discern which events observed in their previous locations are relevant to the new one,
and which are not. Another crucial aspect of the agents' reasoning is the ability to assess the importance of the
events they have observed, particularly of the actions they are about to take, as these actions may have long-term
consequences that only appear far into the future.

The agents should be given time to rethink the situation, as the amount of data they directly observe will be limited
by some physical constraints in the simulation. Based on learned patterns, the agents should be able to fill in the
missing pieces. Agents should be allowed to exchange information. Starting with a primitive language, the agents will
eventually develop a complex way of communication.

Additional features may be gradually added to the simulation, making the environment more complex and engaging.

To implement the mechanism behind the agents' behavior, the following must be considered:

* Define Behavioral Goals: There are objectives and expected behaviors of the agents within the virtual environment.
Naturaly the main goal is to survive, but there are other, secondary drives that the agents will be urged to fulfill
such as the need to explore, the need to communicate, the need to experiment and learn, etc. These goals will be
prioritized based on the agent's physical needs and internal clocks, out of their direct control. It could be
interesting to see what happens when the agents' receive the ability to influence these priorities at some point.

* Design Cognitive Framework: Develop a cognitive framework that incorporates reasoning, biases, and planning abilities
for the agents. The reasoning process should not be opaque, and the agents should be able to explain their motives
when questioned. No black-box neural networks.

* Memory and Narrative Integration: Implement a system where agents perceive events as a narrative stored in memory.
This narrative should drive their decision-making process by providing the necessary context for predicting the outcome
of their actions.

* Handling Uncertainty and Complexity: Enable agents to navigate uncertainty by reconstructing probable sequences of
events leading to the current state. Develop mechanisms to prioritize relevant events across different locations and
contexts. As in the human reasoning processes, there shouldn't be gaps in the agents' narative.

* Adaptive Learning and Communication: Facilitate adaptive learning where agents can fill in missing data based on
observed patterns as consequence of exploratory actions. I.e. agents should have the ability to probe or ask for
answers. Begin with a basic communication protocol and allow it to evolve into a complex language over time through
agent interaction.

* Environment Expansion: Gradually introduce additional features into the virtual environment to enhance complexity and
engage the agents more deeply. These features should challenge their cognitive abilities and decision-making processes.
Additionally, introduce new aspects of agents' behavior, such as their ability to form groups for cooperation. Agents
should also possess the capability to process "abstract ideas" represented as narrative events not directly reflected
in the physical environment. Alternatively, they could maintain an inventory of conceptual "objects" that they consider
part of their personal environment. Enable agents to share these abstract objects with others, simulating the
exchange of ideologies or beliefs, akin to religious concepts.

* Iterative Development: Implement the simulation mechanics in iterative way, allowing to refine and adjust based on
experimental outcomes.
