# Multi-Agent Persona

This repo gives GitHub Copilot a small SDLC team inside VS Code.

It includes:
- custom agents in `.github/agents`
- an orchestration skill in `.github/skills/multi-agent-orchestrator`
- demo inputs in `docs/demo`

## Use It

1. Open this repo in VS Code.
2. Open Copilot Chat in agent mode.
3. Use either:
   - a single agent from the agent picker, or
   - the `multi-agent-orchestrator` skill for an end-to-end flow

## End-to-End Sample Prompt

Paste this into Copilot Chat:

```text
Use the multi-agent-orchestrator skill with the agents in .github/agents.
Process the feature request in docs/demo/sample-feature-request.md.

Run the stages in order:
1. Requirements
2. Architecture
3. Security review
4. API contract if needed
5. Backend implementation plan
6. Frontend implementation plan
7. Test plan
8. Documentation updates
9. Final quality critique

For each stage, return a compact artifact that the next stage can use.
Do not invent files that do not exist.
If the repo does not contain an implementation surface, produce the smallest credible implementation plan instead of pretending code was changed.
End with the next concrete tasks.
```

## Included Roles

- `requirements-engineer`
- `platform-architect`
- `security-specialist`
- `api-architect`
- `backend-specialist`
- `frontend-specialist`
- `test-specialist`
- `documentation-steward`
- `quality-engineer`

## Key Files

- `.github/prompts/run-multi-agent-demo.prompt.md`
- `docs/demo/sample-feature-request.md`
- `docs/github-copilot-custom-agents-guide.md`
- `docs/demo/multi-agent-demo.md`

## Note

This repository is a demo and documentation workspace. In this repo, the implementation stages should usually return plans, contracts, and review artifacts rather than real application code.