# 🎨 Flaky Test Combat Handbook with Playwright - Infographic

## 📋 Infographic Content (English Version)

---

<div style="display: flex; justify-content: space-between; gap: 20px; margin: 20px 0;">

<div style="flex: 1; background: #e3f2fd; padding: 20px; border-radius: 10px;">

## 🚨 PROBLEM: WHAT IS A FLAKY TEST?

### Definition
**Flaky Test** = A test that runs randomly: sometimes passes, sometimes fails without changing code.

🎭 **Visual**: Two theatrical masks - one red (sad/failing) ❌ and one green (happy/passing) ✅ with a question mark above them, indicating inconsistency.

---

### 🔍 5 Common Causes

#### 1. ⏱️ Insufficient Timing
**Visual**: ⏳ Hourglass with clock hand  
**Text**: "Timing không đủ" → **"Insufficient Timing"**

#### 2. 📊 Dynamic Data Dependency
**Visual**: 📦 Three rectangular blocks with arrows showing movement + ☁️ cloud icon  
**Text**: "Phụ thuộc dữ liệu động" → **"Dynamic Data Dependency"**

#### 3. ⚡ Race Condition
**Visual**: 🏁 Two arrows racing towards finish line (red vs green)  
**Text**: **"Race Condition"**

#### 4. 🧹 Unclean State
**Visual**: 🧪 Beaker with bubbling green liquid  
**Text**: "State không sạch" → **"Unclean State"**

#### 5. 🌐 External API
**Visual**: ☁️🔒 Cloud icon with padlock  
**Text**: "API bên ngoài" → **"External API"**

---

### 📈 Symptoms

#### 1. Rerun Behavior
🔄 **"Rerun" passes, CI/CD is sometimes green, sometimes red.**

**Visual**: Circular arrow (rerun) with green checkmark → Three rectangular blocks alternating: 🟢 Green → 🔴 Red → 🟢 Green (CI/CD pipeline status)

#### 2. Impact on Team
😫 **Causes difficult debugging, reduces confidence in test results and CI/CD.**

**Visual**: Three gear icons connected:
- Gear 1: ✅ Green checkmark
- Gear 2: ❌ Red X
- Gear 3: ❌ Red X

Below: Distressed red and green masks 😢

#### 3. Environment Difference
💻 **Tests run stably locally but fail in CI environment.**

**Visual**: 
- 💻 Computer monitor with ✅ green checkmark (local success)
- → Arrow →
- ☁️ Cloud icon with ❌ red X (CI failure)

</div>

<div style="flex: 1; background: #e8f5e9; padding: 20px; border-radius: 10px;">

## ✅ SOLUTION: HANDLING WITH PLAYWRIGHT

### 🎯 Use Auto-Wait Mechanism

**Visual Comparison**:
```
❌ waitForTimeout          VS          ✅ expect(locator).toBeVisible()
   ⏰ Clock with X                        🔍 Magnifying glass with checkmark
```

**Text**: Replace `waitForTimeout` with smart wait commands like `expect(locator).toBeVisible()`.

---

### 🎭 Mock API to Eliminate Dependencies

**Visual Flow**:
```
❌ Broken Wi-Fi → ☁️ API (avoid)
         ↓
✅ Code </> → page.route() → ☁️ API → 😊 Happy face
```

**Text**: Use `page.route()` to mock API responses, helping tests run faster and more stably.

**Configuration**:
```javascript
await page.route('/api/endpoint', route => {
  route.fulfill({
    status: 200,
    body: JSON.stringify({ success: true })
  });
});
```

---

### 📹 Record "Trace" to Debug When Tests Fail

**Visual Flow**:
```
🔴 FAIL Button
    ↓
📊 Detailed Timeline View
    ↓
🔍 Magnifying Glass highlighting specific point
```

**Text**: Configure `trace: "on-first-retry"` to review each test step like a video.

**Configuration**:
```javascript
// playwright.config.ts
export default defineConfig({
  use: {
    trace: 'on-first-retry',  // 🔄 Three interconnected circles icon
    video: 'retain-on-failure',
    screenshot: 'only-on-failure'
  }
});
```

**Visual Icon**: 🔄 Three interconnected circles labeled `trace: 'on-first-retry'`

---

### 🎯 Additional Best Practices

#### 1. Use Stable Locators
```javascript
// ❌ Bad
await page.click('.btn.btn-primary.ml-2');

// ✅ Good
await page.getByRole('button', { name: 'Login' }).click();
await page.getByTestId('submit-button').click();
```

#### 2. Clean State Before Each Test
```javascript
test.beforeEach(async ({ page }) => {
  await page.context().clearCookies();
  await page.evaluate(() => localStorage.clear());
});
```

#### 3. Appropriate Timeouts
```javascript
await expect(page.getByText('Success'))
  .toBeVisible({ timeout: 10000 });
```

</div>

</div>

---

## 📊 Summary Table

| Problem | Solution |
|---------|----------|
| ⏱️ Insufficient Timing | ✅ Use Auto-Wait (`expect().toBeVisible()`) |
| 📊 Dynamic Data Dependency | ✅ Mock API with `page.route()` |
| ⚡ Race Condition | ✅ Sequential execution, proper async handling |
| 🧹 Unclean State | ✅ Clean state in `beforeEach` |
| 🌐 External API | ✅ Mock external services |

---

## 🎨 Visual Design Elements

- **Color Scheme**: Light blue (#e3f2fd) for Problem section, Light green (#e8f5e9) for Solution section
- **Icons**: 
  - 🎭 Masks (pass/fail inconsistency)
  - ⏳ Hourglass (timing)
  - 📦 Blocks (data)
  - 🏁 Race flags (race condition)
  - 🧪 Beaker (state)
  - ☁️ Cloud (API)
  - 🔄 Arrows (rerun/flow)
  - 🔍 Magnifying glass (debugging)
  - ✅❌ Checkmarks and X marks

---

## 📝 Key Takeaways

1. **Flaky tests** are tests that randomly pass/fail without code changes
2. **5 main causes**: Timing, Dynamic Data, Race Conditions, Unclean State, External APIs
3. **Playwright solutions**: Auto-wait, API mocking, Trace recording
4. **Best practices**: Stable locators, clean state, appropriate timeouts

---

## 🔗 Related Resources

- See [Flaky Test Guide](./Flaky_Test_Guide.md) for detailed documentation
- Playwright Documentation: [Auto-waiting](https://playwright.dev/docs/actionability)
- Playwright Documentation: [Network Interception](https://playwright.dev/docs/network)

