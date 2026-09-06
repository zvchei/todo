# The Contextual Hypermedia Protocol (CHP)

Modern APIs force rigid trade-offs between static REST schemas and complex RPC layers. The Contextual Hypermedia Protocol (CHP) eliminates this overhead by combining dynamic state machines with a minimalist, token-optimised syntax designed natively for both autonomous AI agents and runtime-rendered human interfaces. CHP functions via a continuous dialogue loop in which the server dictates an immediate action horizon, the client executes a command or supplies requested arguments, and the server updates its state payloads to produce a refreshed menu. In CHP, context is server-authoritative session state. The client maintains it by merging partial updates; the server determines the schemas, tools, and actions exposed to the client for its next invocation.

## Protocol Syntax and Mechanics

CHP utilises a lightweight layout to minimise byte overhead and provide clear structure for both machine parsers and human developers. The protocol relies on the following concepts:

* **Custom State Tables and Schemas**: The server transmits arbitrary named state tables, such as `[state]` or `[device]`, to represent current session parameters. State-table payloads are partial updates: the client merges supplied fields into the corresponding local table, and omitted fields retain their previous values. An optional `[schema.<name>]` table defines the expected structure of the matching `[<name>]` state table, using a TOML representation of the JSON Schema vocabulary used for LLM tool invocation: `type`, `properties`, `required`, `description`, and `enum`. A schema update fully replaces the preceding schema with the same name. Omitting a property's definition from a replacement schema signals that the server has deleted the corresponding field. The client may remove the field from its local state table or represent the deletion in another way. Schemas can be sent at any time, allowing client interfaces or UI renderers to adapt on the fly.

* **Reserved Keywords**: `[schema]`, `[actions]`, `[tools]`, and `[calls]`.

* **Tool Definitions (`[tools.<name>]`)**: Tools are named capabilities defined as maps, with the name serving as the unique identifier. Each tool's `args` value is an object schema using the same vocabulary as state schemas, equivalent to an LLM tool's parameter schema. Once transmitted, the client caches and maintains these tool definitions locally. The server may transmit a definition for a tool name at any time. If a definition with the same name already exists, the new definition fully replaces it.

* **Action Horizons (`[actions]`)**: The server publishes a `select` array of active tool names representing the immediate execution horizon. The client is restricted to invoking a tool present within this list, guaranteeing state-machine safety by construction. Every `[actions]` table fully replaces the preceding action horizon; it is not merged with it.

* **Semi-Asynchronous Updates**: The server may transmit state, schema, tool, or action updates at any time. A client may invoke only one action from the most recent action list it has received. Servers should avoid replacing an offered action list before the client invokes one of its actions, to reduce race conditions. A server that dynamically replaces an outstanding list must handle calls racing with that replacement to preserve session consistency.

* **Dual Roles**: CHP is duplex: either peer may act as both a client and a server in the same session. A "client" may therefore publish object state, schemas, tools, and actions using the same protocol structures as a server. A peer that does not support processing peer-originated updates may ignore them. This allows agent-to-agent communication.

* **Client Invocations (`[calls.<tool_name>]`)**: The client transmits execution commands by wrapping the tool name in a dedicated calls table and supplying required parameters directly as key-value pairs. Although the current protocol version permits only one action from the offered list per invocation, the calls container is a map keyed by tool name. This reserves support for multiple-action invocations in a future version.

## Protocol Execution Trace

Although serialisation can vary across languages, TOML is the principal reference representation. The following sequence illustrates a mixed-mode session between a client and an automated espresso machine.

```toml
# Server:

[schema.state]
type = "object"
properties.current = { type = "string" }

[state]
current = "IDLE"

[tools.heat_boiler]
desc = "Heat water to target temperature"
args.type = "object"
args.properties.temp_c = { type = "integer", description = "Target temperature in degrees Celsius" }
args.required = ["temp_c"]

[tools.check_reservoir]
desc = "Check water level in reservoir"

[tools.shutdown]
desc = "Power down the system"

[actions]
select = ["heat_boiler", "check_reservoir", "shutdown"]
```

```toml
# Client:
# state = { current = "IDLE" }

[calls.heat_boiler]
temp_c = 93
```

```toml
# Server:

[schema.state]
type = "object"
properties.current = { type = "string" }
properties.message = { type = "string" }
required = ["current"]

[state]
current = "HEATING"
message = "Boiler active. Target: 93C."

[tools.cancel]
desc = "Cancel the current operation"

[tools.query_temp]
desc = "Read the current boiler temperature"

[actions]
select = ["cancel", "query_temp", "shutdown"]
```

```toml
# Client:
# state = { current = "HEATING", message = "Boiler active. Target: 93C." }
```

```toml
# Server:

[state]
current = "THERMAL_READY"
message = "Temperature stabilised."

[tools.standby]
desc = "Enter standby mode"

[tools.grind_beans]
desc = "Grind coffee beans"
args.type = "object"
args.properties.grams = { type = "number", description = "Weight of beans in grams" }
args.required = ["grams"]

[tools.dispense_water]
desc = "Bypass water volume"
args.type = "object"
args.properties.ml = { type = "integer", description = "Water volume in millilitres" }
args.properties.temperature = { type = "string", enum = ["hot", "cold"] }
args.required = ["ml", "temperature"]

[actions]
select = ["grind_beans", "dispense_water", "shutdown"]
```

```toml
# Client:

[calls.shutdown]
```

```toml
# Server

[schema.state]
type = "object"
properties.current = { type = "string" }
# The 'message' field is removed.

[state]
current = "SHUTTING_DOWN"

[actions]
select = []
```

## Architectural Principles

* **Client Agnostic**: AI agents parse the raw tool maps and unique action names to make programmatic decisions. Human client applications read the exact same payloads to dynamically render native widgets, text inputs, and select fields at runtime.

* **Transport Agnostic**: The protocol is designed to function over various transport layers, including serial, WebSocket, and TCP sockets, without requiring modifications to the client or server logic.

* **Zero Maintenance**: Because the server dictates available actions and tool configurations on every turn, client software requires zero hardcoded API updates when backend capabilities evolve.

## Potential Issues and Considerations

* **Retained Definitions**: The protocol currently has no mechanism for deleting schemas or tools. Long-lived sessions that continuously introduce uniquely named definitions may therefore retain unnecessary client-side state and cause a memory leak. Implementations should consider session limits or client-side eviction. The protocol must also add support for requesting a missing schema.

* **Schema Definition**: The precise schema vocabulary and validation rules have not yet been defined. A future version should specify the supported JSON Schema subset, including nested objects, arrays, nullable values, and handling of invalid data.

* **Error Signalling**: CHP does not yet define a standard representation for rejected calls, validation failures, unavailable actions, or server-side errors. This should be specified so clients can distinguish an unsuccessful invocation from an ordinary state update.