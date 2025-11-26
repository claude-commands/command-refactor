# command-refactor

A Claude Code slash command for AI-guided refactoring with safety checks.

## Installation

```bash
# Clone to your preferred location
git clone git@github.com:claude-commands/command-refactor.git <clone-path>/command-refactor

# Symlink (use full path to cloned repo)
ln -s <clone-path>/command-refactor/refactor.md ~/.claude/commands/refactor.md
```

## Usage

```
/refactor src/utils/helpers.ts   # Refactor a file
/refactor processPayment         # Refactor a specific function
/refactor src/api/               # Analyze directory for opportunities
```

## What it does

1. Analyzes code for refactoring opportunities
2. Identifies code smells and complexity issues
3. Checks test coverage before changes
4. Proposes specific refactoring plan
5. Applies changes incrementally with verification

## Output Format

```markdown
## Refactoring Plan for `processPayment()`

### Issues Found
1. **High Complexity** (cyclomatic: 18)
2. **Long Function** (85 lines)
3. **Too Many Parameters** (7 params)

### Proposed Changes
1. Extract `validatePaymentDetails()` - 20 lines
2. Extract `calculateProcessingFee()` - 15 lines
3. Introduce `PaymentRequest` parameter object

### Metrics
| Metric | Before | After |
|--------|--------|-------|
| Lines | 85 | 45 |
| Complexity | 18 | 8 |

Proceed with refactoring? (y/n)
```

## Refactoring Types

| Type | Risk | When to Use |
|------|------|-------------|
| Extract Method | Low | Long functions |
| Rename | Low | Unclear names |
| Extract Variable | Low | Complex expressions |
| Move Method | Medium | Wrong class/module |
| Change Signature | High | Public APIs |
| Extract Class | High | God classes |

## Safety Features

- Checks test coverage before changes
- Applies changes incrementally
- Runs tests after each change
- Offers to revert on failure
- Asks before breaking changes

## Requirements

- Git repository with source code
- Test suite (recommended)
- Claude Code with Opus 4.5 model access

## Updates

```bash
cd <clone-path>/command-refactor && git pull
```
