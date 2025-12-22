# Chapter 10: Advanced TypeScript Concepts

## Type Aliases vs Interfaces

Both define types, but there are important differences:

### Interfaces

```typescript
interface User {
  name: string;
  age: number;
}

// Can extend
interface Admin extends User {
  role: "admin";
}

// Can be merged (declaration merging)
interface User {
  email?: string;  // Adds email to existing User
}
```

### Type Aliases

```typescript
type User = {
  name: string;
  age: number;
};

// Can use union
type Admin = User & { role: "admin" };

// Can create unions
type Status = "pending" | "approved" | "rejected";

// Cannot be merged directly
type User = {  // ❌ Would override, not merge
  email?: string;
};
```

**General Rule:**
- Use **interfaces** for object contracts
- Use **types** for unions, tuples, and primitives

## Utility Types - Transform Existing Types

### Partial<T> - All properties optional

```typescript
interface User {
  name: string;
  age: number;
  email: string;
}

// Partial<User> makes all properties optional
type PartialUser = Partial<User>;

const updates: PartialUser = {
  name: "John"  // Can skip age and email
};
```

### Required<T> - All properties required

```typescript
interface Config {
  host?: string;
  port?: number;
}

type RequiredConfig = Required<Config>;
// Now host and port are required
```

### Readonly<T> - All properties readonly

```typescript
type ReadonlyUser = Readonly<User>;
// Cannot modify any properties
```

### Pick<T, K> - Select specific properties

```typescript
interface User {
  name: string;
  age: number;
  email: string;
}

type UserPreview = Pick<User, "name" | "email">;
// Only has name and email
```

### Omit<T, K> - Remove specific properties

```typescript
type UserWithoutAge = Omit<User, "age">;
// Has name and email, but not age
```

### Record<K, T> - Create object with specific keys

```typescript
type Role = "admin" | "user" | "guest";
type RolePermissions = Record<Role, string[]>;

const permissions: RolePermissions = {
  admin: ["read", "write", "delete"],
  user: ["read", "write"],
  guest: ["read"]
};
```

## Keyof and Index Access

### keyof - Get all property names

```typescript
interface User {
  name: string;
  age: number;
}

type UserKeys = keyof User;  // "name" | "age"

// Using in a function
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: "John", age: 30 };
getProperty(user, "name");  // ✅ OK - "name" is a valid key
getProperty(user, "email"); // ❌ ERROR - "email" is not a valid key
```

### Indexed Access Types

```typescript
interface User {
  name: string;
  profile: {
    bio: string;
    avatar: string;
  };
}

type Name = User["name"];                    // string
type Profile = User["profile"];              // { bio: string; avatar: string }
type Bio = User["profile"]["bio"];           // string

type AllValues = User[keyof User];           // string | { bio: string; avatar: string }
```

## Conditional Types

```typescript
type IsString<T> = T extends string ? true : false;

type A = IsString<"hello">;   // true
type B = IsString<number>;    // false

// In functions
function process<T>(value: T): T extends string ? number : string {
  if (typeof value === 'string') {
    return (value.length) as any;
  } else {
    return "not string" as any;
  }
}
```

## Never Type

The `never` type represents **impossibility** - a value that can never occur:

```typescript
// Function that never returns (throws or infinite loop)
function throwError(message: string): never {
  throw new Error(message);
}

// Unreachable code
function impossible(x: string | number): string {
  if (typeof x === 'string') {
    return x;
  } else if (typeof x === 'number') {
    return x.toString();
  } else {
    // x is never - impossible to reach here
    const impossible: never = x;
    return impossible;
  }
}

// Exhaustiveness checking
type Status = "loading" | "success" | "error";

function handleStatus(status: Status): void {
  switch (status) {
    case "loading":
      console.log("Loading...");
      break;
    case "success":
      console.log("Success!");
      break;
    case "error":
      console.log("Error!");
      break;
    default:
      // If we forgot a case, status would be 'never' here
      const exhaustive: never = status;
  }
}
```

## Unknown Type

`unknown` is the **type-safe** version of `any`:

```typescript
// ❌ BAD - any bypasses type checking
let value: any = getValue();
value.someMethod();  // No error checking

// ✅ GOOD - unknown requires type checking
let value: unknown = getValue();
// value.someMethod();  // ❌ ERROR - need to check type first

if (typeof value === 'string') {
  value.toUpperCase();  // ✅ OK - now we know it's a string
}
```

## Decorators (Experimental)

Decorators are functions that modify classes or properties:

```typescript
// Class decorator
function Sealed(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}

@Sealed
class User {
  name: string = "John";
}

// Property decorator
function Log(target: any, propertyKey: string) {
  let value = target[propertyKey];

  const getter = () => {
    console.log(`Getting ${propertyKey}`);
    return value;
  };

  const setter = (newValue: any) => {
    console.log(`Setting ${propertyKey} to ${newValue}`);
    value = newValue;
  };

  Object.defineProperty(target, propertyKey, {
    get: getter,
    set: setter,
  });
}

class Person {
  @Log
  name: string = "John";
}
```

## Module Augmentation

Extend existing types from libraries:

```typescript
// Extending global objects
declare global {
  interface String {
    toCamelCase(): string;
  }
}

String.prototype.toCamelCase = function() {
  return this.replace(/-([a-z])/g, (_, letter) => letter.toUpperCase());
};

const result = "hello-world".toCamelCase();  // "helloWorld"
```

## Ambient Declarations

Declare types for external JavaScript libraries:

```typescript
// For a library that doesn't have types
declare module "some-library" {
  export function doSomething(x: string): number;
  export const version: string;
}

import { doSomething } from "some-library";
const result = doSomething("hello");  // Safe to use
```

## ThisType

Specify the type of `this` in an object:

```typescript
interface User {
  name: string;
  getName(this: User): string;
}

const user: User = {
  name: "John",
  getName() {
    return this.name;  // 'this' is correctly typed as User
  }
};
```

## Key Takeaways

1. **Type aliases** vs **Interfaces** - Choose based on use case
2. **Utility types** transform existing types (Partial, Pick, Omit)
3. **keyof** gets all property names of a type
4. **Conditional types** allow type-dependent logic
5. **never type** represents impossibility
6. **unknown type** is type-safe alternative to any
7. **Decorators** modify classes/properties (experimental)
8. **Declaration merging** extends interfaces
9. **Module augmentation** extends external types

---

**Next:** We'll explore best practices and debugging TypeScript.
