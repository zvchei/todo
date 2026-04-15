# Using LLMs as a Transpiler for Node-Graph Programs

TL;DR
-----
Treat an LLM as a transpiler: give it a free-text node description and require it to emit machine-checkable artifacts (input/output/exception schemas, unit tests, and a typed function) so the generated node can be used in a graph-based workflow engine, or wrapped as a standalone tool.

What this solves
----------------
In the context of the Generative Data Transformation Platform framewrork, users define nodes with free-text descriptions of their intended behavior. LLM-executed logic is powerful but fragile and non-deterministic. By forcing the LLM to produce code, schemas, and tests, the transpilation pipeline turns natural-language intent into verifiable, auditable artifacts that can be statically checked, sandbox-tested, and predictable.

Essential artifacts the transpiler must produce
----------------------------------------------
- input_schema - a machine-readable JSON Schema describing valid inputs.
- output_schema - a JSON Schema describing the function's outputs.
- exception_schema - a short, machine-readable description of expected error types and conditions.
- tests - pytest-style unit tests that document expected behavior and edge cases.
- function - the transpiled function (typed, documented, minimal runtime assumptions).
- metadata - node name, description, version, provenance.

Minimal prompt template (concept)
---------------------------------
Instruct the LLM to respond with a single JSON object containing these keys: `input_schema`, `output_schema`, `exception_schema`, `tests`, `function`, and `metadata`. Demand valid JSON only, include strict typing in schemas, and require at least three unit tests (normal case, edge case, invalid input).

Worked example — "Sum two numbers"
----------------------------------
Node description (free text):
"Node: Sum two numbers — Accepts two numbers `a` and `b` and returns their sum. If either input is not numeric, raise ValueError."

Transpiler output (example)

Input schema (JSON Schema):
```json
{
  "$id": "https://example.org/sum-two-numbers-input.schema.json",
  "title": "SumTwoNumbersInput",
  "type": "object",
  "properties": {
    "a": { "type": "number" },
    "b": { "type": "number" }
  },
  "required": ["a", "b"],
  "additionalProperties": false
}
```

Output schema (JSON Schema):
```json
{
  "$id": "https://example.org/sum-two-numbers-output.schema.json",
  "title": "SumTwoNumbersOutput",
  "type": "object",
  "properties": {
    "sum": { "type": "number" }
  },
  "required": ["sum"],
  "additionalProperties": false
}
```

Exception schema (machine-readable description):
```json
{
  "exceptions": [
    {
      "type": "ValueError",
      "when": "When 'a' or 'b' is not a number",
      "message_pattern": "Both 'a' and 'b' must be numbers"
    }
  ]
}
```

Unit tests (pytest-style):
```python
import pytest

def test_sum_two_positive():
    assert sum_two_numbers(1, 2) == 3.0

def test_sum_negative_and_positive():
    assert sum_two_numbers(-1, 1) == 0.0

def test_invalid_input_raises():
    with pytest.raises(ValueError):
        sum_two_numbers("x", 1)
```

Transpiled function (Python, type hints):
```python
def sum_two_numbers(a: float, b: float) -> float:
    """Return a + b. Raises ValueError if inputs are not numeric."""
    if not isinstance(a, (int, float)) or not isinstance(b, (int, float)):
        raise ValueError("Both 'a' and 'b' must be numbers")
    return float(a + b)
```

Safety and verification (short)
-------------------------------
Run static analysis and the generated unit tests in a sandboxed environment before accepting the node. Run linters and security scanners on the generated code.

Best practices (summary)
------------------------
- Always require explicit input/output/exception schemas and tests from the transpiler.
- Pin transpiled artifacts with metadata and a hash of the source prompt for auditability.
- Execute generated code only in constrained sandboxes; restrict network and filesystem access.
- Use provenance logs so the original prompt, model version, and generated artifacts are traceable.

References
----------
- [Generative Data Transformation Platform.md](./Generative%20Data%20Transformation%20Platform.md) — visual block/node-based data-flow system with prompt-configured "Generative" nodes.s
- [Autopoietic Workflow Orchestrator.md](./Autopoietic%20Workflow%20Orchestrator.md) - AI-native engine that compiles visual/textual workflows into a DSL and synthesizes node implementations.

---

This article is intentionally concise and focused on a single, minimal example; extended examples and prompt variants can live in a companion file or appendix if desired.
