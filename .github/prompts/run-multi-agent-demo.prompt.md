---
mode: ask
description: Run a sample multi-agent SDLC workflow using the repository's custom agents and orchestration skill.
---

Use the `multi-agent-orchestrator` skill and the custom agents in `.github/agents` to process the feature request from `docs/demo/sample-feature-request.md`.

Required flow:

1. Produce a short requirement artifact.
2. Produce an ADR-style architecture decision.
3. Produce a security review with explicit approval gates.
4. Produce an API contract proposal if needed.
5. Produce an implementation plan for backend, frontend and tests.
6. Produce a quality critique of the overall result.

Rules:

- Keep each stage scoped to the corresponding role.
- Make assumptions explicit.
- Do not invent repository files that do not exist unless the stage is explicitly proposing them.
- End with a concise list of next concrete tasks.