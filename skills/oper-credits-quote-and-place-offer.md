---
name: Quote and place a mortgage offer
description: Simulate pricing and create an offer on an existing Oper Connect loan request, then record the underwriting decision.
api: openapi/oper-credits-api-openapi.json
operations: [api_simulators_offer_create, api_loan_requests_offers_list, api_loan_requests_offers_create, api_loan_requests_decisions_create]
---

# Quote and place a mortgage offer

Use this on an existing loan request (see the originate-loan-request skill) to price a
product, attach an offer, and capture the decision.

## Auth
- `Authorization: Bearer <access_token>` (JWT). See
  `authentication/oper-credits-authentication.yml`.

## Steps
1. **Simulate pricing** — `api_simulators_offer_create`
   (`POST /api/simulators/offer/`). Feed the simulation with the loan-request financials.
2. **Review existing offers** — `api_loan_requests_offers_list`
   (`GET /api/loan-requests/{loan_request_id}/offers/`).
3. **Create the offer** — `api_loan_requests_offers_create`
   (`POST /api/loan-requests/{loan_request_id}/offers/`) using the simulated terms.
4. **Record the decision** — `api_loan_requests_decisions_create`
   (`POST /api/loan-requests/{loan_request_id}/decisions/`).

## Rules
- Collections are page-number paginated (`page`, `page_size`).
- Role permissions gate who can create offers and decisions on a loan request.
- No idempotency key: reconcile via the offers list before re-creating on a timeout.
- Errors follow the DRF `{ "detail": ... }` envelope
  (`errors/oper-credits-problem-types.yml`).
