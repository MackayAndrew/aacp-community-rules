# Contributing to the AACP Community Rule Library

Thank you for contributing. This library exists to give engineers
a zero-cost starting point for AACP-encoded workflows.

## Before you submit

Read the rule format and quality standards in the README.
Every rule must pass validation before it will be merged.

## Rule format

```json
{
  "id":          "domain-description-of-action",
  "name":        "Short human-readable name",
  "description": "One sentence describing what this rule does",
  "task":        "FETCH",
  "dom":         "HR",
  "packet":      "FETCH|HR|return:HR-Agent|p:2|aacp:1.1|res:emp_salary|...",
  "tags":        ["tag1", "tag2", "tag3"],
  "version":     "1.1",
  "source":      "community",
  "validated":   true
}
```

## ID naming convention

```
{domain-lowercase}-{verb}-{object}

Examples:
  hr-fetch-employee-records
  fin-proc-invoice-three-way-match
  it-flag-excessive-permissions
  cs-resolve-high-ltv-complaint
```

## Valid TASK values

```
FETCH  PROC  FLAG  RESOLVE  LOG  SEND
BUILD  MERGE  CALC  REPORT  ACK  SYNC
```

## Valid DOM values

```
HR  FIN  SALES  LEGAL  IT  CS  MKT
```

## Packet rules

- TASK and DOM are positional (fields 0 and 1)
- All other fields must be named key:value pairs
- No empty positional slots
- `return:` is required
- `aacp:1.1` is required
- Packet must be under 200 characters
- No Python expressions in values

## Validation

Run this before submitting:

```python
import json

VALID_TASKS = {"FETCH","PROC","FLAG","RESOLVE","LOG","SEND",
               "BUILD","MERGE","CALC","REPORT","ACK","SYNC"}
VALID_DOMS  = {"HR","FIN","SALES","LEGAL","IT","CS","MKT"}

def validate(rule):
    packet = rule["packet"]
    fields = [f.strip() for f in packet.split("|")]
    assert len(fields) >= 3,           "Too few fields"
    assert fields[0] in VALID_TASKS,   f"Unknown TASK: {fields[0]}"
    assert fields[1] in VALID_DOMS,    f"Unknown DOM: {fields[1]}"
    keys = {f.split(":",1)[0].lower() for f in fields[2:] if ":" in f}
    assert "return" in keys,           "Missing return:"
    assert "aacp"   in keys,           "Missing aacp:"
    assert len(packet) <= 200,         "Packet too long"
    print("Valid")

with open("your-rule.json") as f:
    validate(json.load(f))
```

## Submitting

1. Create your rule file in the correct domain folder
2. Run validation
3. Open a pull request with a brief description of the workflow
4. CI will validate automatically

## Scope

Rules are accepted for any repeatable business workflow step.
Rules should represent real patterns seen in production agent systems.
