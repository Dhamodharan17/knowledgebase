## Object Oriented Principles

* [OOPs Fundamentals](./oops/oops.md)
  - [Classes and Objects](#classes-and-objects)
  - [Enums](#enums)
  - [Interfaces](#interfaces)
  - [Encapsulation](#encapsulation)
  - [Abstraction](#abstraction)
  - [Inheritance](#inheritance)
  - [Polymorphism](#polymorphism)
  - [Class Relationships](#class-relationships)
    - [Association](#association)
    - [Aggregation](#aggregation)
    - [Composition](#composition)
    - [Dependency](#dependency)
    - [Realization](#realization)
    - [The Golden Rule: Composition Over Inheritance](#the-golden-rule-composition-over-inheritance)
---


### Classes and Objects

#### Class
1. A class is a blueprint, template, or recipe for creating objects. It defines **what an object will contain (its data) and what it will be able to do (its behavior).**
2. Class - A class is not an object itself, it’s a **template used to create many objects with similar structure but independent state.**

#### Object/Instance
1. An object is an **instance of a class**.  It's the actual thing **you can interact with, store data in, and invoke methods on.**
2. Each **object gets its own copy of the data** defined in the class, **shares the same structure and behavior, and operates independently** of every other object created from that same class.

<details>
$1



```java
import java.util.ArrayList;
import java.util.List;

class FoodOrder {
    private String orderId;
    private String customerName;
    private List<String> items;
    private double totalAmount;
    private boolean isPlaced;

    public FoodOrder(String orderId, String customerName) {
        this.orderId = orderId;
        this.customerName = customerName;
        this.items = new ArrayList<>();
        this.totalAmount = 0.0;
        this.isPlaced = false;
    }

    // Only allows adding items before the order is placed
    public void addItem(String name, double price) {
        if (isPlaced) {
            System.out.println("Cannot modify a placed order.");
            return;
        }
        items.add(name);
        totalAmount += price;
    }

    // Places the order if it has at least one item and hasn't been placed yet
    public boolean placeOrder() {
        if (isPlaced || items.isEmpty()) {
            return false;
        }
        isPlaced = true;
        return true;
    }

    public int getItemCount() {
        return items.size();
    }

    public void displayOrder() {
        String status = isPlaced ? "PLACED" : "PENDING";
        System.out.println("Order " + orderId + " (" + customerName + ") - " + status);
        for (String item : items) {
            System.out.println("  - " + item);
        }
        System.out.printf("  Total: $%.2f%n", totalAmount);
    }
}

// Usage
public class FoodApp {
    public static void main(String[] args) {
        // order 1 - instance 1 
        FoodOrder order1 = new FoodOrder("ORD-101", "Alice");
        order1.addItem("Pizza", 12.99);
        order1.addItem("Garlic Bread", 4.99);
        order1.addItem("Coke", 2.49);
        order1.placeOrder();

        // order 2 - instance 2
        FoodOrder order2 = new FoodOrder("ORD-102", "Bob");
        order2.addItem("Burger", 9.99);
        order2.addItem("Fries", 3.99);
        // Bob hasn't placed his order yet

        order1.displayOrder();
        System.out.println();
        order2.displayOrder();
    }
}
```
**Why This Design Works**
1. Encapsulates order state: Items, total, and placement status live together. **No need to track these in separate data structures.**
2. Enforces business rules: The addItem() method prevents modifications after placement. **The object protects its own invariants.**
3. Reusable across the platform: One class handles every customer order allowing you to **create thousands of order objects from the same blueprint.**
4. Easy to extend: Need to add delivery addresses, payment methods, or order tracking later? **Add new fields and methods without restructuring your entire codebase.**
</details>

---



### Enums

An **enum** (short for enumeration) is a special data type that **defines a fixed set of named constants**. Unlike strings or integers, **enums are type-safe, meaning the compiler enforces that you can only assign values** explicitly declared in the defined set.

#### Why Use Enums?

Using raw strings or integers for status representations often leads to **hidden runtime bugs**:

```java
String status = "PENDING";

// Somewhere else in the codebase...
if (status.equals("PNDING")) {  // Typo! This condition is never true
    processOrder();
}

```

This code compiles without any warnings because `"PNDING"` is a valid string. **The bug only surfaces at runtime when an order fails to process.** Enums eliminate this entire class of bugs by moving validation to compile time.

#### Key Advantages

* **Eliminates Magic Values:** Replaces scattered strings or magic numbers with centralized, named constants which reduced bugs.
* **Improves Readability:** Self-documenting code like `OrderStatus.SHIPPED` makes intent clear.
* **Enforces Compiler Checks:** Catches typos and invalid assignments during compilation rather than at runtime.

<details>

```java
public enum PaymentMethod {
  // pass as many arguments into the parentheses as you want, provided you declare matching fields and constructor parameters inside the enum.
    CREDIT_CARD("Credit Card", 2.5),
    DEBIT_CARD("Debit Card", 1.0),
    UPI("UPI", 0.0),
    NET_BANKING("Net Banking", 1.5);

    private final String displayName;
    private final double feePercent;
  //The arguments in parentheses must match the signature of the enum's constructor in both order and type.
    PaymentMethod(String displayName, double feePercent) {
        this.displayName = displayName;
        this.feePercent = feePercent;
    }

    public String getDisplayName() { return displayName; }
    public double getFeePercent() { return feePercent; }
}

public class Order {
    private final String orderId;
    private OrderStatus status;
    private final PaymentMethod paymentMethod;
    private final double amount;

    public Order(String orderId, PaymentMethod paymentMethod, double amount) {
        this.orderId = orderId;
        this.paymentMethod = paymentMethod;
        this.amount = amount;
    }

    public void displayInfo() {
        System.out.println(paymentMethod.getDisplayName());//from enum
    }
}

public class Main {
    public static void main(String[] args) {
        Order order = new Order("ORD-001", PaymentMethod.CREDIT_CARD, 99.99);
        order.displayInfo(); // Output: Credit Card
        System.out.println(order.paymentMethod.getDisplayName())//from enum
    }
}

```
</details>

---

### Interfaces
1. At its core, an interface is a contract: a **list of methods** that any **implementing class must provide**
2. It **specifies a set of behaviors** that a class agrees to implement but **leaves the details of those behaviors up to each implementation.**

#### Key Properties of Interfaces
* Interfaces are more than just method declarations, they are the **foundation of flexible software design.**
1. Programming to the Interface  
  * Look at the CheckoutService constructor. It takes a PaymentGateway, not a StripePayment. This **single decision is what decouples the service from any specific provider.**
  * This pattern is called dependency injection: instead of creating its own dependencies, the class receives them from the outside. And **it only works because the dependency is typed as an interface, not a concrete class.**
2. Runtime Flexibility
   *  The CheckoutService didn't change between the two calls. The only thing that changed was which **implementation was plugged in.**
   *  That's the power of programming to interfaces: **the calling code is completely insulated from implementation details.**

#### Multiple Inheritence

When a class implements multiple interfaces, an instance of that class can masquerade as any of those interface types. The caller doesn't need to know anything about the concrete class—it only cares that the object guarantees the contract (methods) defined by the interface. That is the c**ore power of polymorphism paired with interface-based multiple inheritance.**
  <details>
  $1


  In real-world software architecture, the decision to make a class implement multiple interfaces is guided by the Interface Segregation Principle (ISP)—the idea that clients should not be forced to depend on methods they do not use.

  Instead of creating one massive interface with dozens of methods, engineers design small, focused interfaces representing specific capabilities or roles. A concrete class then implements multiple interfaces to assemble its complete set of capabilities.
</details>
<details>
$1



  ```java
  public class CheckoutService {
    private PaymentGateway paymentGateway; //Programming to the Interface  

    public CheckoutService(PaymentGateway paymentGateway) {
        this.paymentGateway = paymentGateway;
    }

    public void setPaymentGateway(PaymentGateway paymentGateway) {
        this.paymentGateway = paymentGateway;
    }

    public void checkout(double amount) {
        paymentGateway.initiatePayment(amount);
    }
  }

  public class Main {
    public static void main(String[] args) {
      // Runtime Flexibility
        PaymentGateway stripeGateway = new StripePayment();
        CheckoutService service = new CheckoutService(stripeGateway);
        service.checkout(120.50);  // Output: Processing payment via Stripe: $120.5

       // Switch to Razorpay
        PaymentGateway razorpayGateway = new RazorpayPayment();
        service.setPaymentGateway(razorpayGateway);
        service.checkout(150.50);  // Output: Processing payment via Razorpay: ₹150.5        
    }
}
  ```
Why This Design Works
* **Adding a new payment method is trivial**. Need PayU notifications? Create a PayUPayment class that implements NotificationService.** The CheckoutService works with it immediately, no modifications needed.**
* Each PaymentGateway is independently testable. You can **unit test RazorpayPayment to verify it oays correctly, without involving StripePayment or PayUPayment.**
* **The CheckoutService service is agnostic.** It doesn't import any PaymentGateway classes. It only knows about the PaymentGateway interface. This means you could move all the PaymentGateway implementations to a separate package or module, and CheckoutService would still compile without changes.
* Configuration drives behavior. In a real system, you'd read the payment channel from a config file or environment variable, create the appropriate payment, and inject it. The checkout logic stays completely untouched regardless of which channel is active.
</details>

---
   
### Encapsulation
1. Practice of grouping data (variables) and **behavior (methods) that operate on that data** into a single unit (typically a class) and **restricting direct access to the internal details of that class.**
2. In a well-encapsulated design, **external code doesn't need to know how something is done. It only needs to know what can be done.** (exposes behavior, not data).

```java
public class Booking {
    public enum Status { CONFIRMED, CHECKED_IN, CHECKED_OUT, CANCELLED }
    // grouping data (variables)
    private final String id;
    private final RoomType roomType;
    private final String guestName;
    private final LocalDate checkIn;
    private final LocalDate checkOut;
    private Status status = Status.CONFIRMED;


  // behavior (methods) that operate on that data
    public List<LocalDate> nights() {
        return checkIn.datesUntil(checkOut).toList();
    }

  //behavior (methods) that operate on that data
    public void cancel() { status = Status.CANCELLED; }

    //  getters
    public RoomType getRoomType() { return roomType; }
    public LocalDate getCheckIn() { return checkIn; }
    public LocalDate getCheckOut() { return checkOut; }
    public Status getStatus() { return status; }
    public String getId() { return id; }
}
```

**Why Encapsulation Matters ?** - Encapsulation isn’t just about data protection, it’s about designing systems that are robust, secure, and easy to maintain.
   1. Data Hiding - keeps this data private and accessible only through controlled methods.
   2.  Controlled Access and Validation (Boundary and Predictability) - It ensures that data can only be modified in controlled, predictable ways (not any part of code change data).
   3. Improved Maintainability - you can change the implementation (e.g., how data is stored or validated) without affecting the code that depends on it. since they know the method not the actual data, they will keep using those methods.
   4. Security and Stability - By preventing external tampering, encapsulation reduces the risk of inconsistent or invalid system states.

**How Encapsulation is Achieved ?**
1. access modifiers that control visibility, and getters/setters that provide controlled access to private data.
2. The general rule is simple: make everything private by default, then selectively expose what needs to be public.
3. Getters and Setters: public methods that provide controlled, indirect access to private attributes.

---

### Abstraction
   **Interfaces as Abstraction** - worth seeing how interfaces serve as a different kind of abstraction.  

   While **abstract classes abstract a family of related classes that share behavior** , **interfaces abstract a capability that unrelated classes** can share. 

  ```java
  public interface Exportable {
  String export();
  }

  public class CSVExporter implements Exportable {
  public String export() {
      return "name,email,age\nAlice,alice@example.com,30";
  }
  }

  public class JSONExporter implements Exportable {
  public String export() {
      return "{\"name\": \"Alice\", \"email\": \"alice@example.com\"}";
  }
  }
```

The Exportable interface doesn't share any behavior between exporters. Ther**e's no common formatting logic, no shared fields. It purely defines the contract**: "anything that claims to be exportable must have an export() method." Any code that needs to export data depends on the Exportable interface, not on CSVExporter or JSONExporter directly.
| Feature / Aspect | Abstract Class | Interface |
| :--- | :--- | :--- |
| **State** | Can have instance variables (object-specific state). | Cannot have instance state (only `public static final` constants). |
| **Access Control** | Can have `protected`, `private`, or `public` methods for internal reuse. | Methods are `public` by default (no `protected` behavior). |
| **Use Case** | Use when you need shared state and controlled, reusable logic. | Use when defining a pure capability/contract without shared state. |
| **Inheritance** | Supports single inheritance only (`extends`). | Supports multiple inheritance (`implements`). |

> **Key Takeaway:** Use an **abstract class** when behavior depends on internal object state or requires `protected` helper methods. Use an **interface** for defining shared capabilities across unrelated classes.


**Abstraction vs Encapsulation** - Abstraction is the external view of an object, while Encapsulation is the internal view.

| Aspect | Encapsulation | Abstraction |
| :--- | :--- | :--- |
| **Focus** | Protecting data within a class | Hiding implementation complexity |
| **Goal** | Restrict access to internal state | Simplify usage and expose only essentials |
| **Level** | Implementation-level | Design-level |
| **Example** | Private `balance` field in `BankAccount` | Exposing only `deposit()` and `withdraw()` without showing how they work |
---

### Inheritance

Inheritance enables code reuse by letting you define common logic once in a base class and then extend or specialize it in multiple derived classes.

#### Why Inheritance Matters

* **Code Reusability:** Embodies the DRY (Don't Repeat Yourself) principle. **Common logic is written once in the parent class** and shared across all subclasses, reducing redundancy.
* **Logical Hierarchy:** Creates a **clear and intuitive hierarchy** that models real-world "is-a" relationships, like `ElectricCar` is a `Car` or `Admin` is a `User`.
* **Ease of Maintenance:** If a **bug is found or a change is needed in the shared logic**, you only need to fix it in one place: the superclass. All subclasses automatically inherit the fix.
* **Polymorphism:** Serves as a prerequisite for polymorphism, **allowing objects of different subclasses to be treated as objects of the superclass.**

#### How Inheritance Works

When a class inherits from another:

1. The subclass inherits all non-private fields and methods of the superclass.
2. The subclass can override inherited methods to provide a different implementation.
3. The subclass can also extend the superclass by adding new fields and methods.

This allows for both reuse and customization.

#### When to Avoid Inheritance

* **"Has-a" or "Uses-a" Relationships:** Avoid inheritance when the relationship is "has-a" or "uses-a" rather than "is-a". A `Car` *has an* `Engine`, it is not an `Engine`. A `Printer` *uses a* `Logger`, it is not a `Logger`.
* **Combining Dynamic Behaviors:** Avoid it when you want to combine behaviors from multiple sources dynamically. Inheritance locks you into a single parent at compile time, while composition lets you mix and match components freely.
* **Runtime Flexibility Needed:** Avoid it when you need runtime flexibility to swap behaviors. With composition, you can inject different implementations (such as swapping a `FileLogger` for a `ConsoleLogger`). With inheritance, the parent relationship is fixed.
* **Tight Coupling Risks:** Avoid it when you want to prevent tight coupling between child and parent internals **. Changes to a parent class** ripple down to every child in the hierarchy, which is risky in large codebases.

<details>
  $1


**Combining behaviors** means assembling different capabilities like building blocks, rather than locking a class into a single static parent hierarchy.

---

### The Problem: Trying to Combine with Inheritance

Imagine you are building an `ECommerceOrder` system and need combinations of two behaviors: **Discount Types** (Seasonal, VIP) and **Notification Types** (Email, SMS).

If you try to combine these using inheritance, you face **class explosion**:

```java
// You are forced to create every possible combination as a unique class:
class SeasonalDiscountEmailOrder extends Order { ... }
class SeasonalDiscountSMSOrder extends Order { ... }
class VIPDiscountEmailOrder extends Order { ... }
class VIPDiscountSMSOrder extends Order { ... }

```

If you add a third discount type or notification method, the number of classes multiplies exponentially.

---

### The Solution: Combining Behaviors with Composition

Instead of inheriting, you compose an `Order` by injecting the specific behaviors it needs as interfaces:

```java
interface DiscountStrategy { double apply(double price); }
interface NotificationService { void notify(String msg); }

public class Order {
    private DiscountStrategy discount;      // Behavior 1
    private NotificationService notifier;  // Behavior 2

    // Plug-and-play assembly via constructor
    public Order(DiscountStrategy discount, NotificationService notifier) {
        this.discount = discount;
        this.notifier = notifier;
    }

    public void checkout(double amount) {
        double finalPrice = discount.apply(amount);
        notifier.notify("Order processed for: $" + finalPrice);
    }
}

```

### How You Combine Them

You can now freely mix and match any combination of behaviors dynamically without creating new classes:

```java
// Combination 1: VIP Discount + Email Notification
Order order1 = new Order(new VIPDiscount(), new EmailNotifier());

// Combination 2: Seasonal Discount + SMS Notification
Order order2 = new Order(new SeasonalDiscount(), new SMSNotifier());

// Combination 3: No Discount + Push Notification
Order order3 = new Order(new NoDiscount(), new PushNotifier());

```

### Why This Wins

* **No Class Explosion:** 2 discounts + 2 notification types = **4 classes total** instead of a massive inheritance tree.
* **Plug and Play:** You can combine any `DiscountStrategy` with any `NotificationService` instantly.

  </details>
  
---
### Polymorphism

Polymorphism allows the same method name or interface to **exhibit different behaviors depending on the object that is invoking it.**

Polymorphism lets you call the same method on different objects, and have each object respond in its own way.

1. Compile-time Polymorphism (Method Overloading)
Compile-time polymorphism, also called method overloading, happens when you have multiple methods with the same name in the same class but with different parameter lists.

2. Runtime Polymorphism (Method Overriding / Dynamic Dispatch)
Runtime polymorphism is the more powerful and more important form. It happens when a child class overrides a method defined in its parent class, and the decision of which version to call is made at runtime based on the actual type of the object, not the declared type of the reference.

**Polymorphism with Interfaces vs Abstract Classes**  
Both interfaces and abstract classes enable polymorphism. In the notification example, you could define Notification as either an abstract class or an interface. **The polymorphic behavior, calling send() on a base reference and having the child's version execute**, works the same either way. 

---
