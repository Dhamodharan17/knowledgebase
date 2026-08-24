# Realization

- Realization is an "implements" relationship where a class fulfills a contract defined by an interface.
-

Use Realization (Interfaces) when:

1. Unrelated classes share a capability (Flyable, Serializable, Comparable)
2. Multiple inheritance of behavior is needed
   You want maximum flexibility and loose coupling
3. The contract matters more than shared implementation

Consider a backend system for a **Rental Fleet Management Application**. You have different types of assets, like a `Car` and a `Building`.

By making `Car` implement multiple interfaces (`Drivable`, `Insurable`, `Maintainable`), different parts of your software can work with the `Car` without knowing or caring about everything else it does.

---

### Code Example

```java
// 1. Interfaces representing distinct capabilities
interface Drivable {
    void drive(int miles);
}

interface Insurable {
    double calculateInsurancePolicy();
}

interface Maintainable {
    void performMaintenance();
}

// 2. Concrete class implementing multiple interfaces
class Car implements Drivable, Insurable, Maintainable {
    private double vehicleValue = 25000.0;
    private int mileage = 12000;

    @Override
    public void drive(int miles) {
        this.mileage += miles;
    }

    @Override
    public double calculateInsurancePolicy() {
        return vehicleValue * 0.05; // 5% of value
    }

    @Override
    public void performMaintenance() {
        System.out.println("Changing oil and rotating tires.");
    }
}

```

---

### How This Helps in Real-World Code

Implementing multiple small interfaces helps because **each service only sees what it needs to see** (Interface Segregation Principle).

```java
// The Insurance Service ONLY cares about things that can be insured.
// It doesn't care if it's a Car, a House, or a Boat.
class InsuranceService {
    public void processPolicy(Insurable item) {
        double cost = item.calculateInsurancePolicy();
        System.out.println("Policy cost: $" + cost);
    }
}

// The Repair Shop ONLY cares about things that can be maintained.
class MaintenanceService {
    public void serviceAsset(Maintainable asset) {
        asset.performMaintenance();
    }
}

```

---

### Core Benefits

- **Decoupling (Interface Segregation Principle):** `InsuranceService` doesn't need to know how to `drive()` a car. It only takes `Insurable` objects. This prevents services from calling methods they shouldn't access.
- **Polymorphism across unrelated classes:** A `Building` class can also implement `Insurable`. The `InsuranceService` can now process both a `Car` and a `Building` using the exact same `processPolicy()` method, even though a Car and a Building share no base parent class.
- **Avoids "God Interfaces":** Instead of creating one massive `VehicleInterface` with 30 methods, you break capabilities into modular, reusable blocks (`Drivable`, `Insurable`).

**Yes, exactly.** That is the single most powerful reason to use interfaces.

Because `Building` and `Car` have completely different inheritance trees, a `Building` cannot inherit from `Vehicle`. But since both can be insured, both can implement `Insurable`.

```java
class Building implements Insurable {
    private double propertyValue = 500000.0;

    @Override
    public double calculateInsurancePolicy() {
        return propertyValue * 0.02; // 2% for property insurance
    }
}

```

Now your insurance service can process both seamlessly without caring what kind of object it is:

```java
InsuranceService service = new InsuranceService();

Insurable myCar = new Car();
Insurable myBuilding = new Building();

// Both work with the exact same method!
service.processPolicy(myCar);
service.processPolicy(myBuilding);

```

### Why This Matters

- **No artificial inheritance:** You don't have to force `Building` and `Car` to share a common parent class like `Object` or a fake `Property` parent class.
- **Plug-and-play code:** If tomorrow you add a `Drone` class, just make it implement `Insurable`, and your `InsuranceService` will automatically work with it without modifying a single line of existing code.
