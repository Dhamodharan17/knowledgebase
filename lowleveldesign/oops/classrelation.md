
### Class Relationships
* In the real world, nothing exists in isolation.
    A doctor has patients.
    A driver has a car.
    A student enrolls in courses.
* our goal is to model this real world where objects** communicate and work together to achieve meaningful outcomes.**
  
### Association
* Association represents a relationship between two classes where **one object uses, communicates with, or references another.**
  
  Student * -- learns from -->(1..*) Teacher
  A student has-a teacher who teaches them.
    A teacher teaches multiple students.
    However:

    A student can still exist without a teacher.
    A teacher can still exist without any specific student.
    This is a real-world association:

    The relationship exists.
    But neither party owns the other.
    Their lifecycles are independent.
* reflects a "has-a" or "uses-a" relationship.
* objects are loosely coupled and can exist independently of one another.
*  can be unidirectional or bidirectional, and can follow different multiplicity patterns (1-to-1, 1-to-many, etc.).
  
  Types of Association
  * Associations between classes can vary depending on **how objects are connected** and in **which direction information flows.**
  *  associations are primarily defined by two key properties:
        Directionality — Who knows about whom?
        Multiplicity — How many objects are connected?
  1. Based on Direction (Directionality) - determines which class holds a reference to the other and whether communication is one-way or two-way.
     1. In a unidirectional association, only one class is aware of or holds a reference to the other class. The referenced class has no knowledge of who is referencing it. E.g. Order holds a reference to PaymentGateway and calls its method. But PaymentGateway has no field or reference pointing back to Order. 
     2. In a bidirectional association, both classes are aware of each other. Each class holds a reference to the other, enabling two-way communication. A Team has a list of Developers, and each Developer knows which Team they belong to. Either side can navigate to the other.
        1. In a bidirectional association, both references must stay in sync otherwise inconsitent state.
        ```java
        class Developer {
    private Team team;

    public void setTeam(Team team) {
        this.team = team;
    }
}

class Team {
    private List<Developer> developers = new ArrayList<>();

    public void addDeveloper(Developer dev) {
        #  updates both sides of the relationship
        developers.add(dev);
        dev.setTeam(this);
    }
}
```
2. Based on Multiplicity
* Multiplicity defines how many instances of one class can be associated with instances of another class. 
* It describes the quantity and nature of the connections.

---

### Aggregation

---

### Composition

---

### Realization

---

### The Golden Rule: Composition Over Inheritance