# Low-Level Design (LLD) Interview Strategy Guide

A systematic, time-boxed playbook for clearing Object-Oriented Design (OOD) and Low-Level Design interview rounds without falling into over-engineering traps.

---

## 1. The Anti-Pattern: Sequencing Failure

Many candidates fail LLD rounds not from a lack of coding skills, but due to bad interview execution.

```

Common Trap Sequence:

1. Hear "Design X" -> Immediately draw classes (ParkingLot, Spot, Ticket, Vehicle).
2. Write deep class hierarchies (Truck extends Vehicle, ElectricCar extends Vehicle).
3. Add boilerplate design patterns (PaymentStrategy, SpotObserver).
4. Result: 30 minutes in, candidate has 15 classes that do nothing.
5. Interviewer asks: "How do I execute the main flow?" -> Silence.

```

**Core Principle:** Design top-down driven by behavior, not bottom-up driven by passive data models.

---

## 2. Phase 1: Requirements Gathering (5 Minutes)

Ask intent-driven questions to establish the system boundary and core execution flow before touching the board.

**Key Clarifications to Ask:**

- **Primary User & Action:** Who is using the system, and what is their main goal?
- **Success Metric:** What does success look like in one sentence?
- **Concurrency & Scale:** Is this a single-user system, or are there multi-threaded/concurrent requests?
- **Persistence:** Are objects held purely in memory, or is database/storage integration expected?

> **The 20-Second Scope Restatement:**
> Always close Phase 1 by restating the scope back to the interviewer:
> _"So we are building a multi-level parking lot with a single entry/exit, hourly billing, and no reservations. Is that correct?"_

---

## 3. Phase 2: Entity Extraction (5 Minutes)

Extract top-level domain nouns from the agreed scope. Keep entities minimal and validate them using the **One-Line Responsibility Rule**.

- Write a single line defining what each entity does.
- If an entity cannot earn a unique one-line responsibility based on the immediate requirements, **exclude it**.

| Candidate Entity   | One-Line Responsibility Check                                           | Decision |
| :----------------- | :---------------------------------------------------------------------- | :------- |
| **`ParkingLot`**   | Manages floors, entry/exit gates, and coordinates spot allocations.     | **Keep** |
| **`Ticket`**       | Tracks entry timestamp, vehicle ID, and assigned spot ID for billing.   | **Keep** |
| **`ElectricCar`**  | "It is a car that is electric." (No unique behavior required by scope). | **Drop** |
| **`SpotObserver`** | Observes spot changes. (Overkill; a simple boolean state handles this). | **Drop** |

---

## 4. Phase 3: Class Design & Implementation (20 Minutes)

Drive your design **Top-Down using the Facade Pattern**. Define the high-level control flow before implementing low-level leaf nodes.

### Step 1: Define the Facade / Main Controller First

Create the entry-point class that receives external events and exposes the primary operations.

```java
public class ParkingLotFacade {
    private final ParkingManager parkingManager;
    private final FeeCalculator feeCalculator;

    public Ticket enter(Vehicle vehicle) {
        // High-level orchestration for vehicle entry
        return parkingManager.assignSpotAndIssueTicket(vehicle);
    }

    public Receipt exit(Ticket ticket, PaymentDetails paymentInfo) {
        // High-level orchestration for vehicle exit
        double amount = feeCalculator.calculate(ticket);
        parkingManager.releaseSpot(ticket.getSpotId());
        return new Receipt(ticket.getId(), amount);
    }
}

```

### Step 2: Expand Inward to Leaf Nodes

Introduce data classes and helper entities only when required by the Facade's execution path.

```java
// Leaf node created strictly as a data carrier required by the flow
public class Ticket {
    private final String ticketId;
    private final String spotId;
    private final long entryTimestamp;

    public Ticket(String spotId) {
        this.ticketId = UUID.randomUUID().toString();
        this.spotId = spotId;
        this.entryTimestamp = System.currentTimeMillis();
    }
}

```

### Strategy Matrix: Facade-First vs. Leaf-First

| Approach                    | Method                                                                                 | Interview Outcome                                                                        |
| --------------------------- | -------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **Top-Down (Facade First)** | Start with entry point APIs (`enter()`, `exit()`), pulling in helper models as needed. | **Pass.** Validates end-to-end user flow early and demonstrates functional architecture. |
| **Bottom-Up (Leaf First)**  | Draft `Vehicle`, `Car`, `Spot`, `Coin` with getters/setters first.                     | **Fail.** Wastes 20 minutes on data models without implementing core execution flow.     |

---

## 5. Phase 4: System Walkthrough (10 Minutes)

Stop writing code and verbally trace an end-to-end transaction through the classes and methods on the board.

```
Walkthrough Execution Path:
1. Event: Car arrives at entry gate.
2. Call: ParkingLotFacade.enter(car)
3. Delegation: ParkingManager queries ParkingFloor for an available Spot matching CarSize.
4. State Change: Spot state set to occupied; Ticket generated with current timestamp.
5. Exit Event: Car arrives at exit gate 2 hours later.
6. Call: ParkingLotFacade.exit(ticket, payment)
7. Calculation: FeeCalculator computes duration and cost.
8. State Change: Spot marked free; Receipt returned.

```

---

## 6. Phase 5: Deep Dives & Trade-offs (5 Minutes)

Use the remaining time to address non-functional requirements and refactor cleanly without altering the core design framework:

- **Concurrency & Synchronization:** Discuss how spot allocation handles concurrent entry gates (e.g., using `ReentrantLock`, `ConcurrentHashMap`, or optimistic locking on spot state).
- **Design Pattern Integration:** Justify patterns used (e.g., **Strategy Pattern** for dynamic pricing algorithms, **Factory Pattern** for spot allocation rules).
- **Extensibility:** Explain how the system handles future requirements (e.g., adding valet parking or electric vehicle charging stations).

```

```
