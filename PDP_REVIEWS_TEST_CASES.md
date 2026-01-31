# PDP Reviews – Test Cases (Future Suite)

This document describes test cases for a **dedicated Reviews suite** on the Product Detail Page. These are **not implemented** in the current Beanie PDP suite; they are intended for a separate session where reviews are added via UI and API, with edge cases (count, ratings, display).

**Scope:** Reviews tab content, submission via UI, creation via API, display of ratings and count, edge cases (empty, one, many, different star ratings).

**Suggested suite location:** e.g. `ssqatest/tests/product_detail_page/test_pdp_reviews.py` or `test_beanie_pdp_reviews.py` (or a `reviews/` subfolder).

---

## 1. Add review via UI (customer flow)

| ID (suggested) | Title | Description | Priority | Steps | Expected |
|----------------|--------|-------------|----------|--------|----------|
| TC-REV-01 | Submit review via UI | Customer can submit a review (rating + comment + name + email) and see it displayed. | High | 1. Navigate to PDP (e.g. Beanie). 2. Open Reviews tab. 3. Fill rating, review text, name, email. 4. Submit. 5. Reload or wait for display. | Review appears in list with correct rating, text, author; rating count updates. |
| TC-REV-02 | Review form validation | Required fields (rating, review text, name, email) are enforced. | Medium | 1. Open Reviews tab. 2. Submit with missing fields. | Validation messages shown; review not submitted. |
| TC-REV-03 | "Be the first to review" CTA | When there are no reviews, CTA/form for first review is visible. | Low | 1. Navigate to PDP with 0 reviews. 2. Open Reviews tab. | "There are no reviews yet" and "Be the first to review" (or equivalent) visible. |

---

## 2. Add review via API and verify display

| ID (suggested) | Title | Description | Priority | Steps | Expected |
|----------------|--------|-------------|----------|--------|----------|
| TC-REV-04 | Create review via API – display on PDP | Create a product review via WooCommerce API; verify it appears on PDP Reviews tab. | High | 1. Create review for product (e.g. Beanie) via API (rating, content, reviewer). 2. Navigate to PDP. 3. Open Reviews tab. | Review text, rating, and reviewer (if shown) match API payload. |
| TC-REV-05 | Rating count matches API | After creating N reviews via API, Reviews tab label and count match. | Medium | 1. Create reviews via API for a product. 2. Navigate to PDP. 3. Check tab label and review list. | Tab shows "Reviews (N)" and list shows N reviews. |

---

## 3. Edge cases: count and display

| ID (suggested) | Title | Description | Priority | Steps | Expected |
|----------------|--------|-------------|----------|--------|----------|
| TC-REV-06 | Zero reviews – empty state | PDP with no reviews shows correct empty state. | Medium | 1. Ensure product has 0 reviews (API). 2. Open PDP Reviews tab. | "There are no reviews yet" (or equivalent); no review list; form/CTA present. |
| TC-REV-07 | One review – display | Single review displays correctly (rating, text, author). | Medium | 1. Ensure product has exactly 1 review (API). 2. Open PDP Reviews tab. | One review visible; rating and content match API. |
| TC-REV-08 | Many reviews – list/pagination | Product with many reviews (e.g. 10+) shows list and optionally pagination. | Low | 1. Create many reviews via API. 2. Open PDP Reviews tab. | All (or paginated) reviews visible; count correct. |

---

## 4. Different star ratings – display

| ID (suggested) | Title | Description | Priority | Steps | Expected |
|----------------|--------|-------------|----------|--------|----------|
| TC-REV-09 | 5-star review display | Review with 5-star rating shows 5 stars on PDP. | Medium | 1. Create 5-star review via API. 2. Open PDP Reviews tab. | UI shows 5 filled stars (or "5" rating). |
| TC-REV-10 | 4-star review display | Review with 4-star rating shows 4 stars on PDP. | Medium | 1. Create 4-star review via API. 2. Open PDP Reviews tab. | UI shows 4 filled stars (or "4" rating). |
| TC-REV-11 | 3-star review display | Review with 3-star rating shows 3 stars on PDP. | Medium | 1. Create 3-star review via API. 2. Open PDP Reviews tab. | UI shows 3 filled stars (or "3" rating). |
| TC-REV-12 | Mixed ratings – average/count | Product with mixed ratings (e.g. 3, 4, 5) shows correct average and count. | High | 1. Create reviews with different ratings via API. 2. Open PDP. | Average rating and "Reviews (N)" match API/catalog. |

---

## 5. Implementation notes (for future session)

- **API:** Use WooCommerce REST API (or existing `api_helpers`) to create product reviews (POST product reviews endpoint) and optionally to clear/reset reviews for test data.
- **Cleanup:** Prefer test data setup/teardown (e.g. create review in setup, delete or use dedicated test product in teardown) so main catalog is not polluted.
- **Source of truth:** Assert displayed rating, count, and content against API response or known test data; avoid hard-coding site copy if it can vary.
- **Product:** Beanie (or a dedicated test product) can be used so Reviews suite is stable and independent of other PDP tests.

---

*Document created for implementation in a future session. No tests are implemented in the current Beanie PDP suite.*
