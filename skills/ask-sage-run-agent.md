---
name: Run an Ask Sage agent
description: Authenticate, list the agents available to the user, and execute one with a message and optional variables.
api: openapi/ask-sage-server-openapi.json
operations:
  - POST /user/get-token-with-api-key
  - POST /server/list-agents
  - POST /server/execute-agent
---

# Run an Ask Sage agent

Execute a pre-built Ask Sage agent (Agent Builder workflow) programmatically.

## Steps

1. **Get an access token.** POST email + API key to
   `/user/get-token-with-api-key`; keep the JWT.
2. **Authenticate every call** with `x-access-tokens: <jwt>`.
3. **List agents.** POST `/server/list-agents` (operationId `list_agents_ep`)
   to enumerate agents available to you; capture the target `agent_id`.
4. **Execute.** POST `/server/execute-agent` (operationId `execute_agent_ep`)
   with the `agent_id`, a `message`, and any `variables`. Read the result from
   the `AgentExecutionResponse` envelope.

## Rules

- A 400 usually means a bad `agent_id` or malformed `variables`; a 404 means the
  agent was not found or you lack access; a 409 is a duplicate-name conflict on
  create; a 504 means the agent execution exceeded its time budget.
- Execution failures surface as the `ExecutionError` shape
  `{ error, message, details }`; inspect it rather than retrying blindly.
- See `conventions/ask-sage-conventions.yml` and
  `errors/ask-sage-problem-types.yml`.
