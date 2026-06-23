# moss-crewai

Cryptographic signing for CrewAI multi-agent orchestration using ML-DSA-44 post-quantum signatures.

[![PyPI](https://img.shields.io/pypi/v/moss-crewai)](https://pypi.org/project/moss-crewai/)
[![License](https://img.shields.io/badge/license-BSL--1.1-blue)](LICENSE)

## Overview

moss-crewai integrates MOSS cryptographic signing into your CrewAI workflows. Every task output, agent action, and crew result gets a tamper-evident signature using ML-DSA-44 (NIST FIPS 204), the post-quantum cryptographic standard. This creates an immutable audit trail for compliance, debugging, and multi-agent accountability.

## Installation

```bash
pip install moss-crewai
```

## Quick Start

```python
from crewai import Crew, Agent, Task
from moss_crewai import sign_task_output, sign_crew_result

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

## Features

- **ML-DSA-44 signatures** - Post-quantum cryptographic standard (NIST FIPS 204)
- **Crew result signing** - Sign complete crew kickoff outputs
- **Task output signing** - Sign individual task results
- **Agent output signing** - Sign per-agent contributions
- **Policy enforcement** - Block high-risk actions with enterprise policies
- **Crew tracing** - Track work across agents and tasks
- **Offline verification** - Verify signatures without network access

## Usage Examples

### Basic Usage

```python
from moss_crewai import sign_task_output, sign_agent_output, verify_envelope

# Sign a task output
signed = sign_task_output(task_output, agent_id="my-crew", task="analysis")

# Sign agent output
signed = sign_agent_output(agent_output, agent_id="my-crew", agent_name="researcher")

# Verify any envelope
verify_result = verify_envelope(signed.envelope)
print(f"Valid: {verify_result.valid}, Subject: {verify_result.subject}")
```

### With Policy Enforcement

```python
import os
os.environ["MOSS_API_KEY"] = "your-api-key"

from moss_crewai import sign_crew_result

result = sign_crew_result(
    crew_output,
    agent_id="finance-crew",
    context={"user_id": "u123", "department": "finance"}
)

if result.blocked:
    print(f"Blocked by policy: {result.policy.reason}")
```

## API Reference

| Function | Description |
|----------|-------------|
| `sign_task_output()` | Sign a task's output |
| `sign_task_output_async()` | Async version |
| `sign_agent_output()` | Sign an agent's output |
| `sign_agent_output_async()` | Async version |
| `sign_crew_result()` | Sign full crew kickoff result |
| `sign_crew_result_async()` | Async version |
| `verify_envelope()` | Verify a signed envelope |
| `enterprise_enabled()` | Check if enterprise mode is active |

## Configuration

| Environment Variable | Description |
|---------------------|-------------|
| `MOSS_API_KEY` | API key for enterprise features (policy enforcement, SIEM) |
| `MOSS_API_URL` | Custom API endpoint (default: api.mosscomputing.com) |

## Links

- [Documentation](https://docs.mosscomputing.com/sdks/crewai)
- [Dashboard](https://app.mosscomputing.com)
- [PyPI](https://pypi.org/project/moss-crewai/)

## License

Business Source License 1.1 - Production use requires a [MOSS subscription](https://mosscomputing.com/pricing).
