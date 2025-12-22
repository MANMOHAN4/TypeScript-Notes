# Chapter 7: Union Types and Type Guards

## Union Types - Multiple Possible Types

A **union type** allows a variable to have **one of several types**.

### Basic Union Type

```typescript
// A variable that can be either string or number
let id: string | number;

id = "user-123";  // ✅ Valid - string
id = 123;         // ✅ Valid - number
id = true;        // ❌ ERROR - boolean not in union
```

### Union with Functions

```typescript
function processId(id: string | number) {
  // What can we do here?
  // We can't assume it's a string OR a number
  // We need to check first
}
```

## Type Guards - Checking Union Types

When you have a union type, you often need to **check which type you actually have** before using type-specific operations.

### The `typeof` Guard

The most common way to narrow union types:

```typescript
function process(value: string | number) {
  if (typeof value === 'string') {
    // Inside this block, TypeScript knows value is string
    console.log(value.toUpperCase());  // ✅ string method
  } else {
    // Inside this block, TypeScript knows value is number
    console.log(value + 1);  // ✅ number operation
  }
}

process("hello");  // Output: HELLO
process(42);       // Output: 43
```

### Example: Flexible Function

```typescript
function getLength(value: string | number | boolean): number {
  if (typeof value === 'string') {
    return value.length;
  } else if (typeof value === 'number') {
    return Math.abs(value);  // Return absolute value
  } else {
    return value ? 1 : 0;    // Return 1 for true, 0 for false
  }
}

console.log(getLength("hello"));    // 5
console.log(getLength(-42));        // 42
console.log(getLength(true));       // 1
```

## Union with Literal Types

**Literal types** are specific values that a variable can be:

```typescript
// Status can only be one of these exact strings
type Status = "pending" | "approved" | "rejected";

function handleStatus(status: Status) {
  switch(status) {
    case "pending":
      console.log("Waiting for approval...");
      break;
    case "approved":
      console.log("Request approved!");
      break;
    case "rejected":
      console.log("Request rejected");
      break;
  }
}

handleStatus("pending");     // ✅ OK
handleStatus("approved");    // ✅ OK
handleStatus("unknown");     // ❌ ERROR - not in union
```

## Literal Type with Numbers

```typescript
type HttpStatus = 200 | 404 | 500;

function getStatusMessage(status: HttpStatus): string {
  switch(status) {
    case 200:
      return "OK";
    case 404:
      return "Not Found";
    case 500:
      return "Server Error";
  }
}

getStatusMessage(200);   // ✅ OK
getStatusMessage(404);   // ✅ OK
getStatusMessage(403);   // ❌ ERROR - not in union
```

## Union with Objects (Discriminated Unions)

When union members are objects, use a **discriminator** property:

```typescript
interface SuccessResponse {
  status: "success";
  data: any;
  message: string;
}

interface ErrorResponse {
  status: "error";
  code: number;
  message: string;
}

type ApiResponse = SuccessResponse | ErrorResponse;

function handleResponse(response: ApiResponse) {
  if (response.status === "success") {
    // TypeScript knows this is SuccessResponse
    console.log("Data:", response.data);
    console.log("Message:", response.message);
  } else {
    // TypeScript knows this is ErrorResponse
    console.log("Error code:", response.code);
    console.log("Error message:", response.message);
  }
}

const success: ApiResponse = {
  status: "success",
  data: { id: 1, name: "John" },
  message: "User fetched successfully"
};

const error: ApiResponse = {
  status: "error",
  code: 404,
  message: "User not found"
};

handleResponse(success);
handleResponse(error);
```

## Complex Example: Tea Order Union

```typescript
interface HotTea {
  type: "hot";
  temperature: number;
  flavor: string;
}

interface ColdTea {
  type: "cold";
  iceCubes: number;
  flavor: string;
}

interface BubbleTea {
  type: "bubble";
  pearlSize: string;
  flavor: string;
}

type TeaOrder = HotTea | ColdTea | BubbleTea;

function serveTea(order: TeaOrder): string {
  switch(order.type) {
    case "hot":
      return `Serving hot ${order.flavor} tea at ${order.temperature}°C`;
    case "cold":
      return `Serving cold ${order.flavor} tea with ${order.iceCubes} ice cubes`;
    case "bubble":
      return `Serving bubble tea with ${order.pearlSize} pearls`;
  }
}

const hotTea: TeaOrder = {
  type: "hot",
  temperature: 80,
  flavor: "Masala"
};

const coldTea: TeaOrder = {
  type: "cold",
  iceCubes: 10,
  flavor: "Ginger"
};

console.log(serveTea(hotTea));   // Serving hot Masala tea at 80°C
console.log(serveTea(coldTea));  // Serving cold Ginger tea with 10 ice cubes
```

## The `in` Operator for Type Guards

```typescript
interface Bird {
  fly(): void;
}

interface Fish {
  swim(): void;
}

type Animal = Bird | Fish;

function move(animal: Animal) {
  if ("fly" in animal) {
    // It's a Bird
    animal.fly();
  } else {
    // It's a Fish
    animal.swim();
  }
}
```

## instanceof Guard (For Classes)

```typescript
class Dog {
  bark() {
    console.log("Woof!");
  }
}

class Cat {
  meow() {
    console.log("Meow!");
  }
}

type Pet = Dog | Cat;

function makePetSound(pet: Pet) {
  if (pet instanceof Dog) {
    pet.bark();
  } else {
    pet.meow();
  }
}

makePetSound(new Dog());  // Woof!
makePetSound(new Cat());  // Meow!
```

## Exhaustive Checking

TypeScript can warn you if you don't handle all union cases:

```typescript
type Fruit = "apple" | "banana" | "orange";

function getColor(fruit: Fruit): string {
  switch(fruit) {
    case "apple":
      return "red";
    case "banana":
      return "yellow";
    case "orange":
      return "orange";
    // If we forget a case, TypeScript won't warn unless we add exhaustive checking
  }
}

// Exhaustive checking pattern
function getColorExhaustive(fruit: Fruit): string {
  switch(fruit) {
    case "apple":
      return "red";
    case "banana":
      return "yellow";
    // Forgot "orange" - TypeScript will warn here if strict enough
    default:
      const exhaustiveCheck: never = fruit;  // Error if not all cases handled
      return exhaustiveCheck;
  }
}
```

## Union Type Best Practices

### ✅ Good - Discriminated Union
```typescript
type Result = 
  | { status: "loading" }
  | { status: "success"; data: any }
  | { status: "error"; error: string };

function handle(result: Result) {
  if (result.status === "success") {
    console.log(result.data);
  }
}
```

### ❌ Bad - Unclear Union
```typescript
type BadResult = string | number | boolean | object;

function handle(result: BadResult) {
  // What can we do with this?
  // We don't know what to expect
}
```

## Key Takeaways

1. **Union types** allow multiple possible types: `string | number`
2. **Type guards** narrow types: `typeof`, `instanceof`, `in`
3. **Literal types** specify exact values: `"success" | "error"`
4. **Discriminated unions** use a property to identify the type
5. **Exhaustive checking** ensures all union members are handled
6. **Type narrowing** makes TypeScript smarter about types

---

**Next:** We'll explore arrays, tuples, and working with collections.
