# SOLID Principles Overview

A concise summary of the SOLID software design principles with code examples (TypeScript/Java syntax).

---

## 1. Single Responsibility Principle (SRP)

> _A class should have one, and only one, reason to change._

- ❌ **Bad:** A `User` class that manages user data **and** saves it to a database.
- **Good:** Separate data representation from storage logic.

```typescript
// Good
class User {
  constructor(
    public name: string,
    public email: string,
  ) {}
}

class UserRepository {
  save(user: User) {
    // Database save logic
  }
}
```

---

## 2. Open/Closed Principle (OCP)

> _Software entities should be open for extension, but closed for modification._

- ❌ **Bad:** Adding `if/else` checks inside a core class every time a new feature is added.
- **Good:** Extend functionality using polymorphism without modifying existing code.

```typescript
// Good
interface PaymentMethod {
  pay(amount: number): void;
}

class CreditCard implements PaymentMethod {
  pay(amount: number) {
    /* pay via CC */
  }
}

class PayPal implements PaymentMethod {
  pay(amount: number) {
    /* pay via PayPal */
  }
}

class PaymentProcessor {
  process(payment: PaymentMethod, amount: number) {
    payment.pay(amount);
  }
}
```

---

## 3. Liskov Substitution Principle (LSP)

> _Subtypes must be substitutable for their base types without breaking the program._

- ❌ **Bad:** A `Square` extending `Rectangle` where setting width alters the height unexpectedly.
- **Good:** Derive classes only when child behavior honors parent expectations.

```typescript
// Good
interface Shape {
  getArea(): number;
}

class Rectangle implements Shape {
  constructor(
    private width: number,
    private height: number,
  ) {}
  getArea() {
    return this.width * this.height;
  }
}

class Square implements Shape {
  constructor(private side: number) {}
  getArea() {
    return this.side * this.side;
  }
}
```

---

## 4. Interface Segregation Principle (ISP)

> _Clients should not be forced to depend upon interfaces they do not use._

- ❌ **Bad:** A giant `Worker` interface with `work()` and `eat()` forced onto a `Robot`.
- **Good:** Split large interfaces into smaller, targeted ones.

```typescript
// Good
interface Workable {
  work(): void;
}

interface Eatable {
  eat(): void;
}

class Human implements Workable, Eatable {
  work() {}
  eat() {}
}

class Robot implements Workable {
  work() {} // No useless eat() implementation needed
}
```

---

## 5. Dependency Inversion Principle (DIP)

> _Depend upon abstractions, not concretions._

- ❌ **Bad:** High-level `Car` directly instantiating a specific `GasEngine`.
- **Good:** High-level classes depend on interface abstractions.

```typescript
// Good
interface Engine {
  start(): void;
}

class GasEngine implements Engine {
  start() {
    /* ... */
  }
}

class ElectricEngine implements Engine {
  start() {
    /* ... */
  }
}

class Car {
  constructor(private engine: Engine) {} // Inject via abstraction

  startCar() {
    this.engine.start();
  }
}
```
