# 🚀 Fikisha API

### One API. Every delivery provider in Africa.

Integrate once — access Uber Direct, Pickup Mtaani, Glovo, and more
through a single RESTful endpoint. Compare prices, book deliveries,
and track in real time.

[Get API Key](https://Fikisha.com/signup) •
[API Reference](#api-reference) •
[Quick Start](#quick-start) •
[Webhooks](#webhooks) •
[Status](https://status.Fikisha.com)

---

![Version](https://img.shields.io/badge/API%20Version-v1-blue)
![Status](https://img.shields.io/badge/Status-Beta-orange)
![Coverage](https://img.shields.io/badge/Coverage-Nairobi-green)
![License](https://img.shields.io/badge/License-MIT-green)

</div>

---

## What is Fikisha?

Fikisha is a **delivery aggregation API** — a single integration point
that connects your application to multiple last-mile delivery providers
across Africa.

Instead of building and maintaining separate integrations with Uber Direct,
Pickup Mtaani, Glovo, and every new provider that launches, you integrate
with Fikisha **once** and get access to all of them through a unified
interface.

```
┌─────────────────────┐
│                     │
│   Your App / POS    │
│   / Website / Bot   │
│                     │
└─────────┬───────────┘
          │
     ONE API CALL
          │
          ▼
┌─────────────────────┐
│                     │
│   Fikisha API      │──── Compares price, speed, availability
│                     │     across all providers
└──┬──────┬───────┬───┘
   │      │       │
   ▼      ▼       ▼
┌─────┐┌──────┐┌──────┐
│Uber ││Pickup││Glovo │ ... more coming
│Dirct││Mtaani││      │
└─────┘└──────┘└──────┘
   │      │       │
   ▼      ▼       ▼
 Their riders/network
 handle the delivery
```

**You deal with one API. We deal with all the providers.**

---

## Why Fikisha?

| Without Fikisha | With Fikisha |
|---|---|
| Research each provider's API docs | Read one set of docs |
| Build integration per provider (weeks each) | One integration (~1 hour) |
| Handle different auth methods per provider | One API key |
| Map different status names per provider | One unified status model |
| Build comparison logic yourself | We compare and rank for you |
| Maintain integrations when providers change | We maintain everything |
| New provider launches? Build again. | New provider? You get it automatically |
| Different webhook formats per provider | One consistent webhook format |

---

## Coverage

### Currently Available

| City | Country | Providers | Status |
|------|---------|-----------|--------|
| Nairobi | Kenya 🇰🇪 | Uber Direct, Pickup Mtaani | ✅ Live |

### Coming Soon

| City | Country | Target |
|------|---------|--------|
| Mombasa | Kenya 🇰🇪 | Q2 2026 |
| Kisumu | Kenya 🇰🇪 | Q2 2026 |
| Kampala | Uganda 🇺🇬 | Q3 2026 |
| Dar es Salaam | Tanzania 🇹🇿 | Q3 2026 |
| Kigali | Rwanda 🇷🇼 | Q4 2026 |
| Johannesburg | South Africa 🇿🇦 | Q4 2026 |
| Lagos | Nigeria 🇳🇬 | 2027 |

Want us in your city? [Let us know](https://fikisha.com/request-city).

---

## Quick Start

### 1. Get your API key

Sign up at [Fikisha.com/signup](https://fikisha.com/signup).
You'll get:

```
API Key:    dva_live_xxxxxxxxxxxxxx
API Secret: dvs_xxxxxxxxxxxxxx
```

### 2. Get delivery quotes

```bash
curl -X POST https://api.fikisha.com/v1/quotes \
  -H "Authorization: Bearer dva_live_xxxxxxxxxxxxxx" \
  -H "Content-Type: application/json" \
  -d '{
    "pickup": {
      "lat": -1.2635,
      "lng": 36.8083,
      "address": "Sarit Centre, Westlands, Nairobi",
      "contact_name": "John",
      "contact_phone": "+254712345678"
    },
    "dropoff": {
      "lat": -1.2921,
      "lng": 36.8219,
      "address": "Yaya Centre, Kilimani, Nairobi",
      "contact_name": "Mary",
      "contact_phone": "+254798765432"
    },
    "package": {
      "size": "small",
      "description": "Phone accessories"
    }
  }'
```

Response:

```json
{
  "request_id": "req_1a2b3c4d",
  "quotes": [
    {
      "quote_id": "qt_abc123",
      "provider": "uber_direct",
      "provider_name": "Uber Direct",
      "delivery_type": "door_to_door",
      "price": {
        "amount": 290,
        "currency": "KES"
      },
      "estimated_duration_minutes": 30,
      "estimated_pickup_minutes": 8,
      "vehicle_type": "motorcycle",
      "rating": 4.5,
      "expires_at": "2026-01-15T10:30:00Z"
    },
    {
      "quote_id": "qt_def456",
      "provider": "pickup_mtaani",
      "provider_name": "Pickup Mtaani",
      "delivery_type": "pickup_point",
      "price": {
        "amount": 95,
        "currency": "KES"
      },
      "estimated_duration_minutes": 240,
      "vehicle_type": "network",
      "pickup_point": {
        "name": "Mama Njeri Shop",
        "address": "Tom Mboya Street, Nairobi",
        "lat": -1.2855,
        "lng": 36.8260
      },
      "expires_at": "2026-01-15T10:30:00Z"
    }
  ],
  "recommended": "qt_abc123",
  "recommendation_reason": "fastest"
}
```

### 3. Book the delivery

```bash
curl -X POST https://api.Fikisha.com/v1/deliveries \
  -H "Authorization: Bearer dva_live_xxxxxxxxxxxxxx" \
  -H "Content-Type: application/json" \
  -d '{
    "quote_id": "qt_abc123",
    "metadata": {
      "order_id": "your-internal-order-123"
    }
  }'
```

Response:

```json
{
  "delivery_id": "del_x7y8z9",
  "status": "searching",
  "provider": "uber_direct",
  "provider_name": "Uber Direct",
  "price": {
    "amount": 290,
    "currency": "KES"
  },
  "tracking_url": "https://track.Fikisha.com/del_x7y8z9",
  "pickup": {
    "address": "Sarit Centre, Westlands, Nairobi",
    "contact_name": "John",
    "estimated_at": "2026-01-15T10:22:00Z"
  },
  "dropoff": {
    "address": "Yaya Centre, Kilimani, Nairobi",
    "contact_name": "Mary",
    "estimated_at": "2026-01-15T10:50:00Z"
  },
  "created_at": "2026-01-15T10:10:00Z"
}
```

### 4. Track the delivery

```bash
curl https://api.Fikisha.com/v1/deliveries/del_x7y8z9 \
  -H "Authorization: Bearer dva_live_xxxxxxxxxxxxxx"
```

Or share the tracking URL with your customer:
```
https://track.Fikisha.com/del_x7y8z9
```

### 5. Receive status updates via webhooks

Configure your webhook URL in [your dashboard](https://Fikisha.com/dashboard/webhooks).
We'll send `POST` requests to your URL as the delivery progresses:

```json
{
  "event": "delivery.picked_up",
  "delivery_id": "del_x7y8z9",
  "status": "picked_up",
  "rider": {
    "name": "James K.",
    "phone": "+254711111111",
    "vehicle": "motorcycle",
    "location": {
      "lat": -1.2635,
      "lng": 36.8083
    }
  },
  "occurred_at": "2026-01-15T10:23:00Z",
  "metadata": {
    "order_id": "your-internal-order-123"
  }
}
```

**That's it. You're delivering.** 🎉

---

## API Reference

### Base URL

```
Production:  https://api.Fikisha.com/v1
Sandbox:     https://sandbox.Fikisha.com/v1
```

The sandbox environment uses simulated providers. No real deliveries
are created. Use it to build and test your integration.

### Authentication

All requests require a Bearer token in the `Authorization` header:

```
Authorization: Bearer dva_live_xxxxxxxxxxxxxx
```

Sandbox keys start with `dva_test_`, live keys start with `dva_live_`.

---

### Quotes

#### `POST /v1/quotes`

Get delivery quotes from all available providers for a given route.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `pickup` | object | ✅ | Pickup location details |
| `pickup.lat` | number | ✅ | Pickup latitude |
| `pickup.lng` | number | ✅ | Pickup longitude |
| `pickup.address` | string | ✅ | Human-readable address |
| `pickup.contact_name` | string | ✅ | Name of person at pickup |
| `pickup.contact_phone` | string | ✅ | Phone (E.164 format: +254...) |
| `pickup.notes` | string | — | Instructions for rider |
| `dropoff` | object | ✅ | Dropoff location details |
| `dropoff.lat` | number | ✅ | Dropoff latitude |
| `dropoff.lng` | number | ✅ | Dropoff longitude |
| `dropoff.address` | string | ✅ | Human-readable address |
| `dropoff.contact_name` | string | ✅ | Name of person at dropoff |
| `dropoff.contact_phone` | string | ✅ | Phone (E.164 format) |
| `dropoff.notes` | string | — | Instructions for rider |
| `package` | object | ✅ | Package details |
| `package.size` | string | ✅ | `small` \| `medium` \| `large` \| `xlarge` |
| `package.description` | string | — | What's being delivered |
| `package.weight_kg` | number | — | Weight in kilograms |
| `package.fragile` | boolean | — | Requires careful handling |
| `preferences` | object | — | Optional preferences |
| `preferences.vehicle_type` | string | — | `motorcycle` \| `car` \| `van` \| `any` |
| `preferences.providers` | string[] | — | Only query specific providers |
| `preferences.max_price` | number | — | Maximum price in KES |
| `preferences.sort_by` | string | — | `price` \| `speed` \| `rating` (default: `price`) |

**Package Sizes:**

| Size | Description | Max Weight | Example |
|------|-------------|------------|---------|
| `small` | Fits in one hand | 5 kg | Documents, phone, small box |
| `medium` | Backpack size | 15 kg | Laptop, shoes, medium box |
| `large` | Needs car trunk | 30 kg | Large box, small furniture |
| `xlarge` | Needs van | 50 kg+ | Appliances, large furniture |

**Response:**

```json
{
  "request_id": "req_xxxxx",
  "quotes": [
    {
      "quote_id": "qt_xxxxx",
      "provider": "provider_code",
      "provider_name": "Display Name",
      "delivery_type": "door_to_door | pickup_point",
      "price": {
        "amount": 290,
        "currency": "KES"
      },
      "estimated_duration_minutes": 30,
      "estimated_pickup_minutes": 8,
      "vehicle_type": "motorcycle",
      "rating": 4.5,
      "pickup_point": null,
      "expires_at": "ISO 8601 timestamp"
    }
  ],
  "recommended": "qt_xxxxx",
  "recommendation_reason": "fastest | cheapest | best_rated"
}
```

> **Note:** Quotes expire. The `expires_at` field tells you when.
> Typically 10-15 minutes. After that, request new quotes.

---

### Deliveries

#### `POST /v1/deliveries`

Book a delivery using a quote ID.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `quote_id` | string | ✅ | Quote ID from `/v1/quotes` response |
| `metadata` | object | — | Your custom data (returned in webhooks) |
| `metadata.order_id` | string | — | Your internal order reference |
| `metadata.*` | any | — | Any key-value pairs you want |

**Response:** `201 Created`

```json
{
  "delivery_id": "del_xxxxx",
  "status": "searching",
  "provider": "uber_direct",
  "provider_name": "Uber Direct",
  "delivery_type": "door_to_door",
  "price": {
    "amount": 290,
    "currency": "KES"
  },
  "tracking_url": "https://track.Fikisha.com/del_xxxxx",
  "pickup": {
    "address": "...",
    "contact_name": "...",
    "contact_phone": "...",
    "estimated_at": "ISO 8601"
  },
  "dropoff": {
    "address": "...",
    "contact_name": "...",
    "contact_phone": "...",
    "estimated_at": "ISO 8601"
  },
  "package": {
    "size": "small",
    "description": "..."
  },
  "rider": null,
  "proof_of_delivery": null,
  "metadata": {
    "order_id": "your-order-123"
  },
  "created_at": "ISO 8601",
  "updated_at": "ISO 8601"
}
```

---

#### `GET /v1/deliveries`

List your deliveries. Supports pagination and filtering.

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | int | Page number (default: 1) |
| `per_page` | int | Results per page (default: 20, max: 100) |
| `status` | string | Filter by status |
| `provider` | string | Filter by provider code |
| `from` | string | Filter from date (ISO 8601) |
| `to` | string | Filter to date (ISO 8601) |
| `order_id` | string | Filter by your metadata.order_id |

**Response:**

```json
{
  "data": [ ... ],
  "pagination": {
    "page": 1,
    "per_page": 20,
    "total": 156,
    "total_pages": 8
  }
}
```

---

#### `GET /v1/deliveries/:id`

Get full details of a specific delivery including current status,
rider info, and timeline.

**Response:**

```json
{
  "delivery_id": "del_xxxxx",
  "status": "in_transit",
  "provider": "uber_direct",
  "provider_name": "Uber Direct",
  "price": {
    "amount": 290,
    "currency": "KES"
  },
  "tracking_url": "https://track.Fikisha.com/del_xxxxx",
  "pickup": {
    "address": "Sarit Centre, Westlands",
    "contact_name": "John",
    "contact_phone": "+254712345678",
    "estimated_at": "2026-01-15T10:20:00Z",
    "actual_at": "2026-01-15T10:23:00Z"
  },
  "dropoff": {
    "address": "Yaya Centre, Kilimani",
    "contact_name": "Mary",
    "contact_phone": "+254798765432",
    "estimated_at": "2026-01-15T10:50:00Z",
    "actual_at": null
  },
  "rider": {
    "name": "James K.",
    "phone": "+254711111111",
    "vehicle_type": "motorcycle",
    "vehicle_plate": "KMXX 123A",
    "photo_url": "https://...",
    "location": {
      "lat": -1.2780,
      "lng": 36.8150,
      "updated_at": "2026-01-15T10:35:12Z"
    }
  },
  "proof_of_delivery": null,
  "timeline": [
    {
      "status": "pending",
      "occurred_at": "2026-01-15T10:10:00Z"
    },
    {
      "status": "searching",
      "occurred_at": "2026-01-15T10:10:02Z"
    },
    {
      "status": "assigned",
      "occurred_at": "2026-01-15T10:15:00Z",
      "detail": "James K. assigned"
    },
    {
      "status": "en_route_pickup",
      "occurred_at": "2026-01-15T10:15:30Z"
    },
    {
      "status": "picked_up",
      "occurred_at": "2026-01-15T10:23:00Z"
    },
    {
      "status": "in_transit",
      "occurred_at": "2026-01-15T10:23:30Z"
    }
  ],
  "metadata": {
    "order_id": "your-order-123"
  },
  "created_at": "2026-01-15T10:10:00Z",
  "updated_at": "2026-01-15T10:23:30Z"
}
```

---

#### `POST /v1/deliveries/:id/cancel`

Cancel a delivery. Can only cancel before `picked_up` status.
Cancellation fees may apply depending on the provider and timing.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `reason` | string | — | Reason for cancellation |

**Response:**

```json
{
  "delivery_id": "del_xxxxx",
  "status": "cancelled",
  "cancellation": {
    "cancelled_at": "2026-01-15T10:18:00Z",
    "reason": "Customer changed their mind",
    "fee": {
      "amount": 0,
      "currency": "KES"
    },
    "refund": {
      "amount": 290,
      "currency": "KES",
      "refunded_to": "wallet"
    }
  }
}
```

---

### Providers

#### `GET /v1/providers`

List all available delivery providers and their current status.

**Response:**

```json
{
  "providers": [
    {
      "code": "uber_direct",
      "name": "Uber Direct",
      "description": "On-demand motorcycle and car delivery via Uber's rider network",
      "delivery_types": ["door_to_door"],
      "vehicle_types": ["motorcycle", "car"],
      "coverage": ["nairobi"],
      "avg_rating": 4.5,
      "status": "operational",
      "typical_price_range": {
        "min": 150,
        "max": 600,
        "currency": "KES"
      },
      "typical_duration_range": {
        "min_minutes": 20,
        "max_minutes": 60
      }
    },
    {
      "code": "pickup_mtaani",
      "name": "Pickup Mtaani",
      "description": "Affordable delivery to local pickup points. Customer collects from nearby agent.",
      "delivery_types": ["pickup_point"],
      "vehicle_types": ["network"],
      "coverage": ["nairobi"],
      "avg_rating": 4.2,
      "status": "operational",
      "typical_price_range": {
        "min": 50,
        "max": 200,
        "currency": "KES"
      },
      "typical_duration_range": {
        "min_minutes": 120,
        "max_minutes": 480
      }
    }
  ]
}
```

---

### Coverage

#### `GET /v1/coverage`

Check if a route can be serviced by any provider.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `pickup_lat` | number | ✅ | Pickup latitude |
| `pickup_lng` | number | ✅ | Pickup longitude |
| `dropoff_lat` | number | ✅ | Dropoff latitude |
| `dropoff_lng` | number | ✅ | Dropoff longitude |

**Response:**

```json
{
  "serviceable": true,
  "providers_available": ["uber_direct", "pickup_mtaani"],
  "city": "nairobi",
  "estimated_distance_km": 5.2
}
```

---

### Wallet

#### `GET /v1/wallet/balance`

Check your prepaid wallet balance.

**Response:**

```json
{
  "balance": {
    "amount": 5430,
    "currency": "KES"
  },
  "last_topped_up": "2026-01-14T08:00:00Z"
}
```

---

#### `POST /v1/wallet/topup`

Initiate an M-Pesa STK push to top up your wallet.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `amount` | number | ✅ | Amount in KES (min: 100) |
| `phone` | string | ✅ | M-Pesa phone number (+254...) |

**Response:**

```json
{
  "topup_id": "top_xxxxx",
  "status": "stk_pushed",
  "amount": 1000,
  "currency": "KES",
  "phone": "+254712345678",
  "message": "Check your phone and enter M-Pesa PIN to complete"
}
```

---

## Webhooks

Webhooks notify your application in real time as a delivery progresses.
Configure your webhook URL in [your dashboard](https://Fikisha.com/dashboard/webhooks).

### Setup

1. Go to [Dashboard → Webhooks](https://Fikisha.com/dashboard/webhooks)
2. Enter your endpoint URL (must be HTTPS)
3. Copy your **webhook secret** (used to verify signatures)
4. Select which events you want to receive (or all)

### Webhook Payload

All webhooks are sent as `POST` requests with `Content-Type: application/json`.

```json
{
  "webhook_id": "wh_xxxxx",
  "event": "delivery.picked_up",
  "delivery_id": "del_x7y8z9",
  "status": "picked_up",
  "data": {
    "rider": {
      "name": "James K.",
      "phone": "+254711111111",
      "vehicle_type": "motorcycle",
      "location": {
        "lat": -1.2635,
        "lng": 36.8083
      }
    },
    "proof": {
      "photo_url": "https://..."
    }
  },
  "metadata": {
    "order_id": "your-order-123"
  },
  "occurred_at": "2026-01-15T10:23:00Z"
}
```

### Events

| Event | Description | Triggered When |
|-------|-------------|----------------|
| `delivery.created` | Delivery created | Delivery booked successfully |
| `delivery.searching` | Looking for rider | Sent to provider, searching |
| `delivery.assigned` | Rider assigned | Rider accepted the job |
| `delivery.en_route_pickup` | Rider heading to pickup | Rider is on the way to collect |
| `delivery.at_pickup` | Rider at pickup | Rider arrived at pickup location |
| `delivery.picked_up` | Package collected | Rider has the package |
| `delivery.in_transit` | On the way | Rider heading to dropoff |
| `delivery.at_dropoff` | Rider at dropoff | Rider arrived at destination |
| `delivery.delivered` | Delivered | Package delivered (includes POD) |
| `delivery.cancelled` | Cancelled | Delivery was cancelled |
| `delivery.failed` | Failed | Delivery attempt failed |
| `delivery.returned` | Returned | Package returned to sender |

### Delivery Completed Webhook

The `delivery.delivered` event includes proof of delivery:

```json
{
  "event": "delivery.delivered",
  "delivery_id": "del_x7y8z9",
  "status": "delivered",
  "data": {
    "proof_of_delivery": {
      "photo_url": "https://cdn.Fikisha.com/pod/xxxxx.jpg",
      "signature_url": "https://cdn.Fikisha.com/sig/xxxxx.png",
      "received_by": "Mary",
      "delivered_at": "2026-01-15T10:47:00Z"
    },
    "final_price": {
      "amount": 290,
      "currency": "KES"
    },
    "duration_minutes": 37
  },
  "metadata": {
    "order_id": "your-order-123"
  },
  "occurred_at": "2026-01-15T10:47:00Z"
}
```

### Verifying Webhook Signatures

Every webhook includes a signature header to verify it came from Fikisha:

```
X-Fikisha-Signature: sha256=a1b2c3d4e5...
X-Fikisha-Timestamp: 1705312020
```

**Verification (Node.js):**

```javascript
const crypto = require('crypto');

function verifyWebhook(payload, signatureHeader, timestampHeader, secret) {
  const expectedSignature = crypto
    .createHmac('sha256', secret)
    .update(`${timestampHeader}.${JSON.stringify(payload)}`)
    .digest('hex');

  const receivedSignature = signatureHeader.replace('sha256=', '');

  return crypto.timingSafeEqual(
    Buffer.from(expectedSignature),
    Buffer.from(receivedSignature)
  );
}
```

**Verification (Python):**

```python
import hmac
import hashlib
import json

def verify_webhook(payload: dict, signature_header: str, timestamp: str, secret: str) -> bool:
    expected = hmac.new(
        secret.encode(),
        f"{timestamp}.{json.dumps(payload)}".encode(),
        hashlib.sha256
    ).hexdigest()

    received = signature_header.replace("sha256=", "")

    return hmac.compare_digest(expected, received)
```

### Retry Policy

If your endpoint returns a non-2xx status code or times out (10 seconds),
we retry with exponential backoff:

| Attempt | Delay |
|---------|-------|
| 1st retry | 1 minute |
| 2nd retry | 5 minutes |
| 3rd retry | 30 minutes |
| 4th retry | 2 hours |
| 5th retry (final) | 12 hours |

After 5 failed attempts, the webhook is marked as failed.
You can view and retry failed webhooks from your dashboard.

---

## Delivery Statuses

Every delivery follows a consistent lifecycle regardless of provider:

```
pending ──► searching ──► assigned ──► en_route_pickup ──► at_pickup
                                                              │
                                                              ▼
delivered ◄── at_dropoff ◄── in_transit ◄───────────── picked_up

                    ┌── cancelled (before pickup)
                    ├── failed (delivery attempt failed)
                    └── returned (package returned to sender)
```

| Status | Description |
|--------|-------------|
| `pending` | Delivery created, being sent to provider |
| `searching` | Provider is looking for an available rider |
| `assigned` | Rider has been assigned |
| `en_route_pickup` | Rider is heading to the pickup location |
| `at_pickup` | Rider has arrived at pickup location |
| `picked_up` | Rider has collected the package |
| `in_transit` | Rider is on the way to dropoff |
| `at_dropoff` | Rider has arrived at dropoff location |
| `delivered` | Package has been delivered ✅ |
| `cancelled` | Delivery was cancelled |
| `failed` | Delivery attempt failed |
| `returned` | Package was returned to sender |

---

## Error Handling

All errors follow a consistent format:

```json
{
  "error": {
    "code": "QUOTE_EXPIRED",
    "message": "This quote has expired. Please request new quotes.",
    "status": 400
  }
}
```

### Error Codes

| HTTP Status | Code | Description |
|---|---|---|
| 400 | `INVALID_REQUEST` | Request body validation failed |
| 400 | `QUOTE_EXPIRED` | Quote has expired, request new quotes |
| 400 | `INVALID_PHONE` | Phone number format invalid (use +254...) |
| 400 | `PACKAGE_TOO_LARGE` | No provider supports this package size on this route |
| 400 | `CANCEL_NOT_ALLOWED` | Cannot cancel — already picked up |
| 401 | `UNAUTHORIZED` | Missing or invalid API key |
| 402 | `INSUFFICIENT_BALANCE` | Wallet balance too low for this delivery |
| 404 | `DELIVERY_NOT_FOUND` | Delivery ID does not exist |
| 404 | `QUOTE_NOT_FOUND` | Quote ID does not exist |
| 404 | `NOT_SERVICEABLE` | No providers cover this route |
| 409 | `DELIVERY_ALREADY_BOOKED` | This quote was already used to book |
| 429 | `RATE_LIMITED` | Too many requests — slow down |
| 500 | `PROVIDER_ERROR` | Upstream provider returned an error |
| 503 | `PROVIDER_UNAVAILABLE` | Provider is temporarily down |

---

## Rate Limits

| Plan | Requests/minute | Deliveries/month |
|------|-----------------|------------------|
| Free | 30 | 100 |
| Starter | 60 | 500 |
| Growth | 120 | 2,000 |
| Business | 300 | 10,000 |
| Enterprise | Custom | Unlimited |

Rate limit headers are included in every response:

```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 58
X-RateLimit-Reset: 1705312080
```

---

## SDKs & Plugins

### Official SDKs

| Language | Package | Status |
|----------|---------|--------|
| Node.js / TypeScript | `npm install Fikisha` | ✅ Available |
| Python | `pip install Fikisha` | ✅ Available |
| PHP | `composer require Fikisha/Fikisha-php` | 🔜 Coming soon |
| Go | `go get github.com/Fikisha/Fikisha-go` | 🔜 Coming soon |

### Node.js SDK

```bash
npm install Fikisha
```

```javascript
const Fikisha = require('Fikisha');

const client = new Fikisha('dva_live_xxxxxxxxxxxxxx');

// Get quotes
const { quotes } = await client.quotes.create({
  pickup: {
    lat: -1.2635,
    lng: 36.8083,
    address: 'Sarit Centre, Westlands',
    contact_name: 'John',
    contact_phone: '+254712345678',
  },
  dropoff: {
    lat: -1.2921,
    lng: 36.8219,
    address: 'Yaya Centre, Kilimani',
    contact_name: 'Mary',
    contact_phone: '+254798765432',
  },
  package: { size: 'small' },
});

// Book cheapest option
const delivery = await client.deliveries.create({
  quote_id: quotes[0].quote_id,
  metadata: { order_id: 'ORD-123' },
});

console.log(delivery.tracking_url);

// Get status
const status = await client.deliveries.get(delivery.delivery_id);
console.log(status.status); // 'in_transit'

// Cancel
await client.deliveries.cancel(delivery.delivery_id, {
  reason: 'Customer changed mind',
});

// List deliveries
const { data, pagination } = await client.deliveries.list({
  status: 'delivered',
  from: '2026-01-01',
  per_page: 50,
});
```

### Python SDK

```bash
pip install Fikisha
```

```python
from Fikisha import Fikisha

client = Fikisha("dva_live_xxxxxxxxxxxxxx")

# Get quotes
result = client.quotes.create(
    pickup={
        "lat": -1.2635,
        "lng": 36.8083,
        "address": "Sarit Centre, Westlands",
        "contact_name": "John",
        "contact_phone": "+254712345678",
    },
    dropoff={
        "lat": -1.2921,
        "lng": 36.8219,
        "address": "Yaya Centre, Kilimani",
        "contact_name": "Mary",
        "contact_phone": "+254798765432",
    },
    package={"size": "small"},
)

# Book
delivery = client.deliveries.create(
    quote_id=result.quotes[0].quote_id,
    metadata={"order_id": "ORD-123"},
)

print(delivery.tracking_url)
```

### E-Commerce Plugins

| Platform | Status |
|----------|--------|
| Shopify | 🔜 Coming Q2 2026 |
| WooCommerce | 🔜 Coming Q2 2026 |

---

## Code Examples

### Express.js — Webhook Handler

```javascript
const express = require('express');
const app = express();

app.post('/webhooks/Fikisha', express.json(), (req, res) => {
  const event = req.body;

  // Verify signature (recommended)
  // verifyWebhook(req.body, req.headers['x-Fikisha-signature'], ...)

  switch (event.event) {
    case 'delivery.assigned':
      console.log(`Rider ${event.data.rider.name} assigned`);
      // Update your order status
      break;

    case 'delivery.picked_up':
      console.log('Package picked up!');
      // Notify your customer
      break;

    case 'delivery.delivered':
      console.log('Delivered!', event.data.proof_of_delivery);
      // Mark order as fulfilled
      // Send confirmation to customer
      break;

    case 'delivery.failed':
      console.log('Delivery failed');
      // Handle failure — retry? refund?
      break;
  }

  res.status(200).json({ received: true });
});
```

### Django — Full Integration

```python
# views.py
import json
import requests

Fikisha_API_KEY = "dva_live_xxxxxxxxxxxxxx"
Fikisha_BASE = "https://api.Fikisha.com/v1"
HEADERS = {
    "Authorization": f"Bearer {Fikisha_API_KEY}",
    "Content-Type": "application/json",
}

def create_delivery_for_order(order):
    """Called when customer places an order and wants delivery."""

    # Step 1: Get quotes
    quotes_response = requests.post(
        f"{Fikisha_BASE}/quotes",
        headers=HEADERS,
        json={
            "pickup": {
                "lat": order.shop.lat,
                "lng": order.shop.lng,
                "address": order.shop.address,
                "contact_name": order.shop.name,
                "contact_phone": order.shop.phone,
            },
            "dropoff": {
                "lat": order.delivery_lat,
                "lng": order.delivery_lng,
                "address": order.delivery_address,
                "contact_name": order.customer_name,
                "contact_phone": order.customer_phone,
            },
            "package": {
                "size": "small",
                "description": f"Order #{order.id}",
            },
        },
    )

    quotes = quotes_response.json()["quotes"]

    if not quotes:
        raise Exception("No delivery options available for this route")

    # Step 2: Book cheapest option
    cheapest = min(quotes, key=lambda q: q["price"]["amount"])

    delivery_response = requests.post(
        f"{Fikisha_BASE}/deliveries",
        headers=HEADERS,
        json={
            "quote_id": cheapest["quote_id"],
            "metadata": {"order_id": str(order.id)},
        },
    )

    delivery = delivery_response.json()

    # Step 3: Save to your database
    order.delivery_id = delivery["delivery_id"]
    order.delivery_tracking_url = delivery["tracking_url"]
    order.delivery_price = delivery["price"]["amount"]
    order.delivery_status = "searching"
    order.save()

    return delivery


# Webhook handler
@csrf_exempt
def Fikisha_webhook(request):
    event = json.loads(request.body)

    order = Order.objects.get(
        delivery_id=event["delivery_id"]
    )

    order.delivery_status = event["status"]

    if event["event"] == "delivery.delivered":
        order.is_fulfilled = True
        order.fulfilled_at = event["occurred_at"]
        # Send customer confirmation SMS/email

    order.save()

    return JsonResponse({"received": True})
```

### React — Show Delivery Options to Customer

```jsx
import { useState } from 'react';

function DeliveryOptions({ cartItems, pickupLocation, dropoffLocation }) {
  const [quotes, setQuotes] = useState([]);
  const [loading, setLoading] = useState(false);
  const [booked, setBooked] = useState(null);

  const getQuotes = async () => {
    setLoading(true);
    const res = await fetch('/api/delivery-quotes', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ pickup: pickupLocation, dropoff: dropoffLocation }),
    });
    const data = await res.json();
    setQuotes(data.quotes);
    setLoading(false);
  };

  const bookDelivery = async (quoteId) => {
    const res = await fetch('/api/book-delivery', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ quote_id: quoteId }),
    });
    const data = await res.json();
    setBooked(data);
  };

  return (
    <div>
      <h3>Delivery Options</h3>
      <button onClick={getQuotes} disabled={loading}>
        {loading ? 'Getting quotes...' : 'Check Delivery Options'}
      </button>

      {quotes.map((quote) => (
        <div key={quote.quote_id} className="quote-card">
          <h4>{quote.provider_name}</h4>
          <p>KES {quote.price.amount}</p>
          <p>{quote.estimated_duration_minutes} minutes</p>
          <p>{quote.delivery_type === 'pickup_point'
            ? `Pickup from: ${quote.pickup_point.name}`
            : 'Door-to-door delivery'
          }</p>
          <button onClick={() => bookDelivery(quote.quote_id)}>
            Select
          </button>
        </div>
      ))}

      {booked && (
        <div className="booked">
          <p>Delivery booked!</p>
          <a href={booked.tracking_url}>Track your delivery</a>
        </div>
      )}
    </div>
  );
}
```

---

## Sandbox & Testing

Use the sandbox environment to test your integration without
creating real deliveries or being charged.

```
Base URL: https://sandbox.Fikisha.com/v1
API Key:  dva_test_xxxxxxxxxxxxxx (from your dashboard)
```

### Sandbox Behavior

- Quotes return simulated prices and ETAs
- Deliveries are created but no real rider is dispatched
- Status progresses automatically every 30 seconds:
  `searching → assigned → picked_up → in_transit → delivered`
- Webhooks are sent for each status change
- Wallet starts with KES 10,000 test balance
- M-Pesa top-ups are simulated (always succeed)

### Trigger Specific Scenarios

Use special dropoff addresses to test edge cases:

| Address contains | Behavior |
|---|---|
| `"test-fail"` | Delivery will fail after pickup |
| `"test-cancel"` | Delivery will be auto-cancelled |
| `"test-return"` | Delivery will be returned |
| `"test-slow"` | Status changes every 5 minutes |
| `"test-instant"` | All statuses fire within 10 seconds |
| (anything else) | Normal simulated delivery |

---

## Pricing

| Plan | Monthly Fee | Per Delivery | Deliveries Included | Support |
|------|-------------|-------------|---------------------|---------|
| **Free** | KES 0 | KES 0 | 100/month | Community |
| **Starter** | KES 2,000 | KES 0 | 500/month | Email |
| **Growth** | KES 8,000 | KES 0 | 2,000/month | Priority email |
| **Business** | KES 20,000 | KES 0 | 10,000/month | Dedicated |
| **Enterprise** | Custom | Custom | Unlimited | Dedicated + SLA |

> Delivery prices from providers are passed through with a small
> service fee included. You always see the final price in the quote.
> No hidden charges.

All plans include:
- Full API access
- All providers
- Webhooks
- Tracking URLs
- Sandbox environment
- API documentation

---

## Frequently Asked Questions

<details>
<summary><strong>Do I need to sign up with Uber/Glovo/etc separately?</strong></summary>

No. You only sign up with Fikisha. We handle all provider relationships
and integrations. You get one API key, one invoice, one dashboard.
</details>

<details>
<summary><strong>How do I pay for deliveries?</strong></summary>

You maintain a prepaid wallet with Fikisha. Top up via M-Pesa.
Each delivery is deducted from your wallet balance. We also support
monthly invoicing for Business and Enterprise plans.
</details>

<details>
<summary><strong>What happens if no provider is available?</strong></summary>

The `/v1/quotes` endpoint will return an empty `quotes` array.
The `/v1/coverage` endpoint lets you check in advance if a route
is serviceable. We recommend showing your customer an appropriate
message like "Delivery not available for this location."
</details>

<details>
<summary><strong>Can I choose which provider to use?</strong></summary>

Yes. The quotes response gives you all available options. You (or your
customer) choose which one to book. You can also filter providers in
the quote request using `preferences.providers`.
</details>

<details>
<summary><strong>What if a delivery fails?</strong></summary>

You'll receive a `delivery.failed` webhook. The delivery cost is
refunded to your wallet. You can then request new quotes and try
again with the same or different provider.
</details>

<details>
<summary><strong>Do you support cash on delivery (COD)?</strong></summary>

Coming soon. COD support is on our roadmap for Q3 2026.
</details>

<details>
<summary><strong>Can I use this for food delivery?</strong></summary>

Yes, though we recommend using providers that support fast delivery
(Uber Direct) and specifying appropriate handling instructions
in the package notes.
</details>

<details>
<summary><strong>Is there a tracking page I can share with customers?</strong></summary>

Yes. Every delivery includes a `tracking_url` that you can share
directly with your customer. It shows real-time status and rider
location — no login required.
</details>

<details>
<summary><strong>How do you make money?</strong></summary>

A small service fee is included in the delivery price shown in quotes.
The price you see is the final price you pay. No hidden markups.
</details>

<details>
<summary><strong>I want a provider added that you don't support yet.</strong></summary>

[Contact us](mailto:hello@Fikisha.com). If the provider has an API
(or is willing to build one), we can typically add them within 2-4 weeks.
</details>

---

## Changelog

### v1.0.0 — 2026-XX-XX (Beta Launch)

- Uber Direct integration (Nairobi)
- Pickup Mtaani integration (Nairobi)
- Quotes, Deliveries, Tracking endpoints
- Webhook system with retry
- M-Pesa wallet payments
- Sandbox environment
- Node.js and Python SDKs

---

## Support

- 📖 **Docs:** [docs.Fikisha.com](https://docs.Fikisha.com)
- 💬 **Discord:** [discord.gg/Fikisha](https://discord.gg/Fikisha)
- 🐛 **Issues:** [GitHub Issues](https://github.com/Fikisha/Fikisha-api/issues)
- ✉️ **Email:** hello@Fikisha.com
- 𝕏 **Twitter:** [@Fikisha](https://twitter.com/Fikisha)

---

## Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

Areas we especially need help:
- Provider adapters for new delivery services
- SDKs in additional languages (PHP, Go, Ruby, Java)
- E-commerce platform plugins
- Documentation improvements
- Translations

---

## License

MIT — see [LICENSE](LICENSE)

---

<div align="center">

**Built for Africa. By Africans.**

[Get Started](https://Fikisha.com/signup) •
[Documentation](https://docs.Fikisha.com) •
[Status](https://status.Fikisha.com)

</div>
```

---
