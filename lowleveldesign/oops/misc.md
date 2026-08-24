### One to Many Bounded Context

Your interviewer was criticizing the violation of the **Single Responsibility Principle (SRP)** and turning the `User` class into a **"God Object."**

When a single domain entity holds every piece of travel data, your core application code becomes bloated, hard to maintain, and tightly coupled.

---

### Why the Interviewer Objected

- **God Object Anti-Pattern:** A `User` class should represent **identity and authorization** (who the person is). Making it manage ticketing systems, visa regulations, and passport expiration dates makes `User` responsible for the entire business domain.
- **Database Performance Bottleneck:** Fetching a `User` from the database to perform a simple task (like updating an email address) could unnecessarily load heavy lists of tickets, visas, and passports into memory (N+1 query problem / huge payload overhead).
- **High Churn:** If the rules for how tickets work change, you have to modify the `User` class. If visa rules change, you edit `User` again. A class should have only _one_ reason to change.

---

### How to Refactor It

Instead of hanging every list directly off `User`, decouple the travel domain using **bounded contexts** or **wrapper objects**.

#### Approach 1: Bounded Contexts / Separate Services (Best for Enterprise/Microservices)

Keep `User` lean, and fetch related domain objects independently using their own repositories/services.

```java
// Core User entity stays minimal
class User {
    private Long id;
    private String name;
    private String email;
}

// Separate domain entity pointing back to User
class Ticket {
    private Long id;
    private Long userId; // Simple reference instead of direct object embedding
    private String flightNumber;
}

```

_To get a user's tickets, you ask `TicketRepository.findByUserId(userId)` rather than pulling them through `user.getTickets()`._

#### Approach 2: Travel Profile / Aggregate Wrapper (Best for Domain-Driven Design)

If you must group travel items together in code, encapsulate them in a dedicated profile or container class.

```java
class User {
    private Long id;
    private String name;
    private TravelProfile travelProfile; // Encapsulates travel documents
}

class TravelProfile {
    private List<Passport> passports = new ArrayList<>();
    private List<Visa> visas = new ArrayList<>();
    private List<Ticket> tickets = new ArrayList<>();
}

```

---

### What to Say in an Interview

> _"You're completely right. Putting all travel documents into the `User` class violates SRP and turns it into a God Object. In a production environment, I would decouple these by querying `VisaService` or `TicketRepository` separately using `userId`, keeping the core `User` entity lean and preventing massive object graphs from loading into memory."_

**Yes, exactly.** Mathematically and logically, it is still a **One-to-Many** relationship, but it is implemented using **id-based reference** (or standard foreign keys) rather than **in-memory object embedding**.

---

**Comparing the Two Implementations**

| Metric                  | Object Embedding (`User` has `List<Ticket>`)        | ID Reference (`Ticket` has `userId`)                 |
| ----------------------- | --------------------------------------------------- | ---------------------------------------------------- |
| **Relationship Type**   | One-to-Many                                         | One-to-Many                                          |
| **In-Memory Coupling**  | High (loading `User` loads every ticket)            | Low (loading `User` loads only user data)            |
| **Database Match**      | Requires ORM mapping magic to join tables           | Matches how relational databases work natively       |
| **Microservices Ready** | No (`User` and `Ticket` must live in the same code) | Yes (`TicketService` can live on a different server) |

---

**Why Seniors and Interviewers Prefer the ID Reference Way**

1. **Mirrors Database Foreign Keys:** In your SQL database, the `tickets` table has a `user_id` column. The `users` table does _not_ have a list of ticket IDs. The code above mirrors the actual database architecture directly.
2. **Prevents Memory Bloat:** If a frequent flyer accumulates 500 tickets over five years, calling `user.getTickets()` loads 500 objects into memory every time you just want to check the user's name.
3. **Decouples Boundaries:** `User` handles user stuff. `Ticket` handles ticketing stuff. If you need a user's tickets, you ask your `TicketRepository` or `TicketService`:

```java
// Fetch tickets on demand, only when needed
List<Ticket> userTickets = ticketRepository.findByUserId(user.getId());

```
