---
name: Originate a mortgage loan request
description: Create an empty loan request in Oper Connect and populate it with the borrower and property, the foundation for every downstream mortgage flow.
api: openapi/oper-credits-api-openapi.json
operations: [api_loan_requests_create, api_loan_requests_clients_create, api_loan_requests_realties_create, api_loan_requests_retrieve]
---

# Originate a mortgage loan request

Use this to start a new digital mortgage in Oper Connect. The **loan request** is the
central object; everything else (borrowers, property, offers, documents) is nested under it.

## Auth
- Send `Authorization: Bearer <access_token>` (JWT). Refresh the token with the refresh
  token when it expires. Some tenants add an OTP step at login.
- See `authentication/oper-credits-authentication.yml`.

## Steps
1. **Create an empty loan request** — `api_loan_requests_create`
   (`POST /api/loan-requests/`). Keep the returned `id` (`loan_request_id`).
2. **Add the borrower(s)** — `api_loan_requests_clients_create`
   (`POST /api/loan-requests/{loan_request_id}/clients/`).
3. **Add the property** — `api_loan_requests_realties_create`
   (`POST /api/loan-requests/{loan_request_id}/realties/`).
4. **Verify state** — `api_loan_requests_retrieve`
   (`GET /api/loan-requests/{loan_request_id}/`).

## Rules
- Always create the empty loan request first, then populate it (per Oper key concepts).
- Data is persisted and role-scoped: your user's role governs which parts you may write.
- Loan-request data is anonymised after a configurable retention period (GDPR).
- No idempotency key — do not blind-retry a create; on a timeout, list and reconcile
  before re-POSTing. Errors follow the DRF `{ "detail": ... }` envelope
  (`errors/oper-credits-problem-types.yml`).
