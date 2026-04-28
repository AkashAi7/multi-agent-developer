# Multi-Agent Demo

This repo now includes a sample workflow you can use to test the custom agents as a coordinated SDLC system.

## What to test

- Individual agent discovery from the Agent picker.
- End-to-end orchestration through the `multi-agent-orchestrator` skill.
- Critique and handoff quality between roles.

## Fast test path

1. Open Copilot Chat in agent mode.
2. Invoke the prompt from `.github/prompts/run-multi-agent-demo.prompt.md`.
3. When asked for the source request, point Copilot at `docs/demo/sample-feature-request.md`.
4. Confirm that the workflow produces requirement, architecture, security, implementation and critique outputs in sequence.

## Manual test path

Paste this into Copilot Chat:

```text
Use the multi-agent-orchestrator skill with the agents in .github/agents.
Process the feature request in docs/demo/sample-feature-request.md.
Show me the staged outputs from requirements, architecture, security, API design if needed, implementation planning, testing, documentation updates and final quality critique.
```

## Expected outcome

- The requirements stage identifies missing authorization details.
- The architecture stage decides where audit retrieval, masking and retention rules belong.
- The security stage flags PII masking and audit-view authorization as approval gates.
- The implementation stages perform real code and test changes when the repo contains application code, and only fall back to planning when the repo is docs-only like this demo.
- The final critique calls out any missing validation evidence or unresolved product decisions.

## Sample API-stage artifact

- Persona audit timeline contract: `docs/demo/persona-audit-timeline-api-contract.md`