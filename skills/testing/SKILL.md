---
name: testing
description: Testing with Vitest, Jest, and Playwright. Use for unit tests, integration tests, E2E tests, and coverage reports.
allowed-tools: Bash, Read, Edit
user-invocable: true
---

# testing

Testing with Vitest (unit/integration) and Playwright (E2E).

## Vitest (Recommended for Vite/Next.js)

### Installation
```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

### vitest.config.ts
```typescript
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: ['./vitest.setup.ts'],
    include: ['**/*.{test,spec}.{js,ts,jsx,tsx}'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
    },
  },
})
```

### vitest.setup.ts
```typescript
import '@testing-library/jest-dom'
```

### Commands
```bash
npx vitest                    # Watch mode
npx vitest run                # Single run
npx vitest run --coverage     # With coverage
npx vitest run src/utils      # Specific folder
npx vitest run -t "test name" # Specific test
npx vitest --ui               # UI mode
```

### package.json scripts
```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage",
    "test:ui": "vitest --ui"
  }
}
```

## Writing Tests

### Basic Test
```typescript
import { describe, it, expect, beforeEach, afterEach, vi } from 'vitest'

describe('Calculator', () => {
  it('adds two numbers', () => {
    expect(1 + 2).toBe(3)
  })

  it('handles edge cases', () => {
    expect(0 + 0).toBe(0)
    expect(-1 + 1).toBe(0)
  })
})
```

### React Component Test
```typescript
import { render, screen, fireEvent } from '@testing-library/react'
import { describe, it, expect, vi } from 'vitest'
import { Button } from './Button'

describe('Button', () => {
  it('renders with text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByRole('button')).toHaveTextContent('Click me')
  })

  it('calls onClick when clicked', () => {
    const handleClick = vi.fn()
    render(<Button onClick={handleClick}>Click</Button>)

    fireEvent.click(screen.getByRole('button'))

    expect(handleClick).toHaveBeenCalledTimes(1)
  })

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click</Button>)
    expect(screen.getByRole('button')).toBeDisabled()
  })
})
```

### Async Test
```typescript
import { describe, it, expect, vi } from 'vitest'
import { fetchUser } from './api'

describe('API', () => {
  it('fetches user data', async () => {
    const user = await fetchUser(1)

    expect(user).toMatchObject({
      id: 1,
      name: expect.any(String),
    })
  })

  it('handles errors', async () => {
    await expect(fetchUser(-1)).rejects.toThrow('User not found')
  })
})
```

### Mocking
```typescript
import { vi, describe, it, expect, beforeEach } from 'vitest'
import { sendEmail } from './email'
import * as api from './api'

// Mock module
vi.mock('./api', () => ({
  fetchUser: vi.fn(),
}))

describe('Email', () => {
  beforeEach(() => {
    vi.clearAllMocks()
  })

  it('sends email to user', async () => {
    vi.mocked(api.fetchUser).mockResolvedValue({
      id: 1,
      email: 'test@example.com'
    })

    await sendEmail(1, 'Hello')

    expect(api.fetchUser).toHaveBeenCalledWith(1)
  })
})
```

### Spying
```typescript
import { vi, describe, it, expect } from 'vitest'

describe('Logger', () => {
  it('logs to console', () => {
    const spy = vi.spyOn(console, 'log')

    console.log('test message')

    expect(spy).toHaveBeenCalledWith('test message')
    spy.mockRestore()
  })
})
```

### Testing Hooks
```typescript
import { renderHook, act } from '@testing-library/react'
import { describe, it, expect } from 'vitest'
import { useCounter } from './useCounter'

describe('useCounter', () => {
  it('increments counter', () => {
    const { result } = renderHook(() => useCounter())

    expect(result.current.count).toBe(0)

    act(() => {
      result.current.increment()
    })

    expect(result.current.count).toBe(1)
  })
})
```

## Matchers

```typescript
// Equality
expect(value).toBe(expected)           // Strict equality
expect(value).toEqual(expected)        // Deep equality
expect(value).toStrictEqual(expected)  // Strict deep equality

// Truthiness
expect(value).toBeTruthy()
expect(value).toBeFalsy()
expect(value).toBeNull()
expect(value).toBeUndefined()
expect(value).toBeDefined()

// Numbers
expect(value).toBeGreaterThan(3)
expect(value).toBeLessThan(5)
expect(value).toBeCloseTo(0.3, 5)

// Strings
expect(value).toMatch(/pattern/)
expect(value).toContain('substring')

// Arrays
expect(array).toContain(item)
expect(array).toHaveLength(3)
expect(array).toContainEqual({ id: 1 })

// Objects
expect(obj).toHaveProperty('key')
expect(obj).toMatchObject({ a: 1 })

// Exceptions
expect(() => fn()).toThrow()
expect(() => fn()).toThrow('error message')
expect(promise).rejects.toThrow()

// Snapshots
expect(value).toMatchSnapshot()
expect(value).toMatchInlineSnapshot()

// DOM (with @testing-library/jest-dom)
expect(element).toBeInTheDocument()
expect(element).toBeVisible()
expect(element).toBeDisabled()
expect(element).toHaveTextContent('text')
expect(element).toHaveAttribute('href', '/path')
expect(element).toHaveClass('active')
```

## Playwright (E2E)

### Installation
```bash
npm init playwright@latest
```

### playwright.config.ts
```typescript
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    { name: 'firefox', use: { ...devices['Desktop Firefox'] } },
    { name: 'webkit', use: { ...devices['Desktop Safari'] } },
    { name: 'mobile', use: { ...devices['iPhone 14'] } },
  ],
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
})
```

### Commands
```bash
npx playwright test                    # Run all tests
npx playwright test --headed           # With browser UI
npx playwright test --ui               # Interactive UI mode
npx playwright test login.spec.ts      # Specific file
npx playwright test --project=chromium # Specific browser
npx playwright show-report             # View HTML report
npx playwright codegen                 # Generate tests
```

### E2E Test Example
```typescript
import { test, expect } from '@playwright/test'

test.describe('Authentication', () => {
  test('user can login', async ({ page }) => {
    await page.goto('/login')

    await page.fill('input[name="email"]', 'test@example.com')
    await page.fill('input[name="password"]', 'password123')
    await page.click('button[type="submit"]')

    await expect(page).toHaveURL('/dashboard')
    await expect(page.locator('h1')).toContainText('Welcome')
  })

  test('shows error for invalid credentials', async ({ page }) => {
    await page.goto('/login')

    await page.fill('input[name="email"]', 'wrong@example.com')
    await page.fill('input[name="password"]', 'wrongpass')
    await page.click('button[type="submit"]')

    await expect(page.locator('.error')).toBeVisible()
    await expect(page.locator('.error')).toContainText('Invalid credentials')
  })
})
```

### Page Object Model
```typescript
// e2e/pages/LoginPage.ts
import { Page, Locator } from '@playwright/test'

export class LoginPage {
  readonly page: Page
  readonly emailInput: Locator
  readonly passwordInput: Locator
  readonly submitButton: Locator

  constructor(page: Page) {
    this.page = page
    this.emailInput = page.locator('input[name="email"]')
    this.passwordInput = page.locator('input[name="password"]')
    this.submitButton = page.locator('button[type="submit"]')
  }

  async goto() {
    await this.page.goto('/login')
  }

  async login(email: string, password: string) {
    await this.emailInput.fill(email)
    await this.passwordInput.fill(password)
    await this.submitButton.click()
  }
}

// e2e/auth.spec.ts
import { test, expect } from '@playwright/test'
import { LoginPage } from './pages/LoginPage'

test('login with page object', async ({ page }) => {
  const loginPage = new LoginPage(page)
  await loginPage.goto()
  await loginPage.login('test@example.com', 'password')

  await expect(page).toHaveURL('/dashboard')
})
```

## Coverage

### Vitest Coverage
```bash
npx vitest run --coverage
```

### Coverage Thresholds (vitest.config.ts)
```typescript
export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 80,
        statements: 80,
      },
    },
  },
})
```

## CI/CD Integration

### GitHub Actions
```yaml
name: Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run test:run
      - run: npm run test:coverage

  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npm run test:e2e
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
```

## Best Practices

1. **Test behavior, not implementation**
2. **Use descriptive test names**
3. **Arrange-Act-Assert pattern**
4. **One assertion per test (when possible)**
5. **Mock external dependencies**
6. **Keep tests fast and isolated**
7. **Use fixtures for shared setup**
8. **Test edge cases and errors**
