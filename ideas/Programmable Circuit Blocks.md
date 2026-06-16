# Programmable Circuit Blocks

A physical kit of universal, programmable electronic blocks that snap or connect together to simulate circuits, logic networks, signal graphs, or any message-passing system — paired with a digital companion for programming and live visualization.

---

## Concept

Each block is a small microcontroller with flash memory and uniform connectors on all sides. Blocks are behaviorally inert until programmed. A dedicated **programmer** device (a cradle or dock) loads a behavior onto a block: it could simulate a resistor, a capacitor, a flip-flop, a math operator, a sensor, an adder, a filter — anything that can be expressed as a computation over incoming messages producing outgoing messages.

Once assembled, the blocks form a mesh and pass messages to each other through their connections. The programmed logic of each block determines what messages it consumes, what it computes, and what it emits. The topology of the mesh is part of the system — routing and switching can itself be a programmable behavior.

---

## Physical Design

- **Blocks**: Small, roughly cube-shaped units housing a microcontroller, flash memory, and a small display. An e-paper screen is a natural fit — it is low power, readable in any lighting, and retains its image without power, so even a block sitting idle shows its current behavior and last known state. The display shows the loaded behavior name and a live status summary (current value, state, signal activity).
- **Connections**: Uniform connectors on each face — magnetic snap or jumper cables for cases where physical layout requires flexible routing. Connectors carry power and a shared communication bus.
- **Programmer**: A standalone cradle. Place a block on it, select a behavior from the companion app or a local menu, flash it. No soldering, no cables beyond the cradle's own USB link.
- **Bridge block**: A special block with a USB port (or BLE) that connects the mesh to a computer. Acts as a gateway — it can relay live signal traffic and inject commands from the companion app into the network.

---

## Communication Protocol

Blocks communicate peer-to-peer across their connections using a lightweight message-passing protocol. Key properties:

- **Uniform links**: Every connection looks the same to the blocks; direction (input vs output) is a role assigned by the programmed behavior, not the hardware.
- **Message-driven**: Blocks emit typed or untyped packets; neighbors decide whether to consume, forward, or discard.
- **Mesh-aware**: Blocks may optionally participate in topology discovery, allowing the companion app to reconstruct the current physical graph.
- **Supervisor layer**: An optional supervisor-level firmware layer can handle global concerns (clock sync, heartbeat, topology map) independently of user-programmed behavior.

---

## Digital Companion

A desktop or mobile application with two roles:

1. **Block programmer**: Browse a library of behaviors (circuit components, logic gates, math operators, custom code), flash a selected behavior onto a block via the programmer cradle.
2. **Live visualizer**: Connect to the bridge block and display the running mesh in real time — signal values on each link, internal state of each block, message traffic — as an animated schematic or graph.

The companion does not simulate; it observes and programs. The blocks themselves are the simulation.

---

## Example Scenarios

- **Analog circuit**: A set of blocks programmed as a voltage source, resistors, and a voltmeter. Connect them; the voltmeter block reports a computed voltage derived from the message values flowing through the network.
- **Logic circuit**: NAND gates, flip-flops, and a clock block. Assemble a latch or a counter; watch the state propagate in the companion app.
- **Signal processing**: Blocks as math operators (sum, scale, threshold) fed by a sensor block and driving an output block — a tactile dataflow graph.
- **Custom protocol experiment**: Load entirely custom firmware onto a set of blocks and observe how they self-organize.

---

## Next

- Choose a working name for the project.
- Define the minimal viable hardware spec: MCU family, connector type, power budget per block.
- Design the inter-block communication protocol: frame format, addressing, topology discovery.
- Decide whether topology discovery is mandatory or optional.
- Explore existing open-source mesh protocols (e.g. OpenThread, custom CAN-like bus) as a starting point.
- Design the programmer cradle: how does it identify an unknown block? How does it confirm flashing succeeded?
- Sketch the companion app's graph visualizer.
- Consider the [Universal MCU Test Board](Universal%20MCU%20Test%20Board.md) as a potential development platform for early block prototypes.
- See also [Trinkets](Trinkets.md) for related smaller experiments.
