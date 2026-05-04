# stripe-accept-a-payment — Custom Payment Flow

> **Application Under Test** for the [fintech-playwright-quality](https://github.com/summerduck/fintech-playwright-quality) showcase — an AI-augmented fintech testing platform.

This repository runs the **custom-payment-flow** integration from [stripe-samples/accept-a-payment](https://github.com/stripe-samples/accept-a-payment). It is a Flask-based server that exposes Stripe's PaymentIntents API alongside a static HTML client, used as a real payment application to test against.

---

## What this app does

- Accepts payments via Stripe [Elements](https://stripe.com/docs/stripe-js) with a fully custom form (no Stripe-hosted UI)
- Supports multiple payment method types: Card, ACSS Debit, BECS Direct Debit, SEPA Direct Debit, Bancontact, iDEAL, Afterpay/Clearpay, OXXO, Alipay, Apple Pay, Google Pay, GrabPay, and more
- Exposes three server endpoints consumed by the tests:
  - `GET /config` — returns the Stripe publishable key
  - `POST /create-payment-intent` — creates a PaymentIntent for a given payment method type and currency
  - `POST /webhook` — handles Stripe webhook events

---

## Stack

| Layer | Technology |
|---|---|
| Server | Python 3.8+ / Flask |
| Client | Static HTML |
| Stripe SDK | `stripe` Python library (`2023-10-16` API version) |

---

## Running locally

### 1. Configure environment variables

```bash
cp .env.example .env
```

Fill in your [Stripe test API keys](https://dashboard.stripe.com/apikeys):

```bash
STRIPE_PUBLISHABLE_KEY=pk_test_...
STRIPE_SECRET_KEY=sk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...   # optional, needed for webhook verification
STATIC_DIR=../../client/html
DOMAIN=http://localhost:4242
```

### 2. Install dependencies

```bash
cd custom-payment-flow/server/python
pip install -r requirements.txt
```

### 3. Start the server

```bash
python server.py
# Listening on http://localhost:4242
```

Verify:

```bash
curl http://localhost:4242/config
# {"publishableKey":"pk_test_..."}
```

### 4. (Optional) Forward webhooks locally

```bash
stripe listen --forward-to localhost:4242/webhook
```

Copy the printed `whsec_...` value into `.env` as `STRIPE_WEBHOOK_SECRET`.

---

## Supported payment methods

| Payment Method | Currency | Notes |
|---|---|---|
| Card | Any | Test card: `4242 4242 4242 4242` |
| ACSS Debit | CAD | Requires mandate options |
| BECS Direct Debit | AUD | Account must be in AU |
| SEPA Direct Debit | EUR | |
| Bancontact | EUR | Redirect flow |
| iDEAL | EUR | Bank selection required |
| Afterpay / Clearpay | USD/AUD/GBP/CAD/NZD | Redirect flow |
| OXXO | MXN | Account must be in MX |
| Alipay | Multiple | Redirect flow |
| Apple Pay | Any | Requires HTTPS + domain verification |
| Google Pay | Any | Requires HTTPS |
| GrabPay | SGD/MYR | Account must be in SG or MY |

---

## Testing this app

Tests live in the [fintech-playwright-quality](https://github.com/summerduck/fintech-playwright-quality) repository. That repo contains the full AI-augmented test suite (Playwright + pytest + Claude API agents) that runs against this application.

For the built-in RSpec API and E2E tests that ship with this sample, see [SETUP_AND_TROUBLESHOOTING.md](./SETUP_AND_TROUBLESHOOTING.md).

---

## Related

- [fintech-playwright-quality](https://github.com/summerduck/fintech-playwright-quality) — test framework repo (Playwright, pytest, Claude API)
- [Stripe custom payment flow docs](https://stripe.com/docs/payments/accept-card-payments?platform=web&ui=elements)
- [Stripe test cards](https://stripe.com/docs/testing#cards)
