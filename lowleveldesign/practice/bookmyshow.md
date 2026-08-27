Book my show

flow - user comes to appliction, book ticket

functional requirement
user should be able to view/search/book ticket.

user should be able to complete the payment with card/upi/internetbanking

system should be able to show only available tickets

system shouldn't allow mulitiple users to book same seat (atomicty)

system should be able to print ticket and update audit log.

system should time out if payment is not completed withing 10 minutes

user should be able to select spefic seat of choice

user should be able to search by city, movie, date, cinema

system should display respective theater, screen, show, seat layout

admin should be able to add, remove, edit movies, screen, show time.

pricing logic should be dynamic so admin can adjust based on demand

user should be able to cancel ticket before start of movie

Non-Functional Requirements

Modular, thread-safe, maintainable...

system should handle concurrent request

system should be atomic on ticket booking

correction 1 :

- no mention of theater, scree, show , seat management
- search is broad give search by movie, actor
- seat selection missing

correction 2:

- You did not defin**e admin or theater-operator use cases** for adding movies, screens, shows, and seat layouts, which affects show management modeling.
- You did not **mention pricing rules,** seat categories, or dynamic fare calculation, which matters because bookings usually depend on configurable pricing logic.
- You did not specify c**ancellation, refund, or failed-payment handling beyond timeout,** which would clarify important booking lifecycle transitions.

### Step 2

**What you did well**

- You identified User and Ticket, which anchors the customer actor and the final output of the booking flow clearly.
- You captured the Theatre, Screen, and Show hierarchy, which is essential for mapping cinema schedules to physical locations.
- Your Entertainment abstraction shows some extensibility thinking, since the same catalog could potentially support movies or concerts later.
  **What you can improve**
- Seat or SeatLayout is missing, and it is central because users must choose specific seats and see availability.
- Booking is missing, and it should connect user, show, selected seats, status, cancellation, ticket generation, and payment together.
- Payment is missing, and it matters for card, UPI, banking flows, pending status, timeout handling, refunds, and confirmation.

Step 3: (Data Class, Strategy class, Manager classes)
BookingAppFacade - searchMovie() - what all need for seach Entertainment of movie - selectMovie() - Theatre, Screen, Show - view shows() - SeatLayout, Seat - select ticket - Ticket - proceed with payment - payment - dispense ticket - booking

Entertaintment
movie/concert

Ticket
entry pass for movie

User
customer books ticket

Theatre
app contains lot of theatres

Screen
a theate contains lot of show

Show
each screen have multiple shows

SeatLayout
contains multiple seats

Seat
each unique sear

Booking
ledger of transaction

Payment
payment mode and information

SeatLock
temporary lock hold for a ticket

PricingStrategy
to handle different payment system
