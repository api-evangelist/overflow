---
name: Record a donation with Overflow
description: Create a donor profile and record a cash/card contribution against a campaign using the Overflow Open API v3.
api: openapi/overflow-openapi-original.json
operations:
  - OpenApiDonorProfilesController_createDonorProfile
  - OpenApiContributionsController_chargeCashPaymentMethod
  - OpenApiContributionsController_getContributionById
---

# Record a donation with Overflow

Use the Overflow Open API (v3) to capture a one-time gift for a nonprofit.

## Auth
Send both headers on every request (server-side only): `x-client-id` and `x-api-key`.
Base URL: `https://server.overflow.co` (production) or `https://server.stage.overflow.co` (staging). Credentials are environment-scoped.

## Steps
1. **Create or find the donor.** `OpenApiDonorProfilesController_createDonorProfile` (`POST /api/v3/donors`) creates a Donor Profile; list with `OpenApiDonorProfilesController_getDonorProfiles` first to avoid duplicates.
2. **Charge the payment method.** `OpenApiContributionsController_chargeCashPaymentMethod` (`POST /api/v3/contributions`) creates the contribution for the donor's payment method. Amounts are in cents.
3. **Confirm.** `OpenApiContributionsController_getContributionById` (`GET /api/v3/contributions/{contributionId}`) to read final status.

## Rules
- All monetary amounts are in **cents**.
- Contributions carry a discriminator `type`: Cash, Crypto, DAF, Stock, or Manual.
- Errors return standard HTTP codes (see errors/overflow-problem-types.yml). 400 = expired payment method / bad input; 404 = nonprofit not found. Do not retry 4xx; retry 500.
- Rate limit: 120 req/min; watch `x-ratelimit-remaining` / `x-ratelimit-reset`.
- Prefer webhooks (`contribution.approved` / `contribution.declined`) over polling for final state.
