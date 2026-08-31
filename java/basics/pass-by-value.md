Yes, the statement in the image is **100% true**, and what the interviewer told you is accurate for Java.

**Java is strictly pass-by-value.**

Because `String` is an object in Java, what gets passed into a method is **a copy of the reference value** (the memory address pointing to the object), not the reference itself.

---

**How Pass-By-Value Works for Objects and Strings**

| Data Type                                | What is stored in the variable?       | What gets copied to the method?      |
| ---------------------------------------- | ------------------------------------- | ------------------------------------ |
| **Primitives** (`int`, `double`)         | The actual raw value (e.g., `42`)     | A copy of the primitive value (`42`) |
| **Objects / Strings** (`String`, `List`) | A memory address pointing to the heap | A copy of that memory address        |

---

**Why Strings Feel "Special" (Immutability)**

Because Java passes a _copy_ of the memory address, modifying the internal state of a mutable object (like a `StringBuilder` or `ArrayList`) affects the original object outside the method.

However, `String` is **immutable**:

- If you perform a string operation like `str = str + " World"` inside a method, it **does not modify** the original String in memory.
- Instead, it creates a **brand-new `String` object** and points the method's local pointer to this new address.
- The original variable outside the method keeps pointing to the original memory address, leaving its value unchanged.

---

**Code Demonstration**

```java
public class Test {
    public static void main(String[] args) {
        String str = "Hello";
        modifyString(str);
        System.out.println(str); // Prints "Hello", NOT "Hello World"
    }

    public static void modifyString(String s) {
        // 's' received a copy of the memory reference pointing to "Hello".
        // Reassigning 's' creates a new object and updates the LOCAL copy of the reference.
        s = s + " World";
    }
}

```
