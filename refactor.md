---
argument-hint: "<file|function>"
description: "AI-guided refactoring with safety checks"
model: claude-opus-4-5-20251101
allowed-tools: ["Bash", "Read", "Write", "Edit", "Glob", "Grep", "AskUserQuestion"]
---

**If `$ARGUMENTS` is empty or not provided:**

Display usage information and ask for input:

**Usage:** `/refactor <target>`

**Examples:**

- `/refactor src/utils/helpers.ts` - Refactor a file
- `/refactor processPayment` - Refactor a specific function
- `/refactor src/api/` - Analyze a directory for refactoring opportunities

**Workflow:**

1. Analyze code for refactoring opportunities
2. Identify code smells and complexity issues
3. Propose specific refactoring actions
4. Verify test coverage before changes
5. Apply changes incrementally with verification

Ask the user: "What file or function would you like me to refactor?"

---

**If `$ARGUMENTS` is provided:**

Perform AI-guided refactoring on the specified code with safety checks.

## Configuration

- **Target**: `$ARGUMENTS` (file path or function name)

## Steps

1. **Read and Analyze Target**

   Read the target code and identify:
   - Current structure and purpose
   - Dependencies (imports/exports)
   - Callers and callees
   - Existing test coverage

2. **Identify Refactoring Opportunities**

   Look for these patterns:

   **Extract Method** - Long functions (>30 lines):
   - Identify cohesive blocks of code
   - Name them by what they do
   - Extract with clear parameters

   **Simplify Conditionals** - Complex branching:
   - Nested if/else chains
   - Long switch statements
   - Guard clauses opportunity

   **Remove Duplication** - Repeated code:
   - Similar code blocks in same file
   - Copy-paste patterns
   - Extract to shared utilities

   **Rename for Clarity** - Unclear names:
   - Single-letter variables (except loops)
   - Abbreviations
   - Misleading names

   **Reduce Parameters** - Functions with >4 params:
   - Group into objects
   - Use builder pattern
   - Extract configuration

   **Decompose Large Classes** - God classes:
   - Multiple responsibilities
   - Too many methods
   - Extract by domain

3. **Check Test Coverage**

   Before any changes:

   ```bash
   # Find related test files
   # Run tests to ensure baseline passes
   ```

   If no tests exist, **ask the user**:
   - "No tests found for this code. Should I generate tests first?"
   - Recommend writing tests before refactoring

4. **Propose Refactoring Plan**

   Present to user:

   ```markdown
   ## Refactoring Plan for `processPayment()`

   ### Issues Found
   1. **High Complexity** (cyclomatic: 18)
      - 6 nested conditionals
   2. **Long Function** (85 lines)
   3. **Too Many Parameters** (7 params)

   ### Proposed Changes

   #### 1. Extract Validation Logic
   - Extract `validatePaymentDetails()` from lines 15-35
   - Reduces main function by 20 lines

   #### 2. Extract Fee Calculation
   - Extract `calculateProcessingFee()` from lines 40-55
   - Makes fee logic reusable

   #### 3. Introduce Parameter Object
   - Create `PaymentRequest` type
   - Group: amount, currency, method, metadata

   #### 4. Simplify Conditionals
   - Convert nested if/else to early returns
   - Use switch for payment method handling

   ### Impact
   - Files modified: 1
   - New functions: 2
   - Complexity reduction: 18 → 8

   Proceed with refactoring? (y/n)
   ```

5. **Apply Changes Incrementally**

   For each refactoring:
   1. Make the specific change
   2. Run tests to verify
   3. If tests fail, revert and explain
   4. Proceed to next change

   **Order of operations**:
   - Extract methods first (lowest risk)
   - Rename variables second
   - Restructure conditionals third
   - Change signatures last (highest risk)

6. **Verify After Each Change**

   After each refactoring step:

   ```bash
   # Run tests
   npm test  # or go test, pytest, etc.

   # Check for type errors
   tsc --noEmit  # or equivalent
   ```

   If verification fails:
   - Show the error
   - Offer to revert
   - Explain what went wrong

7. **Generate Summary**

   ```markdown
   ## Refactoring Complete

   ### Changes Made
   - Extracted `validatePaymentDetails()` (20 lines)
   - Extracted `calculateProcessingFee()` (15 lines)
   - Created `PaymentRequest` interface
   - Simplified conditionals with guard clauses

   ### Metrics
   | Metric | Before | After |
   |--------|--------|-------|
   | Lines | 85 | 45 |
   | Complexity | 18 | 8 |
   | Parameters | 7 | 1 (object) |

   ### Tests
   - All 12 tests passing
   - No regressions detected

   ### Review Checklist
   - [ ] Verify extracted functions have clear names
   - [ ] Check new interface is well-typed
   - [ ] Ensure no behavior changes
   ```

## Safety Rules

1. **Never refactor without reading tests first**
2. **Ask before making breaking changes** (public API changes)
3. **One refactoring at a time** - don't combine multiple changes
4. **Preserve behavior** - refactoring should not change what code does
5. **Keep changes reversible** - small commits, clear diffs
6. **Don't trust AI for cross-file renames** - verify manually

## Refactoring Types

| Type | Risk | When to Use |
|------|------|-------------|
| Extract Method | Low | Long functions |
| Rename | Low | Unclear names |
| Inline | Low | Unnecessary indirection |
| Extract Variable | Low | Complex expressions |
| Move Method | Medium | Wrong class/module |
| Extract Interface | Medium | Multiple implementations |
| Change Signature | High | Public APIs |
| Extract Class | High | God classes |

## Notes

- Refactoring should NOT change behavior
- If you need to change behavior, that's a bug fix or feature
- Prefer many small refactorings over one large rewrite
- When in doubt, add tests first
- Review AI suggestions critically - don't blindly accept
