# TypeScript Standards Plugin

A comprehensive Claude Code plugin that enforces type-safe TypeScript patterns and best practices to prevent runtime errors through compile-time type safety.

## Overview

This plugin provides the **`enforce-ts-standards`** skill — a production-ready guide for writing safer TypeScript code. It covers critical type safety rules, validation patterns, and advanced techniques using industry-standard tools like **Zod** and **ts-pattern**.

## Features

### 🛡️ Type Safety Foundation
- Prefer `unknown` over `any` for explicit narrowing
- Eliminate type assertions with safer alternatives
- Prevent impossible states through discriminated unions
- Brand types to distinguish similar primitives

### ✅ Validation & Boundaries
- Parse external data with Zod schemas
- Trust validated data internally
- Handle expected failures gracefully with Result types
- Schema-first validation architecture

### 🎯 Pattern Matching
- Exhaustive pattern matching with `ts-pattern`
- Type-safe conditional logic
- Complex state handling with discriminated unions

### 📋 Code Quality Standards
- Naming conventions (camelCase, PascalCase, kebab-case)
- Named exports only (no default exports)
- Explicit return types on module boundaries
- Union types instead of enums

### 🔄 Advanced Patterns
- Recursive schema validation
- Generic function type safety
- Safe indexed access handling
- Pragmatic approaches for legacy code integration

## Installation

```bash
# Copy this plugin into your Claude Code plugins directory
cp -r typescript-standards ~/.claude/plugins/
```

## Usage

Invoke the skill within Claude Code using:

```
/enforce-ts-standards
```

Use this skill when:
- **Writing new TypeScript code** for production features
- **Reviewing TypeScript** for type safety issues
- **Designing validation** for external data (APIs, user input, configs)
- **Modeling complex state** to prevent impossible combinations
- **Deciding between patterns** for safer alternatives

## Documentation

### Quick Start
- **SKILL.md** — Quick reference with key rules and when to use/not use
- **reference/typescript-rules.md** — Complete reference covering all tiers

### Organized by Priority

| Tier | Focus | When to Check |
|------|-------|---------------|
| **TIER 1 - Critical** | Type safety foundation | Always — understand before coding |
| **TIER 2 - Very Important** | Return types, brand types, readonly | When building boundaries |
| **TIER 3 - Important** | Naming, exports, enums | During code review |
| **TIER 4 - Advanced** | Zod deep dives, ts-pattern, recursion | When solving complex problems |
| **Pragmatism** | Legacy code strategies | When modernizing existing code |

## Key Principles

### 1. Type Safety First
```ts
// ❌ Avoid
function process(data: any) { return data.value; }

// ✅ Prefer
function process(data: unknown) {
  if (isUser(data)) return data.value;
}
```

### 2. Validate at Boundaries
```ts
// External data must be validated with Zod
const UserSchema = z.object({ id: z.string(), email: z.string() });
type User = z.infer<typeof UserSchema>;

async function fetchUser(id: string): Promise<User> {
  const json = await fetch(`/api/users/${id}`).then(r => r.json());
  return UserSchema.parse(json); // ← Validation happens here
}
```

### 3. Prevent Impossible States
```ts
// ❌ Allows invalid combinations
type State = { status: string; data?: T; error?: Error };

// ✅ Prevents impossible states
type State<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };
```

### 4. Exhaustive Pattern Matching
```ts
import { match, P } from 'ts-pattern';

const result = match(state)
  .with({ status: 'loading' }, () => <Spinner />)
  .with({ status: 'success', data: P.select() }, (data) => <Data data={data} />)
  .with({ status: 'error' }, ({ error }) => <Error error={error} />)
  .exhaustive(); // ← Compiler ensures all cases handled
```

## When NOT to Use

Apply pragmatism in these cases:
- **Legacy code** being migrated — Document escapes with `// TODO: improve type safety`
- **Third-party libraries** with weak types — Use adapter/bridge modules at boundaries
- **Different tools** — Apply equivalent patterns with your chosen validation/matching libraries

## Naming Conventions

| Category | Convention | Example |
|----------|------------|---------|
| Files | kebab-case | `user-card.ts` |
| Variables/Functions | camelCase | `userName`, `fetchUsers()` |
| Types/Interfaces | PascalCase | `User`, `UserService` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRIES` |
| Generics | T prefix | `TData`, `TKey` |

## Tools & Dependencies

This plugin assumes projects use:
- **Zod** for runtime validation and schema parsing
- **ts-pattern** for pattern matching and exhaustiveness checking

If your project uses different tools (io-ts, Yup, etc.), apply the same principles with your chosen libraries.

## Example Topics in Reference

- **Brand types** — Prevent `UserId` ≠ `ProductId` confusion at compile-time
- **Result types** — Handle recoverable errors without throwing
- **Recursive schemas** — Complex nested data structures with Zod
- **Adapter pattern** — Bridge legacy libraries with weak types
- **Performance hotspots** — When to relax standards for speed

## File Structure

```
typescript-standards/
├── README.md                                    # This file
├── skills/
│   └── enforce-ts-standards/
│       ├── SKILL.md                            # Quick reference guide
│       └── reference/
│           └── typescript-rules.md             # Complete comprehensive rules
└── .claude-plugin/
    └── plugin.json                             # Plugin metadata
```

## Quick Answers

**Q: When do I need Zod vs type guards?**
Zod for untrusted boundaries (APIs, user input, env). Type guards for trusted internal data.

**Q: Should I use brand types?**
When you have similar primitives (UserId, ProductId, OrderId).

**Q: Can I use `as` type assertions?**
Only as last resort. Try: validation → `satisfies` → type narrowing → guards → assertions.

**Q: When should I relax these standards?**
See "When NOT to Use This Skill" section in SKILL.md. Document escapes.

## Golden Rules (Always Apply)

1. **Type Safety First** — Avoid `any`, `as`, `!`. Use `unknown` + narrowing.
2. **Validate at Boundaries** — Use Zod for external data. Trust validated data internally.
3. **Exhaustive Matching** — Use ts-pattern with `.exhaustive()` for complex conditionals.
4. **Explicit on Boundaries** — Return types on exports. Let inference work internally.
5. **Fail Fast** — Prevent impossible states through discriminated unions.

## Contributing

This plugin is maintained as part of the cc-plugins project. Contributions are welcome!

## License

See LICENSE file in the root of the cc-plugins repository.

## Support

For questions or issues with this plugin:
1. Check the SKILL.md for quick reference
2. Review reference/typescript-rules.md for comprehensive coverage
3. Check the pragmatism section for legacy code integration strategies
