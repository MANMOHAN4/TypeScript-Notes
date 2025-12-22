# Chapter 6: Interfaces - Defining Object Contracts

## What is an Interface?

An **interface** is a **contract** that defines the shape of an object. It specifies:
- What properties an object should have
- What type each property should be
- Which properties are required vs optional
- What methods an object should implement

Think of an interface as a **blueprint** for an object.

## Basic Interface Definition

```typescript
interface User {
  name: string;
  age: number;
  email: string;
}

const user: User = {
  name: "John",
  age: 30,
  email: "john@example.com"
};
```

Here:
- `User` is the interface
- Any object with type `User` MUST have `name`, `age`, and `email`
- Each property must be the correct type

## Using Interfaces with Objects

### Valid Object

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
}

const laptop: Product = {
  id: 1,
  name: "MacBook Pro",
  price: 1299
};

// ✅ All required properties present with correct types
```

### Invalid Objects

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
}

// ❌ Missing 'price' property
const phone: Product = {
  id: 2,
  name: "iPhone"
};

// ❌ 'price' is string, should be number
const tablet: Product = {
  id: 3,
  name: "iPad",
  price: "799"
};

// ❌ Extra property 'color' not in interface
const watch: Product = {
  id: 4,
  name: "Apple Watch",
  price: 399,
  color: "silver"
};
```

## Optional Properties

Properties can be optional using the **question mark (`?`)**:

```typescript
interface Config {
  host: string;           // Required
  port: number;           // Required
  username?: string;      // Optional
  password?: string;      // Optional
  timeout?: number;       // Optional
}

// ✅ Valid - required properties present
const config1: Config = {
  host: "localhost",
  port: 3000
};

// ✅ Valid - includes optional properties
const config2: Config = {
  host: "localhost",
  port: 3000,
  username: "admin",
  password: "secret"
};

// ✅ Valid - partial optional properties
const config3: Config = {
  host: "localhost",
  port: 3000,
  timeout: 5000
};
```

## Readonly Properties

```typescript
interface DatabaseConfig {
  readonly host: string;
  readonly port: number;
  maxConnections: number;  // Not readonly, can be changed
}

const dbConfig: DatabaseConfig = {
  host: "localhost",
  port: 5432,
  maxConnections: 100
};

dbConfig.maxConnections = 200;  // ✅ OK - not readonly
dbConfig.host = "remotehost";   // ❌ ERROR - readonly property
```

## Function Type in Interfaces

Interfaces can define function signatures:

```typescript
interface Logger {
  log(message: string): void;
  error(message: string, code: number): void;
  warn(message: string): boolean;
}

const consoleLogger: Logger = {
  log(message: string): void {
    console.log(message);
  },
  error(message: string, code: number): void {
    console.error(`Error ${code}: ${message}`);
  },
  warn(message: string): boolean {
    console.warn(message);
    return true;
  }
};
```

## Interface Inheritance

Interfaces can extend other interfaces:

```typescript
interface Animal {
  name: string;
  sound: string;
}

interface Dog extends Animal {
  breed: string;
  wagTail(): void;
}

// Any object of type Dog MUST have:
// - name (from Animal)
// - sound (from Animal)
// - breed (from Dog)
// - wagTail() method

const myDog: Dog = {
  name: "Max",
  sound: "Bark",
  breed: "Golden Retriever",
  wagTail() {
    console.log("Tail wagging!");
  }
};
```

## Multiple Interface Inheritance

```typescript
interface Named {
  name: string;
}

interface Aged {
  age: number;
}

interface Person extends Named, Aged {
  email: string;
}

const person: Person = {
  name: "John",
  age: 30,
  email: "john@example.com"
};
```

## Interfaces with Methods

```typescript
interface Repository {
  find(id: number): object;
  save(data: object): void;
  delete(id: number): boolean;
  update(id: number, data: object): void;
}

class UserRepository implements Repository {
  find(id: number): object {
    return { id, name: "User" };
  }

  save(data: object): void {
    console.log("Saving user", data);
  }

  delete(id: number): boolean {
    console.log("Deleting user", id);
    return true;
  }

  update(id: number, data: object): void {
    console.log("Updating user", id, data);
  }
}
```

## Interfaces vs Types

While both define shapes, there are subtle differences:

```typescript
// Interface
interface User {
  name: string;
  age: number;
}

// Type (similar)
type UserType = {
  name: string;
  age: number;
};

// Key difference: Interfaces can extend other interfaces
interface Admin extends User {
  role: "admin";
}

// Types can use union
type UserOrAdmin = User | Admin;

// Interfaces can be merged
interface User {
  email?: string;  // Adds email to User
}

// Types cannot be merged
```

**General Rule:** Use **interfaces** for object shapes, use **types** for unions and primitives.

## Real-World Example: React Component Props

```typescript
interface CardProps {
  title: string;
  description: string;
  imageUrl: string;
  isActive?: boolean;
  onClick?: () => void;
}

function Card(props: CardProps) {
  return (
    <div className={props.isActive ? "active" : ""}>
      <h2>{props.title}</h2>
      <p>{props.description}</p>
      <img src={props.imageUrl} alt={props.title} />
      {props.onClick && (
        <button onClick={props.onClick}>Click me</button>
      )}
    </div>
  );
}

// Using the component
<Card
  title="Tea"
  description="Delicious chai"
  imageUrl="tea.jpg"
  isActive={true}
  onClick={() => console.log("Clicked")}
/>
```

## Practical Tea Shop Interfaces

```typescript
interface Tea {
  name: string;
  flavor: string;
  price: number;
  isHot: boolean;
}

interface TeaOrder {
  teaId: number;
  quantity: number;
  customerName: string;
  totalPrice: number;
}

interface TeaShop {
  name: string;
  location: string;
  availableTeas: Tea[];
  takeOrder(tea: Tea, quantity: number): TeaOrder;
  serveOrder(order: TeaOrder): void;
}

class ChaiShop implements TeaShop {
  name = "Chai Express";
  location = "Mumbai";
  availableTeas: Tea[] = [
    { name: "Masala", flavor: "spiced", price: 20, isHot: true },
    { name: "Ginger", flavor: "ginger", price: 15, isHot: true },
  ];

  takeOrder(tea: Tea, quantity: number): TeaOrder {
    return {
      teaId: 1,
      quantity,
      customerName: "Guest",
      totalPrice: tea.price * quantity
    };
  }

  serveOrder(order: TeaOrder): void {
    console.log(`Serving ${order.quantity} teas for ₹${order.totalPrice}`);
  }
}
```

## Interface Composition

Combining multiple interfaces:

```typescript
interface Timestamps {
  createdAt: Date;
  updatedAt: Date;
}

interface Identity {
  id: number;
  uuid: string;
}

interface User extends Timestamps, Identity {
  name: string;
  email: string;
}

const user: User = {
  id: 1,
  uuid: "abc-123",
  name: "John",
  email: "john@example.com",
  createdAt: new Date(),
  updatedAt: new Date()
};
```

## Key Takeaways

1. **Interfaces** define contracts for object shapes
2. **Properties** are required by default
3. **Optional properties** use `?` notation
4. **Readonly properties** cannot be modified
5. **Functions** can be typed in interfaces
6. **Inheritance** allows extending interfaces
7. **Implementation** via `implements` keyword
8. **Interfaces** can't be instantiated, only used as types

---

**Next:** We'll explore unions, literal types, and advanced type patterns.
