---
name: typescript-standards
description: TypeScript coding standards and compilation rules for this project. Use this skill whenever working with TypeScript files (.ts, .tsx), configuring tsconfig.json, writing or reviewing TypeScript code, or discussing TypeScript patterns and best practices. Triggers on any TypeScript-related task including code generation, refactoring, code review, and debugging.
---

# TypeScript Standards

Project-specific TypeScript rules and guidelines. **Read `./reference/typescript-rules.md` for complete documentation.**

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

## When to Consult Full Rules

Read `./reference/typescript-rules.md` for:
- Detailed examples of each tier (1-4)
- Brand types implementation
- `readonly` usage guidelines
- Error handling patterns (Result types)
- Legacy/third-party code strategies
- Performance hotspot handling
