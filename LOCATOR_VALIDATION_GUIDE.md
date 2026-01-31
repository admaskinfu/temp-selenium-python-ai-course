# Locator Validation Guide

## What Went Wrong

**Mistake:** I assumed the cart page used a traditional WooCommerce table structure (`tbody tr.cart_item td.product-name`) without actually validating it on the live site.

**Reality:** The site uses WooCommerce **block-based cart** structure, which has a completely different HTML structure.

## How to Avoid This in the Future

### 1. **Always Validate Locators on Live Site**
   - Never assume HTML structure matches common patterns
   - Use browser developer tools or validation scripts
   - Test locators before committing changes

### 2. **Use Validation Scripts**
   ```python
   # Create a simple validation script
   from selenium import webdriver
   from selenium.webdriver.common.by import By
   
   driver = webdriver.Chrome()
   driver.get("http://your-site.com/page")
   
   # Test your locator
   elements = driver.find_elements(By.CSS_SELECTOR, 'your-locator')
   print(f"Found {len(elements)} elements")
   for elem in elements:
       print(f"Text: {elem.text}")
   ```

### 3. **Inspect Actual HTML Structure**
   - Use browser DevTools (F12)
   - Right-click element → Inspect
   - Check actual class names and structure
   - Don't rely on documentation or assumptions

### 4. **Test Multiple Locator Options**
   - Create a list of potential locators
   - Test each one and see which works
   - Choose the most reliable and specific one

### 5. **Handle Multiple Page Structures**
   - Some sites use different structures (block-based vs traditional)
   - Use flexible locators that work with both: `[class*="product-name"]`
   - Or use multiple selectors with comma: `selector1, selector2`

## Validation Checklist

Before committing locator changes:
- [ ] Test locator on actual live site
- [ ] Verify it finds the expected number of elements
- [ ] Check that it works with actual data (not empty state)
- [ ] Test edge cases (empty cart, multiple items, etc.)
- [ ] Verify locator is specific enough (doesn't match unintended elements)

## Corrected Locators

### Cart Items
- **Wrong:** `tbody tr.cart_item td.product-name`
- **Correct:** `[class*="product-name"]`
- **Why:** Works with both block-based and traditional WooCommerce carts

### Error Messages  
- **Correct:** `ul.woocommerce-error li, div.woocommerce-notices-wrapper ul.woocommerce-error li`
- **Validated:** ✅ Works

### Product Images
- **Correct:** `div.woocommerce-product-gallery__image img, div.woocommerce-product-gallery figure img, div.woocommerce-product-gallery img`
- **Validated:** ✅ Works (handles lazy loading)
