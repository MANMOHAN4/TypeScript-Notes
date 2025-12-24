# Chapter 8: Arrays and Collections

## Basic Array Type Annotation

Arrays in TypeScript use the **bracket notation** `[]` after the type:

```typescript
const numbers: number[] = [1, 2, 3, 4, 5];
const strings: string[] = ["a", "b", "c"];
const booleans: boolean[] = [true, false, true];
```

### Alternative Syntax

```typescript
// Both are equivalent
const numbers: number[] = [1, 2, 3];
const numbers: Array<number> = [1, 2, 3];

// Generic notation is less commonly used for primitives
```

## Array Type Safety

TypeScript ensures you don't add wrong types to arrays:

```typescript
const numbers: number[] = [1, 2, 3];

numbers.push(4);      // OK - number
numbers.push("five"); // ERROR - string is not assignable to number

const mixed: (string | number)[] = [1, "two", 3];
mixed.push(4);        // OK
mixed.push("five");   // OK
mixed.push(true);     // ERROR - boolean not in union
```

## Array Methods with Type Safety

All array methods are type-aware:

```typescript
const numbers: number[] = [1, 2, 3, 4, 5];

// map - transforms array
const doubled = numbers.map(n => n * 2);  // Type: number[]

// filter - creates subset
const even = numbers.filter(n => n % 2 === 0);  // Type: number[]

// forEach - iterates
numbers.forEach(n => {
  console.log(n * 2);
});

// find - returns single element
const first = numbers.find(n => n > 3);  // Type: number | undefined

// reduce - accumulates
const sum = numbers.reduce((acc, n) => acc + n, 0);  // Type: number
```

## Array of Objects

```typescript
interface Tea {
  name: string;
  price: number;
}

const teas: Tea[] = [
  { name: "Masala", price: 20 },
  { name: "Ginger", price: 15 },
  { name: "Green", price: 25 }
];

// TypeScript ensures each element has correct shape
teas.push({ name: "Chamomile", price: 30 });     // OK
teas.push({ name: "Black" });                     // ERROR - missing price
teas.push({ name: "Herbal", price: "10" });      // ERROR - price must be number
```

## Readonly Arrays

```typescript
const colors: readonly string[] = ["red", "blue", "green"];

colors.push("yellow");   // ERROR - readonly array
colors[0] = "purple";    // ERROR - can't modify

// Readonly array as parameter
function printArray(arr: readonly number[]): void {
  arr.forEach(item => console.log(item));
  // arr.push(1);  // Not allowed
}
```

## Tuples - Fixed-Length Arrays

A **tuple** is an array with a **fixed length and specific types** at each position:

### Basic Tuple

```typescript
// A tuple with exactly 2 elements: string and number
type StringNumberTuple = [string, number];

const pair: StringNumberTuple = ["hello", 42];  // OK
const pair: StringNumberTuple = ["hello"];      // ERROR - missing second element
const pair: StringNumberTuple = ["hello", "42"]; // ERROR - second must be number
```

### Tuple with Multiple Types

```typescript
type UserTuple = [id: number, name: string, email: string];

const user: UserTuple = [1, "John", "john@example.com"];

// With labels (TypeScript 4.0+)
type HTTPResponse = [statusCode: number, body: string];
const response: HTTPResponse = [200, '{"data": "..."}'];
```

### Accessing Tuple Elements

```typescript
const [status, message]: [number, string] = [200, "OK"];
console.log(status);   // 200
console.log(message);  // "OK"
```

## Optional Elements in Tuples

```typescript
// First two required, third optional
type FlexibleTuple = [string, number, boolean?];

const a: FlexibleTuple = ["hello", 42];        // OK
const b: FlexibleTuple = ["hello", 42, true];  // OK
const c: FlexibleTuple = ["hello"];            // ERROR - missing second element
```

## Variadic Tuples

```typescript
// Rest parameter in tuple - any number of strings at the end
type StringNumberStrings = [string, number, ...string[]];

const tuple1: StringNumberStrings = ["hello", 42];
const tuple2: StringNumberStrings = ["hello", 42, "a", "b", "c"];
const tuple3: StringNumberStrings = ["hello"];  // ERROR - missing number
```

## Array Union Types

```typescript
type NumOrStr = number[] | string[];

const a: NumOrStr = [1, 2, 3];      // OK - number array
const b: NumOrStr = ["a", "b"];     // OK - string array
const c: NumOrStr = [1, "a"];       // ERROR - mixed types

// Type narrowing
function process(arr: NumOrStr) {
  if (typeof arr[0] === 'number') {
    // It's a number array
    arr.forEach(item => item.toFixed(2));
  } else {
    // It's a string array
    arr.forEach(item => item.toUpperCase());
  }
}
```

## Mixed Type Arrays

```typescript
// Array that can contain strings, numbers, or booleans
type Mixed = (string | number | boolean)[];

const mixed: Mixed = [1, "hello", true, 42, "world"];

// Type narrowing in iteration
mixed.forEach(item => {
  if (typeof item === 'string') {
    console.log(item.toUpperCase());
  } else if (typeof item === 'number') {
    console.log(item * 2);
  } else {
    console.log(!item);
  }
});
```

## Real-World Example: Tea Shop Inventory

```typescript
interface Tea {
  id: number;
  name: string;
  price: number;
  inStock: boolean;
}

interface Order {
  teaIds: number[];
  customerName: string;
  totalPrice: number;
}

class TeaShop {
  inventory: Tea[] = [];
  orders: Order[] = [];

  addTea(tea: Tea): void {
    this.inventory.push(tea);
  }

  getTea(id: number): Tea | undefined {
    return this.inventory.find(tea => tea.id === id);
  }

  getAvailableTeas(): Tea[] {
    return this.inventory.filter(tea => tea.inStock);
  }

  createOrder(teaIds: number[], customerName: string): Order | null {
    const selectedTeas = teaIds
      .map(id => this.getTea(id))
      .filter((tea): tea is Tea => tea !== undefined);

    if (selectedTeas.length === 0) return null;

    const totalPrice = selectedTeas.reduce((sum, tea) => sum + tea.price, 0);

    const order: Order = {
      teaIds,
      customerName,
      totalPrice
    };

    this.orders.push(order);
    return order;
  }
}

// Usage
const shop = new TeaShop();
shop.addTea({ id: 1, name: "Masala", price: 20, inStock: true });
shop.addTea({ id: 2, name: "Ginger", price: 15, inStock: true });
shop.addTea({ id: 3, name: "Green", price: 25, inStock: false });

const available = shop.getAvailableTeas();
console.log(`Available: ${available.length} teas`);

const order = shop.createOrder([1, 2], "John");
if (order) {
  console.log(`Order total: ₹${order.totalPrice}`);
}
```

## Common Array Mistakes to Avoid

### Mistake 1: Wrong Type Inference
```typescript
// TypeScript infers this as (string | number)[]
const mixed = [1, "two"];

// Better: Be explicit
const mixed: (string | number)[] = [1, "two"];
```

### Mistake 2: Empty Array
```typescript
// Type is any[] - too loose!
const items = [];

// Better: Be explicit
const items: string[] = [];
```

### Mistake 3: Modifying Readonly
```typescript
function process(items: readonly string[]) {
  // items.push("new");  // Not allowed
  // const copy = [...items];  // Create a copy instead
}
```

### Good Practices
```typescript
// Clear type annotation
const numbers: number[] = [];

// Use readonly for parameters
function printArray(items: readonly string[]): void {
  items.forEach(item => console.log(item));
}

// Use tuples for fixed structures
type Coordinates = [x: number, y: number, z: number];
const position: Coordinates = [10, 20, 30];
```

## Key Takeaways

1. **Array types** use bracket notation: `number[]`
2. **Type safety** ensures correct element types
3. **Tuples** are fixed-length arrays: `[string, number]`
4. **Optional elements** in tuples: `[string, number?]`
5. **Union arrays** can mix types: `(string | number)[]`
6. **Readonly arrays** prevent modification
7. **Array methods** preserve type information
8. **Type narrowing** helps with union array elements

---

**Next:** We'll explore generics - writing flexible, reusable code.
