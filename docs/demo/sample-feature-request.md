# Sample Feature Request

## Title

Audit timeline for persona changes

## Problem

Operators can update an AI persona configuration, but there is no clear audit trail showing who changed the persona, what fields changed, or when the change happened.

## Goal

Add an audit timeline that lets authorized users inspect persona changes for the last 90 days.

## User story

As an operations user, I want to view a timeline of persona changes so I can investigate unexpected behavior and answer support questions.

## Functional requirements

- Show the last 90 days of persona changes.
- Include actor, timestamp, change summary and affected persona.
- Support filtering by persona and date range.
- Prevent unauthorized users from seeing audit data.
- Show empty, loading and failure states in the UI.

## Non-functional requirements

- The timeline should load in under 2 seconds for the most recent 100 records.
- All audit access should itself be traceable.
- Sensitive values must be masked in the timeline output.

## Open questions

- Which roles are allowed to see audit history?
- Is the change summary generated from structured diffs or free-form activity logs?
- Does the system already store enough historical data to support a 90-day view?