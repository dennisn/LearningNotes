# TypeScript in Practice: Unit Tests with Jest

- Course URL: <https://app.pluralsight.com/ilx/video-courses/typescript-5-building-unit-tests-jest/course-overview>

## Understanding TS Application Testing with Jest

- Why testing
  - Prevent regression: unexpected changes
  - Simplify yet better QA
  - Handle corner cases
- Jest: can test both application logic (e.g. pure functionality with input/output, no UI) and UI components
- Need a configuration file --> define which files are considered unittest, how to compile them, etc.

### Jest API

- `Describe`/`Suite`: to group tests tegother
- `It`/`Test`: individual test
- `Expect`: used within test for assertion
- `Before`/`After`: run setup/cleanup logic --> reduce code repetition

## Testing TS Application Logic with Jest

- Main points:
  - focus on inputs & outputs
  - cover all possible corner cases
  - keeping test logic simple
- Mocks: to replace external components that are not undertest
  - To remove randomness (e.g. returned value not fixed)
  - To remove side effects (e.g. calls resulted in charged fee)
  - Should also use mock to reduce async effects --> speed up execution
- Mock principle:
  - keep implementation simple
  - import mocks before components --> ensure they're applied
  - Use fast, synchronous mocks

## Testing TS Web Components with Jest

- Main points
  - Keep it simple
  - Use mocks to eliminate uncertainty
  - Use libraries to virtualize browser environment (e.g. jsdom)
  - Test critical interactions (as other parts may change)
- Snapshot test: saved the html snapshot of the output for future comparison
  - Pros: fast
  - Cons: sensitive to minor change
