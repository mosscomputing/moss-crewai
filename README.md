# moss-crewai

MOSS signing integration for CrewAI agents. **Unsigned output is broken output.**

[![PyPI](https://img.shields.io/pypi/v/moss-crewai)](https://pypi.org/project/moss-crewai/)

## Installation

```bash
pip install moss-crewai
```

## Quick Start: Auto-Signing (Recommended)

The easiest way to use MOSS with CrewAI is to enable auto-signing:

```python
from moss_crewai import enable_moss

# Enable auto-signing for all CrewAI operations
enable_moss("moss:myteam:crewai")

# All agent tasks, crew outputs, and tool calls are signed automatically
from crewai import Agent, Task, Crew

researcher = Agent(role="Researcher", goal="Find info", backstory="...")
writer = Agent(role="Writer", goal="Write content", backstory="...")

task = Task(description="Research AI trends", agent=researcher)
crew = Crew(agents=[researcher, writer], tasks=[task])

result = crew.kickoff()  # Output is signed!

# Access envelope
envelope = crew._moss_envelope
```

You can also enable auto-signing via environment variable:

```bash
export MOSS_AUTO_ENABLE=true
```

## Manual Usage with moss_wrap

For more control, wrap individual agents:

```python
from crewai import Agent
from moss_crewai import moss_wrap

# Create your CrewAI agent
agent = Agent(
    role="Researcher",
    goal="Find information",
    backstory="You are a research assistant"
)

# Wrap with MOSS signing
agent = moss_wrap(agent, "moss:team:researcher")

# After agent executes, signature is available
result = agent.execute_task(task)
envelope = agent.moss_envelope  # MOSS Envelope with signature
```

## Verification

```python
from moss import verify

# Verify the agent's output - no network required
result = verify(agent.moss_envelope)

if result.valid:
    print(f"Signed by: {result.subject}")
else:
    print(f"Invalid: {result.reason}")

# Or use envelope.verify() directly
result = agent.moss_envelope.verify()
assert result.valid
```

## Execution Record

Each signed output produces a verifiable execution record:

```
agent_id:      moss:team:researcher
timestamp:     2026-01-18T12:34:56Z
sequence:      1
payload_hash:  SHA-256:abc123...
signature:     ML-DSA-44:xyz789...
status:        VERIFIED
```

## What Gets Signed

With `enable_moss()`:
- `Agent.execute_task()` - Task start, completion, and errors
- `Crew.kickoff()` - Crew start, completion, and errors
- Tool calls - Input, output, and errors

With `moss_wrap()`:
- `execute_task()`, `execute()`, `run()`, `invoke()` methods

## Checking Status

```python
from moss_crewai import is_enabled, disable_moss

# Check if auto-signing is enabled
if is_enabled():
    print("MOSS auto-signing is active")

# Disable if needed (for testing)
disable_moss()
```

## Evidence Retention

Free tier provides runtime enforcement only. Production environments require retained, verifiable execution records.

See [mosscomputing.com](https://mosscomputing.com) for evidence continuity options.

## Links

- [moss-sdk](https://pypi.org/project/moss-sdk/) - Core MOSS SDK
- [mosscomputing.com](https://mosscomputing.com) - Project site
- [app.mosscomputing.com](https://app.mosscomputing.com) - Dashboard

## License

MIT
