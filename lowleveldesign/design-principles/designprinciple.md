# Core Design Principles

- [Core Design Principles](#core-design-principles)
    - [DRY Principle - Don’t Repeat Yourself](#dry-principle---dont-repeat-yourself)
      - [The Rule of Three](#the-rule-of-three)
      - [Why Repetition Is a Problem](#why-repetition-is-a-problem)
      - [When it is Okay to Repeat](#when-it-is-okay-to-repeat)
    - [KISS Principle](#kiss-principle)
      - [Why Complexity Is Dangerous](#why-complexity-is-dangerous)
      - [Signs You’re Violating KISS](#signs-youre-violating-kiss)
      - [How to Apply the KISS Principle](#how-to-apply-the-kiss-principle)
      - [When Not to Simplify](#when-not-to-simplify)
    - [YAGNI](#yagni)
      - [Why Premature Work Is Harmful](#why-premature-work-is-harmful)
      - [When to Bend the Rule](#when-to-bend-the-rule)
    - [Law of Demeter](#law-of-demeter)
    - [Example](#example)
    - [Separation of Concerns (SoC)](#separation-of-concerns-soc)

### DRY Principle - Don’t Repeat Yourself

> > “Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.” — The Pragmatic Programmer

- The DRY principle says that each piece of knowledge in your system should live in exactly one place. When you need that knowledge somewhere else, you reference the single source rather than creating a second copy.
- Notice the quote says "knowledge," not "code."
  - Business rules, Configuration, Data models(multiple data model to access same table), Single Documentation, Shared Test setup logic.

> > Redundancy makes your system harder to maintain and more prone to bugs.

#### The Rule of Three

- The idea is simple. Before extracting shared logic, wait until you see the same pattern three times.
- Two occurrences might be coincidental. Maybe those two pieces of code look **similar today but will diverge tomorrow** as their respective features evolve. **Three occurrences, though, that is a pattern.**

At that point, you have strong evidence that the duplication represents **genuine shared knowledge, and extracting it into a single location** is the right call.

#### Why Repetition Is a Problem

- Harder to Maintain - Missing even one copy leads to inconsistent behavior that is difficult to trace.
- Higher Risk of Bugs - More copies mean more chances for errors.(may become invalid inconsitent)
- Bloated Codebase - codebase harder to navigate and understand.
- Poor Test Coverage - you need three sets of tests to cover the same behavior

> > Copy-Paste Is a Red Flag

Copying and pasting code might seem convenient, but it often leads to long-term problems.

> > Ask yourself: If I need to change this logic in the future, will I remember all the places where it exists?
> > If the answer is no or even uncertain, you are creating risk. Following the DRY principle reduces that risk.

#### When it is Okay to Repeat

- Avoid Premature Abstractions - Duplication is far cheaper than the wrong abstraction
- Keep Tests Readable - small amount of repetition is a reasonable trade-off for tests that tell their story from top to bottom.
- Keep It Simple - Don't overengineering.

<details>
<summary>violating dry</summary>

```java
public class OrderService {
    public void notifyOrderConfirmation(String userId, String orderId) {
        // Duplicated: message formatting
        String message = "[Order] Hi " + userId + ", your order "
            + orderId + " has been confirmed.";
        String formatted = message.toUpperCase().substring(0, 1)
            + message.substring(1);

        // Duplicated: sending logic
        System.out.println("Connecting to notification API...");
        System.out.println("Sending to " + userId + ": " + formatted);
        System.out.println("Notification sent successfully.");
    }

}

public class ShippingService {
public void notifyShipmentUpdate(String userId, String trackingId) {
// Duplicated: message formatting
String message = "[Shipping] Hi " + userId + ", your shipment " + trackingId + " is on its way.";
String formatted = message.toUpperCase().substring(0, 1) + message.substring(1);

        // Duplicated: sending logic
        System.out.println("Connecting to notification API...");
        System.out.println("Sending to " + userId + ": " + formatted);
        System.out.println("Notification sent successfully.");
    }

}

public class SupportService {
public void notifyTicketResolution(String userId, String ticketId) {
// Duplicated: message formatting
String message = "[Support] Hi " + userId + ", your ticket " + ticketId + " has been resolved.";
String formatted = message.toUpperCase().substring(0, 1) + message.substring(1);

        // Duplicated: sending logic
        System.out.println("Connecting to notification API...");
        System.out.println("Sending to " + userId + ": " + formatted);
        System.out.println("Notification sent successfully.");
    }

}

```

</details>

<details>
<summary>apply dry</summary>

```java
public class MessageFormatter {
     // applied dry
    public static String format(String category, String userId, String detail) {
        String message = "[" + category + "] Hi " + userId + ", " + detail;
        return message.substring(0, 1).toUpperCase() + message.substring(1);
    }
}

public class NotificationSender {
    // applied dry
    public static void send(String userId, String message) {
        System.out.println("Connecting to notification API...");
        System.out.println("Sending to " + userId + ": " + message);
        System.out.println("Notification sent successfully.");
    }
}

public class OrderService {
    public void notifyOrderConfirmation(String userId, String orderId) {
         // applied dry
        String message = MessageFormatter.format(
            "Order", userId, "your order " + orderId + " has been confirmed.");
        NotificationSender.send(userId, message);
    }
}

public class ShippingService {
    public void notifyShipmentUpdate(String userId, String trackingId) {
         // applied dry
        String message = MessageFormatter.format(
            "Shipping", userId, "your shipment " + trackingId + " is on its way.");
        NotificationSender.send(userId, message);
    }
}

public class SupportService {
    public void notifyTicketResolution(String userId, String ticketId) {
        String message = MessageFormatter.format(
            "Support", userId, "your ticket " + ticketId + " has been resolved.");
        NotificationSender.send(userId, message);
    }
}
```

Why This Design Works

1. **Single source of truth for formatting**. If you need to **change the message template** (for example, adding a timestamp or changing the greeting), you update MessageFormatter once.
2. **Single source of truth for sending.** If the notification API changes (new endpoint, new authentication, retry logic), you update NotificationSender once.
3. **Each service focuses on its own responsibility.** OrderService knows about orders. ShippingService knows about shipments. Neither knows the details of formatting or sending.
4. Easy to test. Yo**u can unit test MessageFormatter and NotificationSender in isolation. You can mock them when testing the services.**
5. Easy to extend. **Adding a BillingService that also sends notifications requires zero changes to the existing code.** Just call MessageFormatter.format() and NotificationSender.send().

</details>

---

### KISS Principle

- The idea was straightforward: **most systems work best when they are kept simple**. Unnecessary complexity introduces failure points, slows down understanding, and makes things harder to fix when they break.

In software, KISS means writing code that is:

1. Easy to read without spending 30 minutes tracing through abstractions.
2. Easy to understand. **The logic flows naturally.** There are no surprises, no hidden side effects, no clever tricks.
3. Easy to change. When requirements shift, you can modify the code confidently without worrying about breaking something three layers deep.

- The simpler the code, the fewer the bugs. The fewer the bugs, the more reliable the system. And the more reliable the system, the less time your team spends firefighting instead of building.

> > Build for the problem you have, not the problem you imagine.
> > Add complexity when simplest solution doesn't work.

#### Why Complexity Is Dangerous

1. Harder to Read
2. More Places for Bugs to Hide - An overengineered calculator has six classes that all need to be correct. A simple calculator has one. The math is straightforward: less code, fewer bugs.
3. Poor Debuggability - When something breaks in simple code, you set a breakpoint, step through the method, and find the issue.
4. Slower Onboarding

#### Signs You’re Violating KISS

- You added an interface before you had a second implementation.
- The Pragmatic Approach: Start with a concrete class. If you ever find yourself needing to swap that class out for a different one, extract an interface at that exact moment. Until then, keep it simple.
- You used recursion when a loop would have been simpler and clearer.
- Your code has more boilerplate than business logic.

if you recognize any of these, it is worth pausing and asking: "Is there a simpler way to do this?"

#### How to Apply the KISS Principle

- Write Code for Humans, Not Machines
- Avoid Premature Abstraction - Abstractions are powerful tools, but they should emerge from repetition or clear need, not from imagination.
- Favor Composition Over Inheritance - Flat, composed structures are almost always simpler and more flexible than Deep inheritance hierarchies.
- Keep Functions Short - As a guideline, if you cannot describe what a function does in a single sentence without using the word "and," it is probably doing too much. Split it.
- Use Familiar Constructs - Do not reinvent the wheel when a simple List, a Map, a for loop can do the job.

#### When Not to Simplify

There are legitimate cases where a certain amount of complexity is not just acceptable, it is necessary.

1. Don't Oversimplify Critical Systems - A payment processing system should have thorough input validation, transaction logging, and error handling, even if that adds complexity. Cutting corners in the name of simplicity can lead to data corruption, security vulnerabilities, or financial loss.
2. Avoid Duplicating Logic Just to Keep Things "Simple" - Sometimes developers avoid creating a shared utility method because it feels like "adding abstraction." But if the same validation logic exists in five places and a rule changes, you now have five places to update. A small, well-named helper function is simpler in the long run than scattered duplication.

> > KISS and DRY often work together. The goal is to find the simplest solution that does not repeat itself unnecessarily.

> > The goal is not to write the simplest possible code. It is to write the simplest sufficient code.

---

### YAGNI

YAGNI is a principle that encourages you to resist the temptation to build features or add flexibility until you are absolutely sure you need them.

> > In simple terms: Don’t build for tomorrow. Build for today. Most it will wrong.

#### Why Premature Work Is Harmful

- Wasted Time and Effort - That's development time, code review time, and testing time, all spent on features with zero users.
- Increased Complexity
- Delayed Value
- Higher Maintenance Costs

#### When to Bend the Rule

Like all principles, YAGNI has exceptions. Sometimes, planning ahead is justified. The key is distinguishing between speculative features (driven by "what if") and known constraints (driven by real requirements, regulations, or contractual obligations).

1. Security and Compliance of the domain
2. Architecture with Known Long-Term Constraints of the domain.
3. Reusable Libraries or Frameworks - If you're building a library that other teams will depend on, some flexibility is expected. API design for **libraries requires more upfront thought because breaking changes affect many consumers.** But even here, start with a minimal API and expand it based on actual usage patterns.

---

### Law of Demeter

A method should only talk to immediate friends; it shouldn't access objects in a nested way.

### Example

- ❌ `order.getCustomer().getAddress().getZipCode()`
- `order.getCustomerZipCode()`

---

### Separation of Concerns (SoC)

Different parts of code handle different responsibilities.

- **UI** $\rightarrow$ No business logic
- **BE** $\rightarrow$ Shouldn't know how data is stored
- **DB** $\rightarrow$ Shouldn't work on display format

---
