---
name: Reconcile Overflow deposits
description: Match settled deposits to their contributions, refunds, and chargebacks for accounting reconciliation using the Overflow Open API v3.
api: openapi/overflow-openapi-original.json
operations:
  - OpenApiDepositsController_getDeposits
  - OpenApiDepositsController_getDepositById
  - OpenApiDepositsController_getDepositSummary
  - OpenApiContributionsController_getContributions
---

# Reconcile Overflow deposits

A deposit is a settlement from Overflow to the nonprofit's bank account. Use these operations to reconcile payouts.

## Auth
Headers `x-client-id` + `x-api-key`. Base URL `https://server.overflow.co`.

## Steps
1. **List deposits.** `OpenApiDepositsController_getDeposits` (`GET /api/v3/deposits`) with date-range filters `minimumUpdatedDate` / `maximumUpdatedDate` and `limit` + `page`.
2. **Get the breakdown.** `OpenApiDepositsController_getDepositById` (`GET /api/v3/deposits/{depositId}`) returns the line-item breakdown.
3. **Get aggregated totals.** `OpenApiDepositsController_getDepositSummary` (`GET /api/v3/deposits/{depositId}/summary`) returns totals for contributions, refunds, chargebacks, and adjustments — all in cents.
4. **Tie out.** Cross-reference with `OpenApiContributionsController_getContributions`, `OpenApiRefundsController_getRefunds`, and `OpenApiChargebacksController_getChargebacks`.

## Rules
- All monetary amounts are in **cents**.
- Collection responses use the `{ data, totalCount }` envelope with offset pagination (`limit` + `page`).
- Automate via the `deposit.paid_out` webhook rather than polling.
