# Garbage Collector

## Problem Statement

- when method finishes execution, removes all variables and references inside the same method stack since scope is within the method.
- **References are deleted what about objects in the heap?** Garbage Collector.
- Garbage collector run periodically and remove unreferenced objects in heap.
- JVM controls when to run Garbage Collector.
- System.gc() : hint to JVM to run garbage collector.

## Reference Types

1. Strong Reference - when garbage collector learns this object have strong reference it will ignore removing it.
2. Weak Reference - garbage collector will removed and keep values as null when it runs.`WeakReference<T>`
3. Soft Reference - type of weak reference, garbage collector removes it when it is very very urgent.(when there is not memeory) `SoftReference<T>`

## Making Object Elligble for GC

```java
public class GarbageCollectionExample {
    public static void main(String[] args) {
        String str1 = new String("Hello");
        String str2 = new String("World");

        // reassigning the reference variable
        str1 = str2; // Now "Hello" object is eligible for garbage collection

        // nullifying the reference variable
        str2 = null; // Now "World" object is eligible for garbage collection
    }
}
```

## Heap Memory

![Alt Text](/java/assests/heap-gc.png)

## GC Cycles

<details>
<summary>Click to view image</summary>

![Alt Text](/java/assests/gc-cycles.png)

</details>

### How It Works (Minor GC)

1. When **Eden** fills up, a **Minor GC** is triggered.
2. The JVM identifies live objects in Eden and the active Survivor space (e.g., S0) and **copies** them into the empty Survivor space (e.g., S1).
3. Eden and S0 are completely cleared. The roles of S0 and S1 flip.
4. Each time an object survives a Minor GC, its **age** increases by 1.

### How Objects Get Here (Promotion)

- **Aging Out:** If an object survives enough Minor GC cycles and reaches the **tenuring threshold** (configured via `-XX:MaxTenuringThreshold`, defaulting up to 15), it is promoted to the Old Generation.

- **Premature Promotion:** If a newly created object is too large to fit comfortably in the Eden space, it bypasses the Young Generation completely and is allocated directly in the Old Generation.

### How It Works (Major / Full GC)

1. When the Old Generation fills up, a **Major GC** or **Full GC** is triggered.
2. The GC cleans up unreferenced objects using algorithms like **Mark-Sweep-Compact**.
3. Because this space is much larger and objects are compacted to avoid fragmentation, these pauses can cause a "Stop-The-World" (STW) delay, momentarily freezing application threads.

| Feature                 | Young Generation                                                     | Old (Tenured) Generation                                  |
| :---------------------- | :------------------------------------------------------------------- | :-------------------------------------------------------- |
| **Object Lifespan**     | Short-lived (temporary, local variables, short sessions).            | Long-lived (caches, singletons, long-running services).   |
| **GC Type & Frequency** | **Minor GC** — Happens frequently.                                   | **Major GC / Full GC** — Happens less frequently.         |
| **Collection Speed**    | Very fast (milliseconds).                                            | Slower (requires scanning a much larger heap space).      |
| **GC Algorithm**        | Copying algorithms (e.g., scavenge).                                 | Mark-Sweep-Compact or region-based tracking.              |
| **Space Distribution**  | Divided into **Eden**, **Survivor 0 (S0)**, and **Survivor 1 (S1)**. | Continuous memory block (or managed as regions/segments). |

## Garbage Collector Algothrim

<details>
<summary>Click to view image</summary>

![Alt Text](/java/assests/mark-sweep.png)

</details>

1. Mark and Sweep
2. Mark and Sweep with compaction : make sequential objects, easy to put new objects.

## Types of Garbage Collector

| GC Version                               | Mechanism & Threads                                                                                                                             | Key Features & Impact                                                                             |
| :--------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------ |
| **1. Serial GC**                         | Single thread for Mark & Sweep.                                                                                                                 | • Stop-the-World (STW) pauses all application threads.<br>• High latency impact on larger heaps.  |
| **2. Parallel GC**<br>_(Java 8 Default)_ | Multiple threads scaling with CPU cores.                                                                                                        | • Maximizes throughput.<br>• Lower pause times than Serial, but still STW-based.                  |
| **3. Concurrent Mark & Sweep (CMS)**     | Runs marking/sweeping concurrently with app threads.                                                                                            | • Minimizes STW pauses.<br>• **No memory compaction** (causes fragmentation; deprecated/removed). |
| **4. G1 GC**<br>_(Java 9+ Default)_      | Region-based, concurrent marking with incremental compaction.(better version of cms)                                                            | • Targetable pause-time goals.<br>• Performs **memory compaction** to prevent fragmentation.      |
| **5. ZGC**                               | It divides the heap memory into multiple regions of variable size and performs almost all of its GC work concurrently with application threads. | • Ultra-low pause times (<1ms), scalable up to multi-terabyte heaps.                              |
| **6. Shenandoah GC**                     | Region-based with concurrent evacuation via Brooks pointers/load barriers.                                                                      | • Ultra-low pause times independent of heap size.                                                 |
| **7. Epsilon GC**                        | No-op garbage collector (allocates memory but never reclaims it).                                                                               | • Zero GC overhead.<br>• Useful for performance testing and short-lived tasks.                    |

<details>
<summary>Click to view image</summary>

![Alt Text](/java/assests/gc-types.png)

</details>

---

links:

1. local pdfs (downloads/documents/desktop)
   1. documents/articles/java

<https://www.linkedin.com/pulse/java-jvm-performance-tuning-ph%C3%A1t-l%C3%A0u>

<https://engineering.linkedin.com/garbage-collection/garbage-collection-optimization-high-throughput-and-low-latency-java-applications>

<https://www.instagram.com/p/DcNaNlgDVg6/?img_index=10>
<https://blog.ycrash.io/different-phases-of-garbage-collection-events/>
