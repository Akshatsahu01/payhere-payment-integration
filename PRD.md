# Product Requirements Document (PRD)

## 1. Product Summary

A lightweight payment demo that enables a customer to pay for an order through the PayHere sandbox. The product demonstrates the complete browser-to-backend-to-payment-provider integration and provides a foundation for a production checkout flow.

## 2. Problem Statement

Developers need a working reference for securely generating PayHere hashes on a server, launching PayHere checkout from React, and verifying PayHere server notifications instead of trusting browser-side results.

## 3. Goals

- Start a PayHere sandbox payment from a React page.
- Keep hash generation and merchant secrets on the backend.
- Verify notification authenticity and accept successful payments only.
- Make the integration easy to run locally and easy to connect to a public notify URL.

## 4. Users and User Stories

### Developer

- As a developer, I can install frontend and backend dependencies separately.
- As a developer, I can configure merchant credentials and a public notify URL.
- As a developer, I can run the frontend and backend locally.
- As a developer, I can inspect logs to understand hash generation and notification verification.

### Customer

- As a customer, I can click a payment button and complete a PayHere checkout.
- As a customer, I can be returned to the application after success or cancellation.
- As a customer, I receive a clear error when payment setup or checkout fails.

### Merchant

- As a merchant, I can rely on a verified provider notification for payment status.
- As a merchant, I can associate a payment with an order and avoid duplicate fulfillment.

## 5. Current MVP Requirements

| ID | Requirement | Acceptance criteria |
|---|---|---|
| MVP-01 | Payment start | Clicking the payment button requests a hash from `POST /payment/start`. |
| MVP-02 | Hash security | The merchant secret is used only by the backend to generate the hash. |
| MVP-03 | PayHere checkout | The frontend starts PayHere with sandbox mode and the returned hash. |
| MVP-04 | Notification verification | `POST /payment/notify` recomputes `md5sig` and accepts only valid status `2`. |
| MVP-05 | Local setup | README instructions start the backend on `5001` and frontend on `3000`. |
| MVP-06 | Public notification | Documentation explains that PayHere must reach the notify URL publicly. |

## 6. Next Release Requirements

| Priority | Requirement | Acceptance criteria |
|---|---|---|
| P0 | Environment configuration | Credentials and URLs load from environment variables; no secrets are committed. |
| P0 | Order persistence | An order is stored before checkout and its state changes only after verified notification. |
| P0 | Input validation | Invalid order IDs, amounts, currencies, and missing fields receive a clear `4xx` response. |
| P0 | Idempotency | Repeated notifications do not duplicate fulfillment or alter a completed order incorrectly. |
| P1 | Result pages | Success, cancel, and failure states are visible in the frontend. |
| P1 | User feedback | Loading, API failure, and payment failure states are visible and actionable. |
| P1 | Test coverage | Hash, notification, API validation, and payment-start flows have automated tests. |
| P2 | Operations | Add structured logs, request IDs, metrics, alerts, and a health endpoint. |

## 7. Non-Functional Requirements

- Security: HTTPS in deployed environments; secret values never exposed to clients or logs.
- Reliability: notification processing is idempotent and safely retryable.
- Performance: hash-generation requests should return quickly without external provider calls.
- Compatibility: support modern browsers listed in the frontend browserslist.
- Maintainability: keep provider-specific signing and verification isolated in backend payment services.
- Privacy: minimize customer data retained and protect any persisted personal information.

## 8. Success Metrics

- A configured developer can run the demo locally in under 10 minutes.
- A valid sandbox payment reaches the notify endpoint and is accepted.
- Invalid signatures are rejected with no order fulfillment.
- Duplicate notifications produce one effective payment state transition.
- Automated tests cover all payment-signing and notification-verification branches.

## 9. Dependencies and Assumptions

- A PayHere sandbox merchant account and credentials are available.
- PayHere SDK availability and provider hash rules remain compatible with the implementation.
- The notify URL is publicly reachable over the internet.
- A database and deployment platform will be selected before productionization.

## 10. Risks

- Hard-coded merchant credentials can cause credential exposure; rotate the current value and move it to a secret store.
- Browser return URLs can be spoofed or interrupted; fulfillment must use verified notifications.
- The current fixed order data is not suitable for multiple customers or real orders.
- PayHere retries can create duplicate updates unless idempotency is implemented.
- CORS is currently local-only and must be configured per deployment environment.

## 11. Release Definition

The integration is ready for a production pilot when environment-based secrets, backend validation, persistent order states, idempotent notification handling, result pages, HTTPS, and automated tests are implemented and a complete sandbox-to-production migration checklist is approved.
