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
  * Compile time/Runtime/Both
      * *At least compile time, because we want to give an IDE a hint to help the user (as it is said in the description of the internship task)*
      * I suggest both, but escape runtime-check, if it is already checked compile-time
      * Also it should be better to let users to switch *[runtime]* contracts on and off, because of their possible overhead (especially, for class invariant checking after each method call)
  * What to do if it fails (return null, throw, crash)
      * It is better to throw some General exeption, because failing assertion is not just an expetive situation - it is a bug. Nevertheless, it should be possible to specify which exception to throw or what else to do on the failure. By default it should be some `ContractAssertionException : RuntimeException`
  * Place:
      * Inside the block
      * **Outside of it** *<- I choose this one because reader does not want to look inside the implementation to see something not connected with it such as contracts*
  * Think about possible syntax for the idea in consideration of already implemented contracts (listed at https://ru.wikipedia.org/wiki/Контрактное_программирование)
      * Suggested (by me) syntax
          * 
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
      2. The opposite solution is to generate code by the given contract. Something like:
      ```java
      import org.jetbrains.annotations.Contract;
      
      public class Trash {
          @Contract(value = "cond -> throw<IllegalArgumentException>(\"Cond must be false\")", pure = true)
          static void Crash(boolean cond) {
          }
      }
      ```
    * Another example
      
3. Reasons of lack of usage of contracts in other languages
  * 
3. Useful links
  * https://ru.stackoverflow.com/questions/458342/Контрактное-программирование-code-contracts
