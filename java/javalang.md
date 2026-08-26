SCP

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
