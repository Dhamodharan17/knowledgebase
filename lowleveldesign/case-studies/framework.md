# Appraoch LLD Case Study

- walk into any "design a \_\_" question with repetable, time-boxed plan instead of improvising on the spot.

- Gather requirements, name entities, write classes, trace request through them and defend choices.

## Problem Statement

The candidate hears "design a parking lot." They nod, pick up the marker, and start drawing classes immediately.

`ParkingLot`, `ParkingSpot`, `Car`, `Ticket`. Then a `Vehicle` base class, then `Truck extends Vehicle`, `Bus extends Vehicle`, `ElectricCar extends Vehicle`. Then a `PaymentStrategy` because they read a book once. Thirty minutes in, they have a wall of classes that look impressive and do nothing. The interviewer asks "how do I park a car?" and the candidate goes silent, because nowhere in those classes is a method that answers that question.

The concrete faulure is not knowledge failure. It is a sequencing failure.

## Core Concepts

# 1. Requirements Gathering

Ask the interviewer**what the system does, who uses it, and what the most important flow is**. Always ask with intent:

State flow - covert requirement - create entity to make the flow work, create class to map , add field, ref and start coding and apply design patterns when needed.

---

> At the end of this phase, restate the scope back in one or two sentences.
>
> _"So we have a multi-level parking lot, single entry and exit, hourly billing, and we're not handling reservation. Is that right?"_
>
> That restatement is the cheapest insurance policy in the whole interview. It takes twenty seconds and it means everything you draw afterward is agreed upon.

# 2. Entities

- Noun Extraction (Topdown)
- Example : there is parking lot with multiple floors each have spots to park of different size and vehicle find suitable spot after taking ticket.
- Get objects from characteristics of system e.g. Parking Lot, it has floors Parking Floors, floors has spots Parking Spot, spots has different type. (Start with whole system and derive the need).
- Decide user flow 1st - Write the flow and identify the object needed for your flow.

# 3. Class Design + Design Pattern

- start and end flow like selectproduct() / dispenseproduct() do they i have start with facade or main class 1st
- Before creating a class, write down its responsibility in a single clear sentence.
- write the classes that carry the story of the system.
- A class only earns space if an external request passes through it. delete the rest.
  Yes, start with the main orchestrator (the Facade or Controller class) and drive your design **top-down** through the main user flow.

Do **not** start by building bottom-up leaf nodes like `Coin`, `Shelf`, or `Product` with endless getters.

---

### Step 1: Start with the Main Orchestrator (Facade)

Create the single high-level entry point that represents the user's primary actions. For a Vending Machine, this is your `VendingMachine` or `VendingMachineController` class.

Write the core entry-point signatures first:

```java
public class VendingMachine {
    public void insertCoin(Coin coin) { ... }
    public void selectProduct(String code) { ... }
    public Product dispenseProduct() { ... }
}

```

---

### Step 2: Expand Inward to Fulfill the Flow

Once the entry points are clear, ask yourself: _"What classes are required to execute `selectProduct()` and `dispenseProduct()`?"_

Now you add supporting entities **only** as needed to make those methods work:

- `selectProduct(code)` needs an `Inventory` to check stock and a `State` handler to verify if enough money was inserted.
- `dispenseProduct()` needs a `Shelf` to release the item and a `PaymentProcessor` to calculate change.

---

### Comparison: Top-Down vs. Bottom-Up

| Strategy                    | Approach                                                                                   | Interview Outcome                                                                                      |
| --------------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| **Top-Down (Facade First)** | Start with `VendingMachine.selectProduct()` $\rightarrow$ pull in `Inventory` and `State`. | **Pass.** Demonstrates clear API design, end-to-end flow, and functional execution early.              |
| **Bottom-Up (Data First)**  | Draft `Product`, `Coin`, `Shelf`, `Display` with full getters/setters first.               | **Fail.** Spends 25 minutes on boilerplate without ever proving how a product actually gets dispensed. |

---

### Summary

Start with the top-level **Facade/Controller**, define the 2–3 public API methods that drive the core use cases, and let those methods dictate what internal helper entities you actually need to build.

<details>
<summary> why bottom up is trap in interview</summary>
No, in an LLD interview, starting with leaf nodes is a trap.

While bottom-up domain modeling works well when writing production code with a full database schema, doing it in a 45-minute interview usually leads to running out of time before showing how the system actually works.

---

### Why Leaf-First Fails in Interviews

If you start by writing `Coin`, `Product`, `Shelf`, and `Display` classes:

- You spend 15 minutes defining attributes, constructors, getters, and setters.
- You end up creating attributes you don't actually need (e.g., `Product.manufacturer`, `Coin.weight`).
- When the interviewer asks, _"How does `selectProduct()` change the machine state?"_, you haven't written a single line of state management logic yet.

---

### The Recommended Flow: Top-Down API Design, Bottom-Up Implementation

You don't ignore the leaf nodes; you just defer their details until the high-level flow demands them.

```
1. Name Entities (Nouns)       → Identify top-level & leaf entities on the whiteboard.
2. Draft Main API (Facade)    → Define public methods: dispenseProduct(), insertCoin().
3. Connect Leaf Objects       → Pass leaf nodes as params or state variables as you fill in methods.

```

---

### Concrete Example: Vending Machine Execution

**1. Define the High-Level Facade First**
Start with the main orchestrator so the interviewer sees your system boundary:

```java
public class VendingMachine {
    private State currentState;
    private Inventory inventory;

    public void selectProduct(String code) {
        // High-level flow: delegate to state and inventory
        currentState.selectProduct(this, code);
    }
}

```

**2. Introduce Leaf Nodes ONLY as Data Holders**
Now that `selectProduct` needs a product, create a minimal leaf class without spending time on boilerplate:

```java
// Keep leaf nodes minimal—just data carriers
public class Product {
    private String name;
    private double price;

    // Minimal constructor & getters only as required by the flow
}

```

---

### Direct Comparison

| Approach                    | What Happens in the Interview                                                     | Outcome                                                                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **Leaf-First (Bottom-Up)**  | You design `Coin`, `Slot`, `Display`, `Product`, `Receipt` first.                 | You waste time on passive data models and run out of time to implement core business logic like state transitions or concurrency control. |
| **Facade-First (Top-Down)** | You design `VendingMachine.dispense()` and wire leaf objects into the parameters. | You prove the core use case works in 10 minutes, then flesh out leaf node details if time permits.                                        |

**Rule of Thumb:** Identify the leaf entity names during your initial entity brainstorming (Phase 2), but do not write their internal code until the top-level control flow requires them.

</details>

# Phase 4: Walkthrough (10 minutes)

Stop writing. Trace a real scenario through the classes you drew.

> _"A car enters. The gate attendant calls `parkingLot.enter(car)`. That asks the ground floor for a spot matching the car's size, marks the spot occupied, issues a ticket, and raises the gate. The driver parks, comes back two hours later, hands over the ticket, we look up the entry time, compute the fee, take the payment, free the spot."_

This walkthrough is where the interview is won or lost, and most candidates treat it as optional. **It is not optional.**

# 5. Trade-offs and close

- more on design pattern/ flexilibity/ multithreading
-
