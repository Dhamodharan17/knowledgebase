### SCP

- <https://medium.com/@bhangalekunal2631996/understanding-java-string-constant-pool-concepts-mechanisms-and-examples-with-diagrams-010122c7ced0>
- there are 2 areas strings are stored -> heap strings and scp strings
-

Literals automatically go to SCP, but strings created with new String() do not, so .intern() solves this.

- string created with new keyword goes to heap and scp
- string created with literal goes to scp
- we can move string from heap to scp using interm()
  <https://softaai.com/understanding-string-constant-pool-scp-in-java/>

  SCP = memory area inside heap only for string literals.

"literal" → goes to SCP.

new String("literal") → heap + SCP (if not already there).
The new String() object goes to the heap; the string literal "hello" used as its argument is in the SCP.

---

# Java Modifiers Reference Guide

A structured reference detailing Java class modifiers, member access levels, and behavior-defining keywords.

## Modifiers Summary Table

| Category                   | Modifier       | Scope & Target          | Key Rules & Features                                                                                                                        |
| :------------------------- | :------------- | :---------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| **Class**                  | `public`       | Top-level & Inner       | Accessible anywhere across packages.                                                                                                        |
|                            | `<default>`    | Top-level & Inner       | Package-private access only; cannot be imported outside package.                                                                            |
|                            | `final`        | Top-level & Inner       | Prevents subclass creation; methods inside are implicitly `final`.                                                                          |
|                            | `abstract`     | Top-level & Inner       | Prevents direct instantiation (`new`); can contain constructors for subclasses.                                                             |
|                            | `strictfp`     | Top-level & Inner       | Forces all floating-point logic in concrete methods to obey IEEE 754 standards.                                                             |
| **Access (Members)**       | `public`       | Methods, Fields         | Accessible everywhere (requires host class to be visible).                                                                                  |
|                            | `<default>`    | Methods, Fields         | Accessible only within the same package.                                                                                                    |
|                            | `private`      | Methods, Fields         | Visible only inside declaring class; **illegal** on `abstract` methods.                                                                     |
|                            | `protected`    | Methods, Fields         | Same package + outside package subclasses _(via subclass reference only)_.                                                                  |
| **Declaration & Behavior** | `final`        | Methods                 | Blocks child overriding.                                                                                                                    |
|                            | `final`        | Fields                  | **Instance:** Must initialize before constructor ends.<br>**Static:** Must initialize during class loading (declaration or `static` block). |
|                            | `final`        | Local / Parameters      | Must initialize before use; reference variables cannot rebind.                                                                              |
|                            | `abstract`     | Methods                 | Declaration only (no body); illegal with `static`, `final`, `native`, `synchronized`, `strictfp`, or `private`.                             |
|                            | `static`       | Methods, Fields, Blocks | Class-level memory allocation; no `this` reference. Access instance members via explicit object reference only.                             |
|                            | `native`       | Methods                 | Written in non-Java language (e.g., C/C++); no Java body.                                                                                   |
|                            | `synchronized` | Methods, Blocks         | Ensures single-thread execution via intrinsic locking.                                                                                      |
|                            | `transient`    | Fields                  | Excludes field from Object Serialization.                                                                                                   |
|                            | `volatile`     | Fields                  | Direct main-memory read/write; bypasses thread cache for visibility.                                                                        |

### Modifiers

**Class Modifer**

- for user defined classes, developer m**ust provide some information about our class** to the **JVM**.
- we can provide this information using corresponding modifiers.

| Question Answered for the JVM                                                     | Class Modifier |
| :-------------------------------------------------------------------------------- | -------------- |
| Whether this class can be accessed from anywhere (or) not?                        | `public`       |
| Whether access is restricted strictly to the current package (or) not?            | `<default>`    |
| Whether child class creation is possible (or) not?                                | `final`        |
| Whether direct object creation is possible (or) not?                              | `abstract`     |
| Whether floating-point operations adhere strictly to IEEE 754 standards (or) not? | `strictfp`     |

- Extra modifer for Inner classes are `private` , `proctected`, `static`.

**Access Modifers**

`public`

- accessible anywhere within or outside package.
- create object to access instance memebers and use class name to access static members.

`default`

- package level access, members/classes can be accessed within pacakage.
- JVM doesn't have default package because we cannot import and use in our package.

`final`

- can be used on classes, methods and variable members
- final method : to block children overrding parent implementation.
- final classes : to block creating child classes and use its data and behaviours.
- all methods inside final class is by default final but not variables.
- final variable as primitive is constant
- final variable as reference - can change values but cannot point to different objects.

  `abstract`
  - abstract is parital state where it is an interface and class.
  - applicable only for methods and classes not for variables.
  - abstract class : cannot create object but have constructor which is called when subclass object beign created.
    - if class contain, atleast one abstract make it abstract class.
    - abstract must not need to be contain abstract method.
    - final classes cannot contain abstract methods as no one extend provide implementation.
  - abstract methods, only declaration not implementation.
    - child classes responsible for implementation, if child cannot give implementation, make it abstract too and child of child should give implementation.
    - abstract never talks about implementation and modifiers talk about implementations are enemy (illegal) like static, final, strictfp, native, synchronised.
    - strictfp & abstract class -> legal (strictfp applicable to all concrete methods inside the class) and not applicable for implementation classes.

  `strictfp`
  - applicable for class/methods which solves the problem where result of floating point varying platform to platform.
  - strict fp classes - every method should follow IEEE 754 standard.
  - strict fp methods - all floating point calculation follows IEEE 754 standard.

**Member Modifer**

`public`

- can be access anywhere but class should be visible. if class is default, member is public we cannot access.

`default`

- members can be access only within current package.(package-private)

`private`

- visibile only within class.
- cannot use with abstract because abstract method should be visible outside class (child) for implementation.

`proctected`

- visibility : default (package-private) and children.
- case 1 : access within package - use reference of child or parent
- case 2 : access from outside package - only with child reference because proctected in parent

  `final`
  - instance variable + final : JVM will not give default value (must initise) why?
  - final instance variable - initalisatio should be performed before consutctor completion.
    - Option 1 : time of declration
    - Option 2 : instance block
    - Option 3 : constructor
    - else complie time issue
  - final + static : must initalise before class loading else complie time error.
    - Option 1 : time of declration.
    - Option 2 : static block
  - final + local variable - only modifer local, must initalise before using
  - used with method parameters - local variables so final can be used

  ```java
  void method1(final int, int y){
    //cannot change x
  }
  ```

  `static`
  - can be used with methods/blocks/variables/inner class
  - static variable : single copy of data at class level shared across all objects.
  - static variables can be accessed from both instance and static area directly with class name
  - instance members cannot be accessed by static area directly because static area loaded into memory at class load time before any objects created because there is no `this` reference in static area since it is common pool.
    <details>
    <summary>details</summary>
        How Class Loading vs. Object Creation Works

    Class Loading Phase: The JVM loads the class bytecodes, allocates memory for static variables, and executes static blocks sequentially.

    Explicit Instantiation Inside Static Area: When the JVM encounters new MyClass() inside a static block, it temporarily pauses the remaining static initialization, allocates memory for the new object on the heap, runs the instance initializer blocks and constructor, and returns the reference.

    Access via Reference: Once you hold that object reference, you can access instance fields and methods via objectRef.instanceVariable.

    ```java
    public class Demo {
        // Instance variable (created on heap when an object is instantiated)
        int instanceValue = 100;

        // Static block (runs at class loading time)
        static {
            //  ERROR: Cannot access instance variable directly
            // System.out.println(instanceValue);

            //  ALLOWED: Explicitly create an object inside the static area
            Demo obj = new Demo();

            // Access instance member using the object reference
            System.out.println("Accessed via reference: " + obj.instanceValue); // Prints 100
        }

        public static void main(String[] args) {
            // Triggers class loading if not loaded already
        }
    }
    ```

  </details>

- static method
  - implementation should be available so can be shared.
  - inheritence applicable if method not in child class, parent class implementation will be called.
  - overriding allowed but it is method hiding.
  - overloading applicable.
  - abstract not implementation so cannot be shared and cannot be static

  `native`
  - applicable only for methods which are non java
  - use alreading existing legacy non java code
  - achive machine level communication

    `synchronised`
    - applicable for methods and blocks where only one thread allowed inside it.
      `transient`
      - don't serliase particular variable (security)
        `volatile`
      - always fectch values from main memory instead of cache.

---
