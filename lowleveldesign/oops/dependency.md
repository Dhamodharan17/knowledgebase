# Dependency

- A Dependency exists when one class relies on another to**fulfill a responsibility for a moment**, but does **so without retaining a permanent reference to it**.
- it reflects a one-time interaction (short lived), often through method parameters, local variables, or return types.
- Unlike association, aggregation, or composition, **a dependency isn’t a structural “we belong together” relationship.** There’s no shared lifecycle and no long-term connection.
- "Uses-a" relationship: The class uses another to accomplish a task, but does not retain it.

**Real-World Analogy**

Imagine a Chef preparing a meal.

- The chef picks up a Knife to chop vegetables.
- Once the chopping is done, the knife is put away or reused elsewhere.
- **The chef doesn’t necessarily own the knife or keep it stored long-term.**
  This represents a dependency. The chef depends on the knife only during the cooking process.

```java
class Document {
    private String content;
}

class Printer {
    // Printer has no fields referencing Document

    public void print(Document document) {
        // The document exists only as a parameter inside the print() method.
        System.out.println("Printing: " + document.getContent());
    }
}

public class Main {
    public static void main(String[] args) {
        Document doc = new Document("Hello, World!");
        Printer printer = new Printer();

        printer.print(doc);

        // After print() returns, the printer has no reference to the document.
        // The document can be garbage collected independently of the printer.
    }
}
```

### Recognizing Dependencies in Code

As Method Parameters

```java
class ReportGenerator {
    //ReportGenerator depends on DataSource, but doesn't store it. The DataSource comes in, gets used, and is gone once generate() returns.
    public String generate(DataSource source) {
        List<String> data = source.fetchAll();
        // Format data into report...
        return formattedReport;
    }
}
```

As Local Variables

```java
class OrderProcessor {
    public void process(Order order) {
        //Sometimes a class creates another class inside a method, uses it, and discards it. The created object never escapes the method scope.
        JsonFormatter formatter = new JsonFormatter();
        String json = formatter.format(order);
        // Send json to external API...
    }
}
```

As Return Types

```java
class UserFactory {
    public User createUser(String name, String email) {

        //UserFactory depends on User because it creates and returns User objects, but it doesn't store any User as a field. The factory's job is to produce users, not to hold onto them.
        return new User(name, email);
    }
}
```

As Static Method Calls

```java
class PasswordService {
    public boolean verify(String input, String hash) {
        //PasswordService depends on HashUtils, but there's no instance of HashUtils stored anywhere.
        return HashUtils.sha256(input).equals(hash);
    }
}
```

### Example

```java
class TicketBookingService {
    // TicketBookingService has zero fields. Every collaborator comes in through bookTicket() and disappears when the method returns. This is pure dependency with no structural coupling.
    public boolean bookTicket(String eventId, String seatNumber, String email,
                              double amount, SeatValidator validator,
                              PaymentProcessor payment, QRCodeGenerator qrGenerator,
                              EmailService emailService) {
        if (!validator.isAvailable(eventId, seatNumber)) {
            System.out.println("Seat not available.");
            return false;
        }

        if (!payment.charge(email, amount)) {
            System.out.println("Payment failed.");
            return false;
        }

        String qrCode = qrGenerator.generate(eventId, seatNumber);
        emailService.sendConfirmation(email, qrCode);

        System.out.println("Booking confirmed!");
        return true;
    }
}

public class Main {
    public static void main(String[] args) {
        TicketBookingService bookingService = new TicketBookingService();

        // All dependencies are created externally and passed in
        SeatValidator validator = new SeatValidator();
        PaymentProcessor payment = new PaymentProcessor();
        QRCodeGenerator qrGenerator = new QRCodeGenerator();
        EmailService emailService = new EmailService();

        bookingService.bookTicket("CONF-2025", "A12", "alice@example.com",
            99.99, validator, payment, qrGenerator, emailService);
    }
}
```

But in production, we won't sit and create object instead we use Dependency Injection (DI)

### Dependency Injection (DI)

In dependency injection, a class receives its dependency from outside, usually through a constructor or setter.

For example:

```java
class NotificationService {
    private final Sender sender;

    public NotificationService(Sender sender) {
        this.sender = sender;
    }
}
```

Here, NotificationService does hold a reference to Sender.

But this is still called a dependency because:

NotificationService depends on Sender to do its work
it does not create Sender itself
the dependency is supplied externally
So DI is about how the dependency is provided, not about whether the class stores a reference.

**In Spring or other DI frameworks**
A bean can have different lifetimes:

Singleton, one shared instance for the whole app, very common
Prototype, a new instance each time requested
Request-scoped, one per HTTP request
Session-scoped, one per user session
So the bean is not “removed after use” in general. Its lifecycle depends on the scope.
