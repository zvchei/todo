# The Contextual Hypermedia Protocol (CHP)

This article describes a design sketch for a fluid, state-driven interaction model for human and agent interfaces. CHP is not intended to be a rigid transport-level protocol with mandatory client memory semantics. Instead, it is a way for peers to negotiate context, expose available actions, and publish state transitions in a partially-updated, server-directed conversation. The server reveals state and permitted actions; the client may interpret, retain, archive, or discard that information as it sees fit.

Modern APIs often force a trade-off between static REST schemas and more complex RPC layers. The Contextual Hypermedia Protocol (CHP) explores a different pattern: dynamic state machines expressed in a lightweight syntax that can be interpreted by both autonomous AI agents and runtime-rendered human interfaces. CHP works as a continuous dialogue loop in which the server presents an immediate action horizon, the client executes a command or provides requested arguments, and the server updates its state payloads to produce a refreshed menu. In CHP, context represents server-authoritative session state expressed through partial updates and intent signaling; the server determines the schemas, tools, and actions exposed to the client for its next invocation, while the client interprets and manages that context according to its own requirements.

## Protocol Syntax and Mechanics

CHP uses a lightweight layout to reduce byte overhead and provide a clear structure for both machine parsers and human developers. The protocol relies on the following concepts:

* **Custom State Tables, Arrays, and Schemas**: The server transmits arbitrary named state tables, such as `[state]` or `[device]`, to represent current session parameters. By default, state tables act as partial updates: the sender transmits only modified or new fields, and omitted fields retain their prior values. Sending an empty table (e.g., `state = {}` or an empty `[state]`) signals a request to clear or delete that table.

    TOML array-of-tables entries, such as `[[messages]]`, represent contextual arrays. Each entry creates an object from its supplied fields and appends it to the matching local array without merging into earlier entries. Transmitting an empty array (such as `messages = []`) signals an intent to clear or delete the array.

    An optional `[schema.<name>]` table defines the expected structure of `[<name>]` (or elements of `[[<name>]]`) using a TOML representation of a constrained subset of JSON Schema. CHP adopts the standard JSON Schema keywords that are relevant to tool and UI metadata: `type`, `properties`, `required`, `description`, and `enum`. Each schema update fully replaces the previous schema with that name. When a replacement schema omits a property definition, the server signals that the field has been deleted; the client may remove it from its local state table or handle the deletion in another way. Schemas can be sent at any time, allowing client interfaces or UI renderers to adapt on the fly.

* **Partial Update Etiquette & Client Autonomy**: The protocol establishes conventions for signalling intent (such as partial updates, array accumulation, or empty-container deletions), but how a peer internally handles, retains, or invalidates prior state is not strictly mandated. Interpretation is an implicit contract and communication etiquette: while the server indicates its immediate context and intended state changes, the client remains free to retain past messages in a conversation history, archive superseded state tables, or purge deleted items immediately.

* **Reserved Keywords**: CHP reserves a small set of table names for protocol-level semantics. These names are part of the CHP control vocabulary and are treated specially by implementations:

  * `[schema]`
  * `[actions]`
  * `[tools]`
  * `[calls]`

  Other names are defined by the service. Reserved names carry protocol meaning; non-reserved names are implementation-defined state or convenience structures. A client should not assume that a particular non-reserved table name has any fixed semantics unless the server communicates that meaning through a schema or explicit documentation.

* **Tool Definitions (`[tools.<name>]`)**: Tools are named capabilities defined as maps, with the name serving as the unique identifier. Each tool exposes a human-readable `description` field and an `args` object schema using the same constrained JSON Schema subset as state schemas. This is equivalent to an LLM tool-call parameter schema. Once transmitted, the client caches and maintains these tool definitions locally. The server may transmit a definition for a tool name at any time. If a definition with the same name already exists, the new definition fully replaces it.

* **Action Horizons (`[actions]`)**: The server publishes a `select` array of active tool names representing the immediate execution horizon. The client is restricted to invoking a tool present within this list. Every `[actions]` table fully replaces the preceding action horizon; it is not merged with it.

* **Semi-Asynchronous Updates**: The server may transmit state, schema, tool, or action updates at any time. A client may invoke only one action from the most recent action list it has received. Servers should avoid replacing an offered action list before the client invokes one of its actions, to reduce interference and stale-horizon races. A server that dynamically replaces an outstanding list must handle calls racing with that replacement in a way that preserves session consistency for the application.

* **Dual Roles**: CHP is duplex: either peer may act as both a client and a server in the same session. A "client" may therefore publish object state, schemas, tools, and actions using the same protocol structures as a server. A peer that does not support processing peer-originated updates may ignore them. This allows agent-to-agent communication.

* **Client Invocations (`[calls.<tool_name>]`)**: The client transmits execution commands by wrapping the tool name in a dedicated calls table and supplying required parameters directly as key-value pairs. Although the current protocol version permits only one action from the offered list per invocation, the calls container is a map keyed by tool name. This reserves support for multiple-action invocations in a future version.

* **Optional Call Correlation (`id`)**: A tool may advertise an optional `id` argument in `args.properties`, but `id` must never appear in `args.required` as not every client may support generating random identifiers. Client SDKs or transport wrappers may inject the unique identifier into this field without requiring the agent or human operator to generate one. If an `id` is supplied, a server may expose schema-described current request state so the client can render and correlate operations across multiplexed channels. The request-state map key is a client-side context key and may differ from the `id` field inside the request object, which is the server-supported request identity used for correlation. Each entry may describe a pending, successful, or failed operation. A server may also expose schema-described historical lifecycle and outcome records, including timestamps, for reference by an operator or agent. If `id` is omitted, the server processes the invocation normally and must not reject the call for that reason.

* **Application-Level Request State**: Names such as `[requests.<key>]` are arbitrary application-level table names, not reserved CHP keywords. When a service uses them, it should describe their purpose and structure to the client through schemas, allowing the client to discover which table represents current request state for a specific operation. The `[requests.<key>]` naming convention is clear and reusable, but it is optional; services may use other names instead. Either structure may be omitted, renamed, or represented differently; neither is required by CHP. Clients may maintain their own local audit trail, but CHP does not require server-managed history.

## Protocol Execution Trace

The following examples are illustrative. They show one possible way to use CHP in practice, not a mandatory serialization or state-management contract.

Although serialisation can vary across languages, TOML is the principal reference representation. The following sequence illustrates a mixed-mode session between a client and an automated espresso machine.

```toml
# Server:

[schema.state]
type = "object"
properties.current = { type = "string" }

[state]
current = "IDLE"

[tools.heat_boiler]
description = "Heat water to target temperature"
args.type = "object"
args.properties.temp_c = { type = "integer", description = "Target temperature in degrees Celsius" }
args.properties.id = { type = "string", description = "Optional client call correlation ID" }
args.required = ["temp_c"]

[tools.check_reservoir]
description = "Check water level in reservoir"

[tools.shutdown]
description = "Power down the system"

[actions]
select = ["heat_boiler", "check_reservoir", "shutdown"]
```

```toml
# Client:
# state = { current = "IDLE" }

[calls.heat_boiler]
temp_c = 93
id = "call_uuid_88f1"
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

[requests.boiler_call_88f1]
id = "req_7742"
status = "STARTED"

[tools.cancel]
description = "Cancel the current operation"

[tools.query_temp]
description = "Read the current boiler temperature"

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

[requests.boiler_call_88f1]
id = "req_7742"
status = "COMPLETED"

[tools.standby]
description = "Enter standby mode"

[tools.grind_beans]
description = "Grind coffee beans"
args.type = "object"
args.properties.grams = { type = "number", description = "Weight of beans in grams" }
args.required = ["grams"]

[tools.dispense_water]
description = "Bypass water volume"
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

### Contextual Arrays and State Deletions

Contextual arrays accumulate entries across partial server updates. Empty containers (`[]` or `{}`) signal the intent to clear or delete the corresponding array or state table:

```toml
# Server:

[[messages]]
content = "Hello!"
```

```toml
# Client:
# messages = [{ content = "Hello!" }]
```

```toml
# Server:

[[messages]]
content = "How are you?"
```

```toml
# Client:
# messages = [{ content = "Hello!" }, { content = "How are you?" }]
```

```toml
# Server:

messages = []
state = {}
```

```toml
# Client:
# The 'messages' array is deleted or emptied: messages = [].
# The 'state' table is cleared/deleted.
```

## Architectural Principles

* **Client Agnostic**: AI agents parse the raw tool maps and unique action names to make programmatic decisions. Human client applications read the same payloads to dynamically render native widgets, text inputs, and select fields at runtime.

* **Transport Agnostic**: The protocol is shaped to function over various transport layers, including serial, WebSocket, and TCP sockets, without requiring special client or server logic for the transport itself.

* **Client Autonomy**: Partial updates, accumulation, and empty-container resets communicate server intent rather than strictly enforcing client-side memory management. The client remains autonomous in deciding whether to preserve historical entries, archive obsolete states, or purge data immediately.

## Application-Level Error State Modeling

CHP does not mandate a rigid, protocol-level error structure. Instead, failures and rejected invocations are communicated as application-level state transitions. A service may update fields such as `state.status = "ERROR"`. However, a state is typically considered global across the entire client-server interaction. An informal `[requests.<key>]` map can carry the current state of each request; the map key is a client-side context key, while the request object's `id` is the server-supported request identity and the two may differ. That structure may be omitted. Its fields and representation are application-level design choices, not CHP requirements. A client may keep any additional local audit trail it needs, but CHP does not require the server to maintain such history. For example:

```toml
[requests.req_9910]
id = "req_9910"
status = "ERROR"
code = "INVALID_ARGUMENT"
message = "The motor driver is not responding."
target_call = "relate"

[state]
status = "ERROR"
error = "The service is unavailable."

[actions]
select = ["insert_entry", "relate", "show", "find"]
```

Human interfaces, CLI clients, and AI agents inspect these application-level updates and decide locally whether to display the error, write it to `stderr`, retry the call, or take another action.

## Open Questions and Future Work

* **Schema Refresh / Rehydration**: The protocol currently has no explicit mechanism for requesting a missing or out-of-date schema. A future version should define how a client asks for a schema refresh or rehydration without guessing state structure.

* **Retained Definitions**: The protocol currently has no explicit deletion mechanism for schemas or tools. Long-lived sessions that continuously introduce uniquely named definitions may therefore retain unnecessary client-side state and cause a memory leak. Implementations should consider session limits or client-side eviction.

* **Schema Definition**: The precise schema vocabulary and validation rules are still intentionally loose. A future version should specify the supported JSON Schema subset, including nested objects, arrays, nullable values, and handling of invalid data.

* **Concurrency and Race Handling**: The article describes action lists and server-directed state transitions, but it does not yet define a formal model for stale action horizons, overlapping calls, or race conditions across dynamic updates.