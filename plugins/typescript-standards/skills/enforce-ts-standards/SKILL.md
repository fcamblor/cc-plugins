---
name: typescript-standards
description: |
  Enforce type-safe TypeScript patterns with Zod validation, pattern matching, and discriminated unions to prevent runtime errors. Use when writing TypeScript code, reviewing for type safety, designing data validation flows, or refactoring to eliminate impossible states.
---

# TypeScript Standards

Project-specific TypeScript rules and guidelines enforcing type safety at system boundaries and preventing impossible application states.

## When to Use This Skill

Use this skill when:
- **Writing new TypeScript code** for production features, utilities, or configuration
- **Reviewing TypeScript** for type safety issues or refactoring opportunities
- **Designing validation** for external data (APIs, user input, config files, environment variables)
- **Modeling complex state** to prevent impossible combinations (UI states, async operations, domain entities)
- **Deciding between patterns** — Which approach is safer: type guards vs Zod, optional fields vs discriminated unions, assertions vs narrowing?

This skill applies to **production code, shared utilities, and core business logic**.

## When NOT to Use This Skill

Apply pragmatism in these specific cases:

### 1. Legacy Code Integration
Code actively being migrated to type safety requires strategic pragmatism. Document escape hatches with `// TODO: improve type safety` comments. Converting a legacy codebase is a **multi-step project**, not a single skill application. Focus on new code and critical paths first.

### 2. Third-Party Libraries with Weak Types
External libraries that don't follow these standards are unavoidable. Solution: **Create adapter/bridge modules** that:
- Accept the library's loose types at the boundary
- Parse/validate using your standards internally
- Export proper types following project conventions

**Example:**
```ts
// old-lib-adapter.ts - bridge the gap
import someLib from 'old-lib';

export function adaptLibFunction(input: unknown) {
  // Accept old lib's loose types here
  const result = someLib.process(input as any);
  // Validate and parse at our boundary
  return resultSchema.parse(result);
}
```

### 3. Tool Selection - Zod, ts-pattern, etc.
These rules assume your project uses **Zod** for validation and **ts-pattern** for matching. If your project chooses different tools:
- **Don't force Zod** if using runtime validation differently (e.g., custom validators)
- **Don't force ts-pattern** if using other pattern matching approaches
- **Apply equivalent patterns** with your chosen tools
- **Enforce the principle**, not the library (type safety, boundary validation, exhaustiveness)

Example: If using `io-ts` instead of Zod, follow the same boundary validation pattern with `io-ts.decode()`.

## Golden Rules (Always Apply)

1. **Type Safety First** — Avoid `any`, `as`, `!`. Use `unknown` + narrowing.
2. **Validate at Boundaries** — Use Zod for external data (APIs, user input, env vars). Trust validated data internally.
3. **Exhaustive Matching** — Use ts-pattern for complex conditionals with `.exhaustive()`.
4. **Explicit on Boundaries** — Return types on exports. Let inference work internally.
5. **Fail Fast** — Prevent impossible states through discriminated unions.

## Quick Reference

### Type Safety Priority

```ts
// ❌ BAD
function process(data: any) { return data.value; }
const user = response as User;
const name = user!.name;

// ✅ GOOD
function process(data: unknown) {
  if (isUser(data)) return data.value;
}
const user = UserSchema.parse(response);
const name = user?.name ?? 'default';
```

### Validation Pattern (Zod)

```ts
// Schema is source of truth
const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  role: z.enum(['admin', 'user', 'guest'])
});
type User = z.infer<typeof UserSchema>;

// Parse at boundary, trust internally
async function fetchUser(id: string): Promise<User> {
  const json = await fetch(`/api/users/${id}`).then(r => r.json());
  return UserSchema.parse(json);
}
```

### Pattern Matching (ts-pattern)

```ts
import { match, P } from 'ts-pattern';

const result = match(state)
  .with({ status: 'loading' }, () => <Spinner />)
  .with({ status: 'success', data: P.select() }, (data) => <Data data={data} />)
  .with({ status: 'error' }, ({ error }) => <Error error={error} />)
  .exhaustive();
```

### Discriminated Unions

```ts
// ❌ Allows impossible states
type State = { status: string; data?: T; error?: Error };

// ✅ Prevents impossible states
type State<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };
```

## Key Conventions

| Category | Rule |
|----------|------|
| Files | `kebab-case.ts` |
| Variables/Functions | `camelCase` |
| Types/Interfaces | `PascalCase` |
| Constants | `UPPER_SNAKE_CASE` |
| Generics | `TData`, `TKey` |
| Exports | Named only (no default) |
| Imports | `import type` for types |
| Enums | Never (use unions + `as const`) |

---

## Full Reference & Deep Dives

For comprehensive rules, advanced patterns, and edge cases, consult `./reference/typescript-rules.md`:

| Topic | Why Check | When |
|-------|-----------|------|
| **TIER 1 - Critical** | Foundation rules on type safety | Always - understand before coding |
| **TIER 2 - Very Important** | Return types, brand types, `readonly` | When building boundaries |
| **TIER 3 - Important** | Naming conventions, exports, enums | During code review |
| **TIER 4 - Advanced** | Zod deep dives, ts-pattern guards, recursion | When solving complex problems |
| **Pragmatism Section** | Legacy code strategies | When modernizing existing code |

**Key Topics in Reference:**
- Brand types implementation (prevent UserId ≠ ProductId confusion)
- Result types for recoverable errors (vs throwing)
- Recursive Zod schemas (comments, nested structures)
- Working with third-party `any` types (adapter pattern)
- Performance hotspots (when to relax for speed)

---

## Quick Answers

**Q: When do I need Zod vs type guards?**
Zod for untrusted boundaries (APIs, user input, env). Type guards for trusted internal data.

**Q: Should I use brand types?**
When you have similar primitives (UserId, ProductId, OrderId). See TIER 2 in reference.

**Q: Can I use `as` type assertions?**
Only as last resort. Try: validation → `satisfies` → type narrowing → guards → assertions.

**Q: When should I relax these standards?**
See "When NOT to Use This Skill" section above. Document escapes with `// TODO: improve type safety`.
