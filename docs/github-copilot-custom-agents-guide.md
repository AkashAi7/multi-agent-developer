# GitHub Copilot Custom Agents Guide

This repository includes a role-based GitHub Copilot agent set under `.github/agents` so teams can route work to focused personas instead of using a single general-purpose assistant.

It also includes a workspace skill under `.github/skills/multi-agent-orchestrator` for coordinating those personas as a staged SDLC workflow.

## Included agents

Core delivery lifecycle:
- `requirements-engineer`
- `platform-architect`
- `security-specialist`
- `backend-specialist`
- `frontend-specialist`
- `test-specialist`
- `documentation-steward`

Optional advanced agents:
- `quality-engineer`
- `refactoring-specialist`
- `api-architect`

## File structure

```text
.github/agents/*.agent.md
.github/skills/<name>/SKILL.md
.github/prompts/*.prompt.md
docs/github-copilot-custom-agents-guide.md
```

Each custom agent is a Markdown file with YAML frontmatter.

Frontmatter fields used here:
- `name`: display name for agent selection
- `description`: discovery text describing when to use the agent
- `tools`: allowed capabilities such as `read`, `search`, `edit`, `terminal`
- `target`: `vscode`
- `model`: optional model preference per role

## Tool access matrix

| Agent | read | search | edit | terminal | Purpose |
| --- | --- | --- | --- | --- | --- |
| Requirements Engineer | yes | yes | yes | no | Write requirement docs |
| Platform Architect | yes | yes | yes | no | Create ADRs |
| Security Specialist | yes | yes | no | no | Review security risks |
| Backend Specialist | yes | yes | yes | yes | Implement and verify backend code |
| Frontend Specialist | yes | yes | yes | yes | Implement and verify UI code |
| Test Specialist | yes | yes | yes | yes | Add and run tests |
| Documentation Steward | yes | yes | yes | no | Maintain docs and runbooks |
| Quality Engineer | yes | yes | no | no | Review change quality |
| Refactoring Specialist | yes | yes | yes | yes | Improve code without behavior changes |
| API Architect | yes | yes | yes | no | Design contracts and schemas |

## Recommended workflow

1. Requirements Engineer turns a feature request into a reviewable requirements document.
2. Platform Architect creates an ADR that assigns responsibility across frontend, backend and infrastructure.
3. Security Specialist reviews the design and identifies risk before implementation starts.
4. Backend Specialist and Frontend Specialist implement their slices with code changes and focused validation when the repo contains the relevant application surface.
5. Test Specialist strengthens coverage and validates the changed workflows.
6. Documentation Steward updates ADRs, guides, runbooks and release notes.
7. Quality Engineer optionally reviews the final diff with a different model or perspective.

If you want Copilot to help coordinate the sequence instead of manually picking agents each time, invoke the `multi-agent-orchestrator` skill with a feature request or change objective.

## Orchestration guidance

- Use a custom agent when you need one focused role with clear tool boundaries.
- Use a skill when you want one reusable workflow that coordinates several agents across stages.
- Keep the orchestration asset opinionated about sequence, handoff artifacts and approval gates.
- Make every handoff artifact explicit: requirement doc, ADR, security notes, implementation result, test evidence and release documentation.
- Treat backend, frontend and test agents as execution roles by default: they should edit code and run checks when code exists, not stop at recommendations.
- Only accept an implementation plan instead of code when the workspace genuinely lacks the files, stack, or runnable surface required for the task.

The provided orchestration skill expects these agents to collaborate in this order:
- `requirements-engineer`
- `platform-architect`
- `security-specialist`
- `api-architect` when contract work is needed
- `backend-specialist` and `frontend-specialist`
- `test-specialist`
- `documentation-steward`
- `quality-engineer`

## Example feature flow

For an audit timeline feature:
- Requirements Engineer writes `docs/features/audit-timeline.md`.
- Platform Architect records the system boundary decision in `docs/adr/0005-audit-timeline-architecture.md`.
- Security Specialist checks masking, authorization and audit exposure risks.
- Backend Specialist adds retrieval and persistence APIs.
- Frontend Specialist builds the timeline experience and resilient UI states.
- Test Specialist adds unit, integration and end-to-end coverage.
- Documentation Steward updates support and release documentation.

## Model strategy

- Writing-heavy roles can use `gpt-5.4`.
- Architecture and security roles benefit from strong reasoning models such as `gpt-5.4`.
- Implementation agents can use a code-focused model.
- Review agents should ideally use a different model than the implementation agent.

## Best practices

- Keep each agent focused on one organizational role.
- Restrict tools so the role boundary is enforced by configuration.
- Put trigger phrases in the `description` so Copilot can discover the right agent.
- Define explicit constraints and output formats in the Markdown body.
- Keep documentation, testing and review agents separate from implementation agents.

## VS Code usage

1. Open Copilot Chat.
2. Choose the Agent picker.
3. Select the appropriate agent, for example `backend-specialist`.
4. Ask for work that fits that role's responsibilities.

To run the end-to-end demo flow, open the sample prompt in `.github/prompts/run-multi-agent-demo.prompt.md` or copy the sample feature request from `docs/demo/sample-feature-request.md` into chat and ask Copilot to use the `multi-agent-orchestrator` skill.

## Notes for adapting this set

- Tighten technology-specific constraints once the repository stack is established.
- Add examples of good code or docs patterns when the project has real conventions.
- Keep descriptions updated because they are the primary discovery surface.
- If you want implementation-first behavior across projects, keep the orchestrator instructions stricter than the demo docs: require code edits plus validation whenever a runnable implementation surface exists.