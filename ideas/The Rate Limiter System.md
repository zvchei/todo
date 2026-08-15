# The Rate Limiter System

## Rate Limiter Component

The stateful orchestration engine responsible for coordinating parallel and sequential task execution against active rules.

* **Acquisition**: Evaluates a task's cost metric against the Constraint Policy Schema by scheduling execution and calculating delays until constraints clear.
* **Release**: Updates internal time windows, sliding counters, and active concurrency slots based on the specific metrics and adapts to the error responses returned by a completed or filed task.

## Constraint Policy Schema

The declarative configuration blueprint that defines the operational boundaries of the system.

* **Window Volumes**: A collection of limit definitions that bind a specific volume threshold to a temporal duration. Example: 50 requests in 1 minute; 10M tokens in 6 hours.
* **Concurrency Ceiling**: A strict integer limit defining the maximum number of simultaneous in-flight operations. Example: 2 concurrent tasks.
* **Pacing Strategy**: An execution-distribution flag that switches between Burst mode (execute as soon as capacity clears) and Spread mode (mathematically smooth execution spacing across the active window).
* **Retry Policy**: A failure-handling profile that defines how a task may be retried after a transient failure. It includes retry counts, backoff intervals, jitter, and retryable error classes, and it is evaluated by the limiter before re-queuing a task or releasing it back into the active execution pool.

## Adaptor Interface

The continuous data-source pipeline that acts as an output stream of independent execution targets.

* **Stream Generator**: A contract that produces or yields objects conforming to the Task Interface from an underlying structural source.
* **Example Implementation Variants**:
  * Loop/Repeat Adaptor: Repeatedly clones and yields a single target function.
  * LLM Request Adaptor: Ingests raw text payloads and builds API-specific networking tasks.
  * FIFO Queue Adaptor: Pulls messages sequentially from a data queue and wraps them in task envelopes.

## Task Interface

The fundamental execution-unit contract defining the logic payload.

* **Execution Handle**: Exposes a standard invocation endpoint encapsulating the logic payload.
* **Cost Reporting**: Reports its resource weights after execution so the limiter can track resource drainage. Example: a task returning a cost of 500 tokens used, allowing the limiter to enforce token-per-minute ceilings; a task returning CPU and network traffic utilisation.
* **Error Signal Contract**: A standardized response structure returned upon task failure, so the limiter can dynamically adjust policy constraints. Example: returning a temporary error when an HTTP 429 is encountered, or returning an unrecoverable error to freeze the limiter queue immediately when an authentication token expires.