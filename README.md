# KotlinContracts
**Plan**
1. Purposes
  * Consider what functionality we want to have
      | Name                         | Description      |
      |------------------------------|------------------|
      | Preconditions                | Checking if input data is valid |
      | Postconditions               | Checking output data / thrown exception / endless execution |
      | Conditional postconditions   | Checking postconditions when depends on input data info (`== null` or etc.) |
      | Class invariants             | Must be written near the class declaration, but checked after (and maybe before) each method call. |
      
      It should be possible for the library designer (or even the library user) to add their own contract types
  * Compile-time/Runtime/Both
      * *At least compile time, because we want to give an IDE a hint to help the user (as it is said in the description of the internship task)*
      * I suggest both, but escape runtime-check, if it is already checked compile-time
      * Also it should be better to let users to switch *[runtime]* contracts on and off, because of their possible overhead (especially, for class invariant checking after each method call)
      * Compile-time contracts should be able to be used to check correctness of the program.
  * What to do if it fails (return null, throw, crash)
      * It is better to throw some General exeption, because failing assertion is not just an expetive situation - it is a bug. Nevertheless, it should be possible to specify which exception to throw or what else to do on the failure. By default it should be some `ContractAssertionException : RuntimeException`
  * Place:
      * Inside the block
      * **Outside of it** *<- I choose this one because reader does not want to look inside the implementation to see something not connected with it such as contracts*
  * Think about possible syntax for the idea in consideration of already implemented contracts (listed at https://ru.wikipedia.org/wiki/Контрактное_программирование)
      * Special constructions for each type of contract -> so it is difficult to combine them
      * Contracts are written in the string (like in examples bellow) -> no highlighting, but wider syntax
      * Suggested (by me) syntax
          1. There are special constructions for a contract in general, but infix functions are used to make it readable
          2. So it is more like second way, but with code instead of plain text.
          3. `Contract` is a vararg function that takes some contracts and applies them to the block in some way (depends on interface)  
          4. 3 is just one of possible implementations of what is given in 1. The main idea to consider is in 1.
    * A very good article is given in `Useful links` paragraph
  * Add documentation generation based on the contracts
    * It would be great if `KDoc` could add contracts to the documentation (at least if it is marked as documentable, for example) because this would help user to guess behavior of methods
2. Problems
  * Code duplication: contracts may repeat the body of the method (this can be a typical problem for complicated contracts).
    * Example (Java):
    ```java
    import org.jetbrains.annotations.Contract;
    
    public class Trash {
        @Contract(value = "true -> fail", pure = true)
        static void Crash(boolean cond) {
            if (cond) {
                throw new IllegalArgumentException("Cond must be false.");
            }
        }
    }
    ```
    * Possible solutions
      1. Contracts should provide access to auto-generate contracts from the code and show them in the IDE by default.
      ```java
      import org.jetbrains.annotations.Contract;
      
      public class Trash {
          @Contract(value = "true -> fail", pure = true) // <- this should only be a highlighting in IDE
          static void Crash(boolean cond) {
              if (cond) {
                  throw new IllegalArgumentException("Cond must be false.");
              }
          }
      }
      ```
      2. *With 2 examples*
        * The opposite solution is to generate code by the given contract. Something like:
          ```java
          import org.jetbrains.annotations.Contract;
          
          public class Trash {
              @Contract(value = "cond -> throw<IllegalArgumentException>(\"Cond must be false\")", pure = true)
              static void Crash(boolean cond) {
              }
          }
          ```
        * Another example of using the second idea would be
          ```java
          import org.jetbrains.annotations.Contract;
    
          @Contract(invariant = "Trash::i > 0")
          public class Trash {
              int i = 2;
              Trash(int i) {
                  this.i = i; //here would be the contract assertion
              }
         
              void Crash(boolean cond) {
                  i += 1; //here would be the contract assertion too
              }
          }
          ```
      
3. Reasons of lack of usage of contracts in other languages
    | Reason                          | Solution      |
    |---------------------------------|---------------|
    | Their compilation is slow       | It should be able to choose level of checking (like `-O0`..`-O3`) |
    | Their execution overhead is big | Not really big, but solution is the same with one in previous line |
    | False positieves                | Customizable checks + supressings (as for usual warnings) |
    | No proper tools                 | Some big company such as `JetBrains` should create them |
3. Useful links
  * https://ru.stackoverflow.com/questions/458342/Контрактное-программирование-code-contracts
  * http://sergeyteplyakov.blogspot.com/2015/08/should-i-use-code-contracts.html
