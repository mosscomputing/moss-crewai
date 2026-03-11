# moss-crewai

MOSS integration for [CrewAI](https://crewai.com) - cryptographic signing for AI crew orchestration.

**Unsigned agent output is broken output.**

All signatures use **ML-DSA-44** (NIST FIPS 204), the post-quantum cryptographic standard.

[![PyPI](https://img.shields.io/pypi/v/moss-crewai)](https://pypi.org/project/moss-crewai/)

## Installation

```bash
pip install moss-crewai
```

## Quick Start: Explicit Signing (Recommended)

Sign task outputs, crew results, and agent outputs:

```python
from crewai import Crew, Agent, Task
from moss_crewai import sign_task_output, sign_crew_result, sign_agent_output

# Run your crew
crew = Crew(agents=[...], tasks=[...])
result = crew.kickoff()

# Sign the crew result
signed = sign_crew_result(result, agent_id="research-crew")
print(f"Signed: {signed.signature[:20]}...")

# Sign individual task outputs
for task_output in result.tasks_output:
    signed = sign_task_output(task_output, agent_id="research-crew", task="research")
```

## Enterprise Mode

Set `MOSS_API_KEY` for automatic policy evaluation:

```python
import os
os.environ["MOSS_API_KEY"] = "your-api-key"

from moss_crewai import sign_crew_result, enterprise_enabled

print(f"Enterprise: {enterprise_enabled()}")  # True

result = sign_crew_result(
    crew_output,
    agent_id="finance-crew",
    context={"user_id": "u123", "department": "finance"}
)

if result.blocked:
    print(f"Blocked by policy: {result.policy.reason}")
```

## Verification

```python
from moss_crewai import verify_envelope

verify_result = verify_envelope(result.envelope)
if verify_result.valid:
    print(f"Signed by: {verify_result.subject}")
```

## All Functions

| Function | Description |
|----------|-------------|
| `sign_task_output()` | Sign a task's output |
| `sign_task_output_async()` | Async version |
| `sign_agent_output()` | Sign an agent's output |
| `sign_agent_output_async()` | Async version |
| `sign_crew_result()` | Sign full crew kickoff result |
| `sign_crew_result_async()` | Async version |
| `verify_envelope()` | Verify a signed envelope |

## Legacy API

The old auto-signing API is still available:

```python
from moss_crewai import enable_moss, moss_wrap

enable_moss("moss:myteam:crewai")  # Global auto-signing
agent = moss_wrap(agent, "moss:team:researcher")  # Per-agent signing
```

## Pricing Tiers

| Tier | Price | Agents | Signatures | Retention |
|------|-------|--------|------------|-----------|
| **Free** | $0 | 5 | 1,000/day | 7 days |
| **Pro** | $1,499/mo | Unlimited | Unlimited | 1 year |
| **Enterprise** | Custom | Unlimited | Unlimited | 7 years |

*Annual billing: $1,249/mo (save $3,000/year)*

All new signups get a **14-day free trial** of Pro.

### Features by Tier

| Feature | Free | Pro | Enterprise |
|---------|------|-----|------------|
| Local signing | ✓ | ✓ | ✓ |
| Offline verification | ✓ | ✓ | ✓ |
| Policy evaluation | - | ✓ | ✓ |
| RBAC | - | ✓ | ✓ |
| Evidence retention | 7 days | 1 year | 7 years |
| Slack/Teams alerts | - | ✓ | ✓ + Buttons |
| SIEM integration | - | ✓ | ✓ |
| Compliance exports | - | ✓ | ✓ |

## Why Sign CrewAI Actions?

1. **Compliance** - Prove to auditors exactly what your crew did
2. **Accountability** - Cryptographic proof of every task output
3. **Crew Tracing** - Track work across agents and tasks
4. **Policy Enforcement** - Block unauthorized actions automatically
5. **Future-Proof** - ML-DSA-44 post-quantum signatures

## Links

- [mosscomputing.com](https://mosscomputing.com) - Project site
- [app.mosscomputing.com](https://app.mosscomputing.com) - Developer Console
- [moss-sdk](https://pypi.org/project/moss-sdk/) - Core MOSS SDK
- [CrewAI](https://crewai.com) - CrewAI framework

## License

This package is licensed under the [Business Source License 1.1](LICENSE).

- Free for evaluation, testing, and development
- Free for non-production use
- Production use requires a [MOSS subscription](https://mosscomputing.com/pricing)
- Converts to Apache 2.0 on January 25, 2030
