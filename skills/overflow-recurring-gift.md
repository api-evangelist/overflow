---
name: Set up a recurring gift with Overflow
description: Create, inspect, and cancel a recurring donation subscription for a donor using the Overflow Open API v3.
api: openapi/overflow-openapi-original.json
operations:
  - OpenApiDonorProfilesController_createDonorProfile
  - OpenApiSubscriptionsController_createSubscription
  - OpenApiSubscriptionsController_getSubscriptionById
  - OpenApiSubscriptionsController_cancelSubscription
---

# Set up a recurring gift with Overflow

Recurring gifts are modeled as subscriptions tied to a donor.

## Auth
Headers `x-client-id` + `x-api-key` on every request. Base URL `https://server.overflow.co`.

## Steps
1. **Ensure a donor exists.** `OpenApiDonorProfilesController_createDonorProfile` (`POST /api/v3/donors`), or list with `OpenApiDonorProfilesController_getDonorProfiles`.
2. **Create the subscription.** `OpenApiSubscriptionsController_createSubscription` (`POST /api/v3/subscriptions/{donorId}`).
3. **Inspect.** `OpenApiSubscriptionsController_getSubscriptionById` (`GET /api/v3/subscriptions/{donorId}/{subscriptionId}`); list a donor's subscriptions with `OpenApiSubscriptionsController_getSubscriptionsByDonorProfile`.
4. **Update or cancel.** `OpenApiSubscriptionsController_updateSubscriptionById` (`PATCH`) or `OpenApiSubscriptionsController_cancelSubscription` (`DELETE /api/v3/subscriptions/{donorId}/{subscriptionId}`).

## Rules
- Subscriptions belong to a donor via `donorId` in the path.
- React to lifecycle via webhooks: `recurring_gift.active`, `recurring_gift.failed`, `recurring_gift.cycle_failed`, `recurring_gift.paused`.
- 400 on create/update means invalid parameters; 404 means donor or subscription not found. Do not retry 4xx.
- Amounts in cents; rate limit 120/min.
