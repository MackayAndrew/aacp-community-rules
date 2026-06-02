# AACP Community Rule Library

**191 pre-validated AACP v1.1 encoding rules for common enterprise workflows.**

MIT Licensed. Maintained by the AACP spec author.
The canonical implementation of the community encoding registry
called for in the AACP specification.

## What is a rule?

A rule is a pre-built, validated AACP v1.1 packet for a common
business workflow step. Instead of calling an LLM to encode a
natural language instruction, your agent loads the matching rule
from this library and gets the packet at zero cost.

```json
{
  "id": "hr-merge-calculate-payroll",
  "name": "Merge and calculate payroll run",
  "description": "Merge employee salary data with cost centre budgets and calculate full payroll",
  "task": "MERGE",
  "dom": "HR",
  "packet": "MERGE|HR|return:HR-Agent|p:1|aacp:1.1|rules:payroll_v2|validate:budget_cc",
  "tags": ["payroll", "merge", "calculate", "paye", "pension"],
  "version": "1.1",
  "source": "community",
  "validated": true
}
```

## Coverage — 191 rules across 7 domains

| Domain | Rules | Workflows covered |
|---|---|---|
| HR | 38 | Payroll, onboarding, absence, performance, headcount, contractors, offboarding |
| FIN | 35 | Invoicing, budget, month-end close, expenses, payment runs, FX, VAT, reporting |
| IT | 30 | Provisioning, access, incidents, licences, vulnerabilities, devices, change |
| SALES | 25 | Lead qualification, pipeline, forecasting, renewals, commissions, competitive |
| CS | 23 | Ticket resolution, sentiment, churn, NPS, SLA, goodwill |
| LEGAL | 22 | Contracts, NDA, GDPR, regulatory, litigation, policy, IP |
| MKT | 18 | Campaigns, lead quality, content, attribution, A/B testing |

Every rule is validated against the AACP v1.1 schema.
`"validated": true` on every rule in this library.

## How to use it

### Option 1 — Load individual rules directly

```python
import json
from pathlib import Path

# Load a specific rule
rule = json.loads(Path("rules/HR/hr-merge-calculate-payroll.json").read_text())
packet = rule["packet"]
# Send packet to your HR agent
```

### Option 2 — Search by tag (coming in SDK v1.4)

```python
from aacp import RuleRegistry

registry = RuleRegistry.from_community()
rule = registry.find("payroll merge calculate")
print(rule.packet)
# MERGE|HR|return:HR-Agent|p:1|aacp:1.1|rules:payroll_v2|validate:budget_cc
```

### Option 3 — Load entire library

```python
import json

index = json.loads(Path("index.json").read_text())
rules_by_domain = {}
for rule in index["rules"]:
    rules_by_domain.setdefault(rule["dom"], []).append(rule)
```

## Using this at work — the one-paragraph pitch

Your agents are writing coordination messages in natural language.
Every one of those messages is an API call and tokens cost money.
This library gives you 191 pre-built, validated packet templates
for the most common enterprise workflow steps -- payroll, invoicing,
IT provisioning, contract review, ticket handling, sales qualification.

Drop the matching rule into your agent's coordination message instead
of natural language. Zero API cost for encoding. Same packet every
time. Machine-readable. Auditable.

For anything not covered, AACP's fallback encoder calls an LLM once,
logs the result, and serves it from cache on every subsequent call.

## Repository structure

```
rules/
  HR/          38 rules — payroll, onboarding, absence, performance
  FIN/         35 rules — invoicing, budgets, month-end, expenses
  IT/          30 rules — provisioning, access, incidents, licences
  SALES/       25 rules — leads, pipeline, forecasting, renewals
  CS/          23 rules — tickets, sentiment, churn, NPS, SLA
  LEGAL/       22 rules — contracts, GDPR, regulatory, IP
  MKT/         18 rules — campaigns, attribution, content

schema/
  rule.schema.json    JSON schema for rule validation

index.json            Full library as single file (191 rules)
CONTRIBUTING.md       How to submit new rules
```

## Quality standards

Every rule in this library meets these requirements:

- Validated against AACP v1.1 schema (`"validated": true`)
- TASK is one of the 12 valid values
- DOM is one of the 7 valid values
- `return:` field present
- `aacp:1.1` field present
- Packet under 200 characters
- No empty positional slots
- Description matches what the packet does

Rules failing validation are not merged. CI runs validation on every PR.

## Contributing

See CONTRIBUTING.md for the rule submission process.
Rule submissions are welcome for any domain and workflow type.

## Links

- Spec and SDK: https://aacp.dev
- Python SDK: https://github.com/MackayAndrew/aacp (`pip install aacp`)
- TypeScript SDK: https://github.com/MackayAndrew/aacp-ts (`npm install aacp-ts`)
- IETF Draft: https://datatracker.ietf.org/doc/draft-mackay-aacp/

## Licence

MIT — free to use, copy, modify, and distribute.
