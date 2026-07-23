---
name: Query a model with a private dataset (RAG)
description: Authenticate, discover available models and datasets, then run a grounded completion against Ask Sage using a private dataset for retrieval.
api: openapi/ask-sage-server-openapi.json
operations:
  - POST /user/get-token-with-api-key
  - POST /server/get-models
  - POST /server/get-datasets
  - POST /server/query
---

# Query a model with a private dataset

Use this to run a retrieval-grounded generative-AI completion on the Ask Sage
platform. All calls require the `x-access-tokens` header (a JWT).

## Steps

1. **Get an access token.** POST your email and 64-character API key to the User
   API `/user/get-token-with-api-key`. Store the returned JWT.
2. **Set the auth header.** Send `x-access-tokens: <jwt>` on every subsequent
   request. See `authentication/ask-sage-authentication.yml`.
3. **List models.** POST `/server/get-models` to see the models available to
   your account and pick a `model`.
4. **List datasets.** POST `/server/get-datasets` to find the private dataset to
   ground on.
5. **Query.** POST `/server/query` with the `message`, the chosen `model`, and
   the target `dataset`. Read the completion from the `CompletionResponse`
   envelope.

## Rules

- Base URL: `https://api.asksage.ai` (use your organization's approved
  instance for IL5/IL6 / self-hosted deployments).
- Errors return the `ApiError` envelope `{ error, status, details }` — not
  RFC 9457. Handle 401 (bad/expired token), 413 (payload too large), and 504
  (execution timeout). See `errors/ask-sage-problem-types.yml`.
- Usage is metered in Ask Sage tokens; check remaining quota with
  `/server/count-monthly-tokens`.
- No idempotency-key contract; do not blindly auto-retry writes.
