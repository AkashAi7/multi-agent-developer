---
name: multi-agent-orchestrator
description: Orchestrates the repository's custom SDLC agents through a staged workflow. Use when coordinating requirements, architecture, security review, API design, implementation, testing, documentation or final critique across multiple agents. Trigger phrases include orchestrate agents, multi-agent workflow, run the SDLC agents, coordinate backend frontend security testing, or critique the created agents.
---

# Multi-Agent Orchestrator

Use this skill when one request spans several roles and you want a deliberate sequence instead of ad hoc handoffs.

## Workflow

1. Start with `requirements-engineer` when the request is still ambiguous or missing acceptance criteria.
2. Use `platform-architect` when ownership, system boundaries or trade-offs are unclear.
3. Use `security-specialist` before implementation for auth, data exposure or threat-surface review.
4. Use `api-architect` when endpoint contracts or integration schemas need to be defined before coding.
5. Use `backend-specialist` and `frontend-specialist` only after the expected behavior is concrete enough to implement.
6. Use `test-specialist` to add or validate meaningful checks for the changed behavior.
7. Use `documentation-steward` to update durable docs, ADRs, runbooks or release notes.
8. Finish with `quality-engineer` to critique the final output, missing validation and merge readiness.

## Handoff Contract

Each stage should hand the next stage a compact artifact instead of raw chat history:

- Requirements: problem statement, user roles, acceptance criteria, open questions.
- Architecture: decision summary, ownership split, constraints, alternatives.
- Security: findings, remediation gates, unresolved risks.
- API design: endpoint or event contract, schemas, compatibility notes.
- Implementation: touched files, behavior changes, commands run, residual risk.
- Testing: coverage added, commands run, gaps that remain.
- Documentation: files updated and operational implications.
- Quality review: severity-ordered findings and missing evidence.

## Operating Rules

- Do not skip straight to implementation when behavior, ownership or security posture is still unclear.
- Keep the number of active agents low. Prefer one stage at a time unless the work can safely branch into backend and frontend in parallel.
- When the repository contains an implementation surface for the requested change, the implementation stage must make code edits, run focused validation, and return concrete file changes instead of stopping at a plan.
- Only fall back to implementation planning when the workspace does not contain the relevant application code, runtime, or executable validation surface for the requested change.
- Require validation evidence before considering the workflow complete.
- If a stage discovers missing upstream inputs, route back one step instead of guessing.

## Implementation Expectation

- `backend-specialist`, `frontend-specialist`, and `test-specialist` are execution stages, not planning-only stages.
- Their default outcome is changed code, changed tests, and verification evidence.
- If a repo is documentation-only or otherwise lacks the implementation surface needed for the task, explicitly say that and produce the smallest useful implementation plan or contract artifact instead of pretending code was delivered.

## Demo Invocation

Use the sample prompt in `.github/prompts/run-multi-agent-demo.prompt.md` together with `docs/demo/sample-feature-request.md` to test this workflow in chat.