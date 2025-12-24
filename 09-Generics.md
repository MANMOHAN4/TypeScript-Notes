# Chapter 9: Generics - Writing Reusable Code

## What are Generics?

**Generics** allow you to write code that works with **any type**, while still maintaining **type safety**. Think of it as a "type placeholder" or "type variable".

Instead of writing the same function multiple times for different types, you write it once and let TypeScript handle different types.

## Basic Generic Function

### Without Generics (Problematic)

```typescript
// This only works with strings
function getFirstString(arr: string[]): string {
  return arr[0];
}

// This only works with numbers
function getFirstNumber(arr: number[]): number {
  return arr[0];
}

// This only works with booleans
function getFirstBoolean(arr: boolean[]): boolean {
  return arr[0];
}

// We're repeating ourselves!
```

### With Generics (DRY Principle)

```typescript
function getFirst<T>(arr: T[]): T {
  return arr[0];
}

// Now it works with ANY type!
const firstString = getFirst<string>(["a", "b", "c"]); // Type: string
const firstNumber = getFirst<number>([1, 2, 3]); // Type: number
const firstBoolean = getFirst<boolean>([true, false]); // Type: boolean

// TypeScript can also infer the type
const inferred = getFirst(["x", "y", "z"]); // Type: string (inferred)
```

## Understanding the Syntax

```typescript
function getFirst<T>(arr: T[]): T {
  return arr[0];
}
```

Breaking this down:

- `<T>` - **Type parameter** (like a variable for types)
- `T` can be used anywhere in the function
- `arr: T[]` - The array contains elements of type T
- `: T` - Return type is also T
- When called, T is replaced with the actual type

## Multiple Type Parameters

```typescript
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

const result1 = pair<string, number>("hello", 42);
const result2 = pair<boolean, string>(true, "world");

// With type inference
const result3 = pair("john", 30); // Inferred as [string, number]
```

## Generic Interfaces

```typescript
interface Container<T> {
  value: T;
  getValue(): T;
  setValue(value: T): void;
}

// Implement with specific type
const stringContainer: Container<string> = {
  value: "hello",
  getValue() {
    return this.value;
  },
  setValue(value: string) {
    this.value = value;
  },
};

const numberContainer: Container<number> = {
  value: 42,
  getValue() {
    return this.value;
  },
  setValue(value: number) {
    this.value = value;
  },
};
```

## Generic Classes

```typescript
class Box<T> {
  private contents: T;

  constructor(value: T) {
    this.contents = value;
  }

  get(): T {
    return this.contents;
  }

  set(value: T): void {
    this.contents = value;
  }
}

const stringBox = new Box<string>("hello");
const numberBox = new Box<number>(42);

stringBox.get(); // Type: string
numberBox.get(); // Type: number
```

## Generic Constraints

Sometimes you want to **limit what types can be used** with a generic:

### Basic Constraint

```typescript
// T must be a number
function double<T extends number>(value: T): T {
  return (value * 2) as T;
}

double(5); // OK
double("five"); // ERROR - string doesn't extend number
```

### Constraint with Objects

```typescript
interface Named {
  name: string;
}

function getName<T extends Named>(obj: T): string {
  return obj.name; // Safe - T is guaranteed to have 'name'
}

getName({ name: "John", age: 30 }); // OK - has name property
getName({ age: 30 }); // ERROR - missing name
```

### Constraint with Array Keys

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = { name: "John", age: 30 };

getProperty(person, "name"); // OK - "name" exists
getProperty(person, "email"); // ERROR - "email" doesn't exist
```

## Default Type Parameters

```typescript
// If no type is provided, U defaults to string
function process<T, U = string>(value: T, extra: U): [T, U] {
  return [value, extra];
}

const a = process<number>(42, "hello"); // [42, "hello"]
const b = process<number, boolean>(42, true); // [42, true]
const c = process<number>(42); // [42, ""] - defaults to string
```

## Real-World Example: Generic Repository

```typescript
interface Entity {
  id: number;
}

class Repository<T extends Entity> {
  private items: T[] = [];

  add(item: T): void {
    this.items.push(item);
  }

  getById(id: number): T | undefined {
    return this.items.find((item) => item.id === id);
  }

  getAll(): T[] {
    return this.items;
  }

  update(id: number, updates: Partial<T>): T | undefined {
    const item = this.getById(id);
    if (item) {
      Object.assign(item, updates);
    }
    return item;
  }

  delete(id: number): boolean {
    const index = this.items.findIndex((item) => item.id === id);
    if (index !== -1) {
      this.items.splice(index, 1);
      return true;
    }
    return false;
  }
}

// Usage with different types
interface User extends Entity {
  name: string;
  email: string;
}

interface Product extends Entity {
  title: string;
  price: number;
}

const userRepository = new Repository<User>();
userRepository.add({ id: 1, name: "John", email: "john@example.com" });

const user = userRepository.getById(1);
// user is of type User | undefined

const productRepository = new Repository<Product>();
productRepository.add({ id: 1, title: "Tea", price: 500 });
```

## Generic with Conditional Types

```typescript
type IsString<T> = T extends string ? true : false;

type A = IsString<"hello">; // true
type B = IsString<number>; // false
```

## Key Takeaways

1. **Generics** allow writing reusable code: `<T>`
2. **Type parameters** act like variables for types
3. **Type inference** eliminates need for explicit types sometimes
4. **Constraints** limit what types can be used: `<T extends string>`
5. **Multiple parameters** can be generic: `<T, U>`
6. **Classes and interfaces** can be generic
7. **Default values** for type parameters: `<T = string>`
8. **Real-world use** includes repositories, hooks, utilities
