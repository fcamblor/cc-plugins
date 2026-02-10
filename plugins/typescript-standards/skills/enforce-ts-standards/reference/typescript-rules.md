# TypeScript Rules & Guidelines - Complete Reference

## Table of Contents

- [TIER 1 - Critical](#tier-1---critical-type-safety-foundation)
- [TIER 2 - Very Important](#tier-2---very-important-maintainability--prevention)
- [TIER 3 - Important](#tier-3---important-code-quality--standards)
- [TIER 4 - Advanced](#tier-4---advanced-patterns-complex-logic--validation)
- [Pragmatism with Legacy Code](#pragmatism-with-legacy--third-party-code)

---

# TIER 1 - Critical (Type Safety Foundation)

## prefer-unknown-over-any

Prefer `unknown` over `any`. Requires explicit narrowing before use.

```ts
// ❌ BAD
function process(data: any) {
  return data.toUpperCase();
}

// ✅ GOOD
function process(data: unknown) {
  if (typeof data === 'string') {
    return data.toUpperCase();
  }
  throw new Error('Expected string');
}

// Error handling
try {
  // operation
} catch (e: unknown) {
  if (e instanceof Error) {
    console.error(e.message);
  }
}
```

**Only use `any` when:** conditional typing requires it, all alternatives exhausted, always document why.

## type-assertions-as-last-resort

Never use `as` unless all safer alternatives exhausted.

**Priority order:**
1. Schema validation: `UserSchema.parse(data)`
2. `satisfies` operator
3. Pattern matching with ts-pattern
4. Type narrowing with if/type guards
5. Optional chaining with defaults

```ts
// ❌ BAD
const user = apiResponse as User;
const value = (data as any) as string;

// ✅ GOOD
const user = UserSchema.parse(data);

const routes = {
  home: { path: '/', method: 'GET' },
} satisfies Record<string, { path: string; method: string }>;
```

## no-non-null-assertions

Never use `!` operator.

```ts
// ❌ BAD
const value = maybeNull!.toUpperCase();

// ✅ GOOD
const value = maybeNull?.toUpperCase();
const val = maybeNull ?? 'default';
const first = array.at(0) ?? defaultValue;
```

## optional-properties

Use optional `?` sparingly. Prefer explicit `Type | undefined`.

```ts
// ❌ BAD - hides required intent
type AuthOptions = { userId?: string };

// ✅ GOOD - explicit
type AuthOptions = { userId: string | undefined };

// ✅ BEST - when always required
type AuthOptions = { userId: string };
```

## discriminated-unions

Model data that can be in several shapes. Prevents impossible states.

```ts
// ❌ BAD - allows impossible states
type FetchingState<T> = {
  status: 'idle' | 'loading' | 'success' | 'error';
  data?: T;
  error?: Error;
};

// ✅ GOOD
type FetchingState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };
```

## import-type

Use `import type` for type imports.

```ts
// ❌ BAD
import { User } from './user';
import { type User } from './user';

// ✅ GOOD
import type { User } from './user';
```

---

# TIER 2 - Very Important (Maintainability & Prevention)

## return-types

Declare return types on module boundaries.

**Boundaries (explicit required):**
- Public API functions
- Exported utilities
- API endpoint handlers
- Async operations
- Complex generic functions

```ts
// ✅ Public function
export async function fetchUser(id: string): Promise<User> {
  return UserSchema.parse(await api.get(`/users/${id}`));
}

// ✅ Internal - inference OK
function processData(raw: unknown) {
  return UserSchema.parse(raw);
}
```

## brand-types

Prevent confusion between similar primitives at compile-time.

```ts
type UserId = number & { readonly __brand: 'UserId' };
type ProductId = number & { readonly __brand: 'ProductId' };

function createUserId(id: number): UserId {
  if (id <= 0) throw new Error('Invalid user ID');
  return id as UserId;
}

// With Zod
const UserIdSchema = z.number().positive().brand<'UserId'>();
type UserId = z.infer<typeof UserIdSchema>;
```

## readonly-properties

Use `readonly` selectively, not as default.

```ts
// ✅ GOOD - readonly where needed
type User = {
  readonly id: string;  // Must never change
  name: string;         // Can be updated
  email: string;        // Can be updated
};
```

**When to use:** IDs, primary keys, critical domain properties, configuration values.

## throwing

Think carefully before throwing. Consider Result types for recoverable operations.

```ts
// ✅ Throwing in startup
export function initializeConfig(env: Environment) {
  if (!env.DATABASE_URL) {
    throw new Error('DATABASE_URL is required');
  }
}

// ✅ Result type for recoverable operations
type Result<T, E> = { ok: true; value: T } | { ok: false; error: E };

function parseJSON(input: string): Result<unknown, Error> {
  try {
    return { ok: true, value: JSON.parse(input) };
  } catch (error) {
    return { ok: false, error: error as Error };
  }
}

// ✅ Never return type for exhaustiveness
function assertNever(x: never): never {
  throw new Error("Unexpected object: " + x);
}
```

## composition-strategy

Prefer interfaces with `extends` over intersection types when possible.

```ts
// ✅ Simple composition - interfaces
interface A { a: string; }
interface B { b: string; }
interface C extends A, B { c: number; }

// ✅ Advanced features - type aliases
type Readonly<T> = { readonly [K in keyof T]: T[K] };
type IsString<T> = T extends string ? true : false;
type Status = 'pending' | 'completed' | 'error';
```

---

# TIER 3 - Important (Code Quality & Standards)

## default-exports

Avoid default exports. Use named exports.

```ts
// ❌ BAD
export default function UserCard() { ... }

// ✅ GOOD
export function UserCard() { ... }
```

## naming-conventions

| Category | Convention | Example |
|----------|------------|---------|
| Files | kebab-case | `user-card.ts` |
| Variables/Functions | camelCase | `userName`, `fetchUsers()` |
| Types/Interfaces | PascalCase | `User`, `UserService` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRIES` |
| Generics | T prefix | `TData`, `TKey` |

## enums

Never introduce new enums. Use unions + `as const`.

```ts
// ❌ BAD
enum Role { Admin, User }

// ✅ GOOD
type Role = 'admin' | 'user' | 'guest';

const ROLES = {
  admin: { id: 1, label: 'Administrator' },
  user: { id: 2, label: 'User' },
} as const;

type Role = keyof typeof ROLES;
```

---

# TIER 4 - Advanced Patterns (Complex Logic & Validation)

## ts-pattern

Use pattern matching for type-safe branching.

```ts
import { match, P } from 'ts-pattern';

const message = match(user)
  .with({ role: 'admin' }, () => 'Welcome admin')
  .with({ role: 'user', verified: true }, () => 'Welcome back')
  .with({ role: 'user', verified: false }, () => 'Please verify')
  .with({ role: 'guest' }, () => 'Please log in')
  .exhaustive();

// P helpers
// P.string, P.number, P.boolean - type wildcards
// P.when(predicate) - conditional patterns
// P.union(p1, p2) - alternative patterns
// P.nullish - null or undefined
// P.select() - value extraction
```

## Zod Guidelines

**Golden Rules:**
1. Schema-first: Infer types from schemas
2. Parse at boundaries: Validate once at entry
3. Transform while parsing: Use `.transform()`

```ts
// ✅ Schema is source of truth
const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  role: z.enum(['admin', 'user', 'guest'])
});
type User = z.infer<typeof UserSchema>;

// ✅ Parse at boundary
async function fetchUser(id: string): Promise<User> {
  const json = await fetch(`/api/users/${id}`).then(r => r.json());
  return UserSchema.parse(json);
}

// ✅ Compose schemas
const BaseSchema = z.object({
  id: z.string().uuid(),
  createdAt: z.string().datetime()
});
const UserSchema = BaseSchema.extend({ email: z.string().email() });

// ✅ Transform during parsing
const DateSchema = z.string().datetime().transform(s => new Date(s));

// ✅ Handle expected failures
const result = FormSchema.safeParse(input);
if (!result.success) {
  return { errors: result.error.flatten() };
}

// ✅ Recursive schemas
const CommentSchema: z.ZodType<Comment> = z.lazy(() =>
  z.object({
    id: z.string(),
    text: z.string(),
    replies: z.array(CommentSchema)
  })
);
```

**Quick Reference:**
- `.parse(data)` → throws ZodError
- `.safeParse(data)` → `{ success, data?, error? }`
- `.strict()` → reject unknown fields
- `.partial()` → all fields optional
- `.readonly()` → mark readonly

## any-inside-generic-functions

May need assertions inside generic function bodies.

```ts
async function loadAllRecords<T>(
  source: DataSource,
  schema: { parse: (data: unknown) => T }
): Promise<T[]> {
  const raw = await source.fetch();
  return schema.parse(raw) as T[];
}
```

## no-unchecked-indexed-access

Always consider indexed access might be undefined.

```ts
const obj: Record<string, string> = {};

// ✅ Defensive
if (obj.key !== undefined) {
  console.log(obj.key.toUpperCase());
}

// ✅ Optional chaining
const upper = obj.key?.toUpperCase();

// ✅ Nullish coalescing
const result = obj.key ?? 'default';
```

---

# Pragmatism with Legacy & Third-Party Code

## Working with Third-Party `any` Types

Create a type boundary:

```ts
// third-party-wrapper.ts
import someLib from 'old-lib';

export function wrappedFunction(input: unknown) {
  const result = someLib.process(input as any);
  return resultSchema.parse(result);
}
```

## Using `as` in Legacy Code

1. Document the reason
2. Isolate in adapter functions
3. Create escape hatch, not norm

```ts
// legacy-adapter.ts
export function legacyToModern(data: unknown): ModernType {
  // All `as` statements isolated here
  return data as ModernType;
}
```

## When to NOT Follow These Rules

- Throwaway code (prototypes, scripts)
- Code under active migration
- Test files
- Auto-generated code
