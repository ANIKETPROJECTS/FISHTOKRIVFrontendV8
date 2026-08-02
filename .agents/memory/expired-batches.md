---
name: Expired batch handling
description: toProduct mapper auto-marks products "unavailable" when all inventory batches are expired.
---

- Filters `inventoryBatches` where `remainingTime !== "expired"` and `expiryDate` is later than the current time.
- If product had batches AND all are now expired → `effectiveStatus = "unavailable"` (overrides DB status) and `batchExpired = true`.
- Batch-based checkout must apply the same current-time filter before stock totals and FIFO deduction; the hourly database sync is not a checkout guard.

In `client/src/pages/storefront/Home.tsx` combo sections:
- Filter combos where any included product has `status === "unavailable"` — those combos are hidden.

**Why:** Expired batch products should automatically disappear from storefront without manual admin intervention.

**How to apply:** The "unavailable" status from this mechanism is indistinguishable from manually-set unavailable — this is intentional. If you need to differentiate, add a separate field (e.g., `batchExpired: true`).
