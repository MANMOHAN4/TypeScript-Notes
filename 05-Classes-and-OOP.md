# Chapter 5: Classes and Object-Oriented Programming

## Basic Class Structure in TypeScript

Classes in TypeScript work similar to JavaScript, but with **type annotations and access modifiers**.

### Simple Class Definition

```typescript
class Tea {
  flavor: string;

  constructor(flavor: string) {
    this.flavor = flavor;
  }
}
```

Breaking this down:
- `flavor: string` - Property declaration with type
- `constructor(flavor: string)` - Constructor with parameter type
- `this.flavor = flavor` - Initialize property

### Creating an Instance

```typescript
const masalaTea = new Tea("Masala");
const gingerTea = new Tea("Ginger");
```

When you create an instance and hover over it, TypeScript shows you what properties are available.

## Access Modifiers - Controlling Visibility

TypeScript provides three access modifiers to control how properties and methods can be accessed:

### 1. Public (Default)

```typescript
class Tea {
  public flavor: string;  // Can be accessed from anywhere

  constructor(flavor: string) {
    this.flavor = flavor;
  }
}

const tea = new Tea("Chai");
console.log(tea.flavor);  // ✅ OK - public property
tea.flavor = "Ginger";    // ✅ OK - can modify
```

**Public means:**
- Accessible from outside the class
- Can be read and modified by anyone
- Default if no modifier specified

### 2. Private (Encapsulation)

```typescript
class Tea {
  public flavor: string;
  private secretIngredients: string;  // Only accessible inside this class

  constructor(flavor: string) {
    this.flavor = flavor;
    this.secretIngredients = "Cardamom";
  }
}

const tea = new Tea("Chai");
console.log(tea.flavor);              // ✅ OK
console.log(tea.secretIngredients);   // ❌ ERROR: private property
```

**Private means:**
- Only accessible within the class
- Cannot access from outside
- Cannot modify from outside
- Use for sensitive or implementation details

### 3. Protected (Inheritance-based)

```typescript
class Tea {
  protected formula: string;  // Accessible in this class and subclasses

  constructor() {
    this.formula = "Secret Formula";
  }
}

class PremiumTea extends Tea {
  constructor() {
    super();
    console.log(this.formula);  // ✅ OK in subclass
  }
}

const premium = new PremiumTea();
console.log(premium.formula);    // ❌ ERROR: protected outside class
```

**Protected means:**
- Accessible within the class and its subclasses
- NOT accessible from outside
- Used for values needed by child classes

## Real-World Example: Tea Shop

```typescript
class Tea {
  // Public - everyone can see the flavor
  public flavor: string;
  public price: number;

  // Private - recipe is secret!
  private secretIngredients: string;

  constructor(flavor: string, price: number) {
    this.flavor = flavor;
    this.price = price;
    this.secretIngredients = "Cardamom, Ginger, Clove";
  }

  // Public method
  public serve(): void {
    console.log(`Serving delicious ${this.flavor} tea for ₹${this.price}`);
  }

  // Private method - only accessible internally
  private brew(): void {
    console.log(`Brewing with ${this.secretIngredients}`);
  }

  // Public method that uses private method
  public prepare(): void {
    this.brew();  // ✅ Can call private method here
    this.serve();
  }
}

const masalaTea = new Tea("Masala", 20);
masalaTea.serve();      // ✅ OK - public method
masalaTea.prepare();    // ✅ OK - public method that uses private

// These would error:
// masalaTea.brew();                // ❌ private
// masalaTea.secretIngredients;     // ❌ private
```

## Methods with Type Annotations

### Method Parameters

```typescript
class Calculator {
  add(a: number, b: number): number {
    return a + b;
  }

  concatenate(a: string, b: string): string {
    return a + b;
  }

  greet(name: string): string {
    return `Hello ${name}`;
  }
}

const calc = new Calculator();
calc.add(5, 3);                    // ✅ OK
calc.add("5", 3);                  // ❌ ERROR: string is not assignable to number
```

### Method Return Types

```typescript
class Service {
  // Explicit return type
  getName(): string {
    return "Service Name";
  }

  // Void return (no return value)
  printInfo(): void {
    console.log("Some information");
    // No return statement
  }

  // Returns boolean
  validate(value: string): boolean {
    return value.length > 0;
  }

  // Returns array
  getItems(): string[] {
    return ["item1", "item2"];
  }
}
```

## The `this` Keyword

In TypeScript, `this` refers to the current instance of the class.

```typescript
class User {
  name: string;
  email: string;

  constructor(name: string, email: string) {
    this.name = name;      // 'this' refers to the current User instance
    this.email = email;
  }

  getInfo(): string {
    return `${this.name} - ${this.email}`;
  }
}

const user = new User("John", "john@example.com");
console.log(user.getInfo());  // "John - john@example.com"
```

## Shorthand Property Initialization

TypeScript provides a convenient shorthand:

```typescript
// Verbose way
class User {
  name: string;
  email: string;

  constructor(name: string, email: string) {
    this.name = name;
    this.email = email;
  }
}

// Shorthand way - TypeScript automatically creates properties
class User {
  constructor(
    public name: string,
    public email: string
  ) {}
}

// Both produce identical code!
```

## Class Inheritance

```typescript
// Parent class
class Animal {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  speak(): void {
    console.log(`${this.name} makes a sound`);
  }
}

// Child class
class Dog extends Animal {
  breed: string;

  constructor(name: string, breed: string) {
    super(name);  // Call parent constructor
    this.breed = breed;
  }

  speak(): void {
    console.log(`${this.name} barks`);
  }

  getInfo(): string {
    return `${this.name} is a ${this.breed}`;
  }
}

const dog = new Dog("Max", "Golden Retriever");
dog.speak();        // "Max barks"
console.log(dog.getInfo());
```

## Static Properties and Methods

Static members belong to the class itself, not to instances:

```typescript
class Configuration {
  static readonly API_URL = "https://api.example.com";
  static readonly VERSION = "1.0.0";

  static getConfig(): string {
    return `API: ${Configuration.API_URL}, Version: ${Configuration.VERSION}`;
  }
}

console.log(Configuration.API_URL);     // ✅ OK - access via class
console.log(Configuration.getConfig()); // ✅ OK - call static method

const config = new Configuration();
// console.log(config.API_URL);  // ❌ Wrong way - use Configuration.API_URL
```

## Abstract Classes

Abstract classes cannot be instantiated but can be extended:

```typescript
abstract class Shape {
  abstract calculateArea(): number;

  describe(): void {
    console.log(`Area is: ${this.calculateArea()}`);
  }
}

class Circle extends Shape {
  constructor(private radius: number) {
    super();
  }

  calculateArea(): number {
    return Math.PI * this.radius * this.radius;
  }
}

// const shape = new Shape();  // ❌ ERROR: Cannot instantiate abstract class
const circle = new Circle(5);
circle.describe();  // Area is: 78.53981633974483
```

## Complete Tea Shop Example

```typescript
class TeaShop {
  private teaList: Tea[] = [];
  private totalSales: number = 0;

  addTea(tea: Tea): void {
    this.teaList.push(tea);
  }

  sellTea(flavorIndex: number): void {
    if (flavorIndex >= 0 && flavorIndex < this.teaList.length) {
      const tea = this.teaList[flavorIndex];
      this.totalSales += tea.price;
      console.log(`Sold: ${tea.flavor} for ₹${tea.price}`);
    }
  }

  getTotalSales(): number {
    return this.totalSales;
  }

  listTeaOptions(): void {
    this.teaList.forEach((tea, index) => {
      console.log(`${index}: ${tea.flavor} - ₹${tea.price}`);
    });
  }
}

const shop = new TeaShop();
shop.addTea(new Tea("Masala", 20));
shop.addTea(new Tea("Ginger", 15));
shop.addTea(new Tea("Green", 25));

shop.listTeaOptions();
shop.sellTea(0);
shop.sellTea(2);

console.log(`Total Sales: ₹${shop.getTotalSales()}`);
```

## Key Takeaways

1. **Classes** organize code with properties and methods
2. **Access modifiers** control visibility:
   - `public` - accessible from anywhere (default)
   - `private` - only within the class
   - `protected` - within class and subclasses
3. **Constructor** initializes properties
4. **Methods** need parameter and return types
5. **`this`** refers to the current instance
6. **Inheritance** extends parent classes with `extends`
7. **Abstract classes** define interfaces for subclasses
8. **Static members** belong to the class, not instances

---

**Next:** We'll explore interfaces - defining contracts for object shapes.
