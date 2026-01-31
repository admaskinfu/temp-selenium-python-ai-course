# Locator-Related Test Failures

**Date:** January 24, 2026  
**Status:** Active Issues  
**Focus:** Tests failing due to locator/TimeoutException issues (excluding stale element issues which have been fixed)

---

## Quick Reference: Failing Tests

1. `test_expired_coupon_message` (test_verify_expired_coupon_message.py)
2. `test_end_to_end_checkout_guest_user` (test_end_to_end_checkout_guest_user.py)
3. `test_variable_product_page_verify_main_image` (test_product_detail_page_variable_product_smoke.py)
4. `test_variable_product_page_logo_dropdown_label` (test_product_detail_page_variable_product_smoke.py)
5. `test_variable_product_pdp_select_options_add_to_cart` (test_variable_product_add_to_cart.py)

---

## Summary

This document tracks tests that are failing due to **locator issues** - elements not being found within the timeout period. These failures indicate potential problems with:
- Incorrect CSS selectors
- Elements not present on the page
- Timing issues (page not fully loaded)
- Application behavior changes

---

## Failure Summary Table

| Test File | Test Function Name | Error Type | Locator | Root Cause | Impact | Priority |
|-----------|-------------------|------------|---------|------------|--------|----------|
| `test_verify_expired_coupon_message.py` | `test_expired_coupon_message` | `TimeoutException` | `'div.woocommerce-notices-wrapper ul.woocommerce-error'` | Error element not found after applying expired coupon | Medium - Cart functionality | Medium |
| `test_end_to_end_checkout_guest_user.py` | `test_end_to_end_checkout_guest_user` | `TimeoutException` | `'tr.cart_item td.product-name'` | Cart items not found after navigation to cart page | High - E2E flow broken | High |
| `test_product_detail_page_variable_product_smoke.py` | `test_variable_product_page_verify_main_image` | `TimeoutException` | `'div.woocommerce-product-gallery.images figure img.wp-post-image'` | Main product image element not found | Medium - Product page validation | Medium |
| `test_product_detail_page_variable_product_smoke.py` | `test_variable_product_page_logo_dropdown_label` | `TimeoutException` | `'table.variations tr th.label label[for="logo"]'` | Logo dropdown label element not found | Low - UI validation | Low |
| `test_variable_product_add_to_cart.py` | `test_variable_product_pdp_select_options_add_to_cart` | `TimeoutException` | `'tr.cart_item td.product-name'` | Cart items not found after adding product to cart | High - Add to cart flow | High |

---

## Detailed Analysis

### 1. Cart Item Locator Issues (2 tests - HIGH PRIORITY)

**Affected Tests:**
- `test_end_to_end_checkout_guest_user()` in `test_end_to_end_checkout_guest_user.py`
- `test_variable_product_pdp_select_options_add_to_cart()` in `test_variable_product_add_to_cart.py`

**Locator:**
```python
PRODUCT_NAMES_IN_CART = (By.CSS_SELECTOR, 'tr.cart_item td.product-name')
```

**Location:**
- `ssqatest/src/pages/locators/CartPageLocators.py:6`
- Used in: `CartPage.get_all_product_names_in_cart()` (line 25)

**Error Message:**
```
TimeoutException: Unable to find elements located by '('css selector', 'tr.cart_item td.product-name')', after timeout of 10
```

**Possible Causes:**
1. **Locator is incorrect** - The CSS selector may not match the actual HTML structure
2. **Timing issue** - Cart page may not be fully loaded when the locator is checked
3. **Application change** - The HTML structure for cart items may have changed
4. **Empty cart** - Cart may be empty when the test expects items

**Investigation Steps:**
1. Inspect the actual cart page HTML to verify the correct selector
2. Add explicit wait for cart items to be present before accessing
3. Verify cart items are actually added before navigating to cart
4. Check if the cart table structure has changed

**Recommended Fix:**
- Verify the locator matches current HTML structure
- Add explicit wait condition: wait for at least one cart item to be present
- Consider using a more specific locator or XPath if CSS selector is fragile

---

### 2. Error Message Element Locator (1 test - MEDIUM PRIORITY)

**Affected Test:**
- `test_expired_coupon_message()` in `test_verify_expired_coupon_message.py`

**Locator:**
```python
ERROR_BOX = (By.CSS_SELECTOR, 'div.woocommerce-notices-wrapper ul.woocommerce-error')
```

**Location:**
- `ssqatest/src/pages/locators/CartPageLocators.py:11`
- Used in: `CartPage.get_displayed_error()` (line 49)

**Error Message:**
```
TimeoutException: Element not found within timeout period
```

**Possible Causes:**
1. **Error element structure changed** - The error message may be displayed in a different structure
2. **Error not displayed** - The expired coupon may not trigger the expected error message
3. **Timing issue** - Error message may take longer to appear than the timeout allows
4. **Different error format** - Error may be displayed in a different container

**Investigation Steps:**
1. Verify the error message actually appears when applying an expired coupon
2. Inspect the HTML structure of the error message when it appears
3. Check if there are multiple error containers or different selectors
4. Verify the coupon is actually expired and triggers the error

**Recommended Fix:**
- Verify the locator matches the actual error message HTML structure
- Consider using a more generic locator that finds any error message
- Add explicit wait for error message to appear
- Check if error appears in a different location (e.g., inline vs. notice wrapper)

---

### 3. Product Image Locator (1 test - MEDIUM PRIORITY)

**Affected Test:**
- `test_variable_product_page_verify_main_image()` in `test_product_detail_page_variable_product_smoke.py`

**Locator:**
```python
PRODUCT_IMAGE_MAIN = (By.CSS_SELECTOR, 'div.woocommerce-product-gallery.images figure img.wp-post-image')
```

**Location:**
- `ssqatest/src/pages/locators/ProductPageLocators.py:7`
- Used in: `ProductPage.get_url_of_displayed_main_image()` (line 22)

**Error Message:**
```
TimeoutException: Element not found within timeout period
```

**Possible Causes:**
1. **Image lazy loading** - Image may use lazy loading and not be immediately available
2. **Locator too specific** - The CSS selector may be too restrictive
3. **Image structure changed** - The product gallery HTML structure may have changed
4. **Timing issue** - Image may load after the timeout period

**Investigation Steps:**
1. Inspect the actual product page HTML to verify the image selector
2. Check if images use lazy loading (data-src vs src attribute)
3. Verify the image element structure matches the locator
4. Add explicit wait for image to be loaded

**Recommended Fix:**
- Verify the locator matches current HTML structure
- Consider waiting for image to be loaded (check for 'src' or 'data-src' attribute)
- Use a more flexible locator if the structure varies
- Add explicit wait for image visibility

---

### 4. Logo Dropdown Label Locator (1 test - LOW PRIORITY)

**Affected Test:**
- `test_variable_product_page_logo_dropdown_label()` in `test_product_detail_page_variable_product_smoke.py`

**Locator:**
```python
VARIABLE_PRODUCT_LOGO_ATTRIBUTE_LABEL = (By.CSS_SELECTOR, 'table.variations tr th.label label[for="logo"]')
```

**Location:**
- `ssqatest/src/pages/locators/ProductPageLocators.py:22`
- Used in: `ProductPage.get_label_for_logo_attribute_dropdown()` (line 89)

**Error Message:**
```
TimeoutException: Element not found within timeout period
```

**Possible Causes:**
1. **Attribute mismatch** - The `for` attribute may not be "logo" (could be "attribute_logo" or similar)
2. **Structure change** - The variations table structure may have changed
3. **Product doesn't have logo attribute** - The test product may not have a logo attribute
4. **Timing issue** - Variations may load after page load

**Investigation Steps:**
1. Verify the test product actually has a logo attribute
2. Inspect the HTML to check the actual `for` attribute value
3. Check if variations load dynamically (AJAX)
4. Compare with color attribute label (which may work) to identify differences

**Recommended Fix:**
- Verify the `for` attribute value matches the actual HTML
- Check if logo attribute uses a different naming convention
- Add explicit wait for variations to be loaded
- Consider using a more flexible locator

---

## Common Patterns and Recommendations

### Pattern 1: Dynamic Content Loading
Many failures may be due to elements loading dynamically via JavaScript. Consider:
- Adding explicit waits for element presence/visibility
- Waiting for specific conditions (e.g., cart count > 0)
- Using WebDriverWait with appropriate expected conditions

### Pattern 2: Locator Fragility
CSS selectors may be too specific or brittle. Consider:
- Using more flexible selectors
- Adding fallback locators
- Using XPath if CSS selector is unreliable
- Verifying locators match current HTML structure

### Pattern 3: Timing Issues
Elements may not be ready when accessed. Consider:
- Increasing timeout for specific operations
- Adding explicit waits before accessing elements
- Waiting for page/component to be fully loaded

### Pattern 4: Application Changes
The application structure may have changed. Consider:
- Verifying current HTML structure matches locators
- Updating locators to match current application
- Adding defensive checks for element presence

---

## Next Steps

1. **High Priority:** Investigate cart item locator issues (2 tests)
   - Verify HTML structure on cart page
   - Test locator manually in browser console
   - Add explicit waits for cart items

2. **Medium Priority:** Fix error message and image locators (2 tests)
   - Verify error message HTML structure
   - Check image loading mechanism
   - Update locators if needed

3. **Low Priority:** Fix logo dropdown label locator (1 test)
   - Verify attribute naming
   - Check if product has logo attribute
   - Update locator if needed

---

## Notes

- Stale element issues have been fixed and are not included in this document
- All failures are `TimeoutException` indicating elements not found within 10-second timeout
- Tests may pass intermittently if timing is the issue
- Consider running tests multiple times to identify flaky tests vs. consistent failures
