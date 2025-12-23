# Chapter 4: Type Annotations and Type Inference

## Core Concept: The Colon Notation

The **colon (`:`)** is the foundation of TypeScript. It's how you tell TypeScript what type a variable, parameter, or return value should be.

```typescript
const variableName: TypeName = value;
```

## Type Inference - TypeScript's Intelligence

### What is Type Inference?

**Type Inference** means TypeScript automatically figures out the type of a variable without you explicitly stating it.

### Example 1: Basic Inference

```typescript
const drink = "coffee";
```

When you hover over `drink`, TypeScript shows:

```
const drink: string
```

TypeScript automatically inferred that `drink` is a string because you assigned a string value to it.

### Example 2: Computed Values

```typescript
const cups = 5;
const moreCups = cups + 3;
```

TypeScript knows:

- `cups` is `number`
- `moreCups` is `number`

### Example 3: Conditional Expressions

```typescript
const count = Math.random() > 0.5 ? 10 : 5;
```

TypeScript infers `count` is `number` because both branches return numbers.

### Example 4: With Falsy Values

```typescript
const cups = Math.random() > 0.5 ? 10 : "5";
```

Now TypeScript infers: `cups: number | string` (union type - could be either)

## When NOT to Rely on Inference

Sometimes you NEED to explicitly annotate types:

```typescript
// Without annotation - TypeScript can't know the intent
let user; // Type is 'any' - TOO LOOSE

// With annotation - clear intent
let user: string; // You plan to assign a string later

// Full annotation
const userName: string = "Manmohan";
```

## Basic Type Annotations

### String Type

```typescript
const name: string = "Manmohan";
const greeting: string = `Hello ${name}`;

// These would error:
let userName: string = 42; // Error: number is not assignable to string
let userEmail: string = true; // Error: boolean is not assignable to string
```

### Number Type

```typescript
const age: number = 25;
const pi: number = 3.14;
const count: number = 42;

// These would error:
let total: number = "100"; // Error: string is not assignable to number
```

### Boolean Type

```typescript
const isActive: boolean = true;
const hasError: boolean = false;
const canAccess: boolean = true;

// These would error:
let isValid: boolean = 1; // Error: number is not assignable to boolean
let isEmpty: boolean = ""; // Error: string is not assignable to boolean
```

### Null and Undefined

```typescript
const empty: null = null;
const notDefined: undefined = undefined;
```

In TypeScript, both null and undefined indicate the absence of a value, but they serve different purposes and have distinct characteristics. undefined generally indicates a variable that has not been assigned a value, while null represents an intentional, programmatic absence of any object value.

## The Two Types of Errors in TypeScript

### Error Type 1: Syntax Errors

Syntax errors are **obvious mistakes** in code structure:

```typescript
let let;  // Syntax Error: can't use reserved keyword
const = "value";  // Syntax Error: invalid variable name
if (condition { }  // Syntax Error: missing closing parenthesis
```

These are usually caught immediately and shown as red squiggly underlines in IDE.

### Error Type 2: Type Errors

Type errors occur when **data types don't match**:

```typescript
let userName: string = "Manmohan";
userName = 42; // Type Error: number is not assignable to string
```

**Most Important Error Message in TypeScript:**

```
Type 'X' is not assignable to type 'Y'
```

When you see this error, it means:

- You declared something as type Y
- You tried to assign type X to it
- X and Y are incompatible types

This is the **most common error** you'll encounter, and it's actually **a good thing** - it prevents bugs before runtime!

## Type Inference Limitations

### When Inference Works Best

TypeScript can infer types when:

- You assign a literal value immediately
- Values come from built-in functions with known return types
- You perform type-narrowing operations

### When You MUST Annotate

```typescript
// BAD - Type is 'any' (too permissive)
let result;

// GOOD - Clear intent
let result: number;

// BAD - No initial value, can't infer
let products;

// GOOD - Explicit annotation
let products: string[];
```

## Important: Type Annotations are REMOVED During Compilation

This is crucial to understand:

### TypeScript Code

```typescript
function greet(name: string): string {
  return `Hello ${name}`;
}
```

### Compiled JavaScript

```javascript
function greet(name) {
  return `Hello ${name}`;
}
```

Notice:

- `: string` parameter annotation is removed
- `: string` return type annotation is removed
- Function body is identical
- All type information is stripped away

**This is why TypeScript doesn't add runtime overhead** - all type checking happens at compile time, then types are removed before execution.

## Real-World Example: Type Mismatches

### The Problem

```typescript
const userName: string = "Manmohan";
let userId: number;

// Later in code, a team member does this:
userId = userName; // ERROR!
```

Error message:

```
Type 'string' is not assignable to type 'number'
```

**This prevents bugs before they happen!** Without TypeScript, this would silently assign the string to the number variable, causing unexpected behavior later.

## Variable Type Inference

```typescript
// Inferred types
const isDone = true; // Inferred: boolean
const count = 42; // Inferred: number
const name = "John"; // Inferred: string
const items = [1, 2, 3]; // Inferred: number[]
const obj = { id: 1 }; // Inferred: {id: number}

// Without initial values - Type 'any' (loose, dangerous)
let value; // Type: any - can be anything!
```

## Optional Type Inference

Sometimes TypeScript can't infer and needs help:

```typescript
// Implicit 'any' - problematic
function add(a, b) {
  return a + b;
}

// With annotations - clear
function add(a: number, b: number): number {
  return a + b;
}
```

Enable `noImplicitAny` in `tsconfig.json` to catch these cases:

```json
{
  "compilerOptions": {
    "noImplicitAny": true
  }
}
```

## Practical Guidelines

### Use Type Annotations When:

1. **Function parameters** - Always annotate input types

   ```typescript
   function processData(data: string): void {}
   ```

2. **Function return types** - For clarity and safety

   ```typescript
   function calculateTotal(items: number[]): number {}
   ```

3. **Complex objects** - Use interfaces

   ```typescript
   interface User {
     name: string;
     age: number;
   }
   ```

4. **API responses** - You don't know the shape
   ```typescript
   const response: APIResponse = await fetch(...);
   ```

### Trust Inference When:

1. **Simple variable assignments**

   ```typescript
   const count = 5; // Obviously a number
   ```

2. **Array literals**

   ```typescript
   const colors = ["red", "blue"]; // Obviously string[]
   ```

3. **Object literals**
   ```typescript
   const user = { name: "John", age: 30 }; // Type is clear
   ```

## Key Takeaways

1. **The colon (`:`)** is how you annotate types in TypeScript
2. **Type Inference** - TypeScript figures out types automatically in simple cases
3. **Explicit annotations** - Always use them for function parameters and return types
4. **Type Errors** - The most common error is "Type X is not assignable to type Y"
5. **Types are stripped** - All type annotations are removed during compilation
6. **Catch bugs early** - TypeScript prevents type-related bugs before runtime

---

**Next:** We'll explore primitive types: string, number, boolean, and their nuances.
