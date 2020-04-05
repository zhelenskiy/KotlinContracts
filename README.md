# KotlinContracts
### Intro
This is a template of how contracts should be and why. I do not say some general information about what contracts are because it is easy to be found on the net.
### Plan
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
      * *At least compile-time, because we want to give an IDE a hint to help the user (as it is said in the description of the internship task)*
      * I suggest both, but escape runtime-check, if it is already checked compile-time
      * Also it should be better to let users switch *[runtime]* contracts on and off, because of their possible overhead (especially, for class invariant checking after each method call)
      * Compile-time contracts should be able to be used to check the correctness of the program.
  * What to do if it fails (return null, throw, crash)
      * It is better to throw some general exception because the failing assertion is not just an exceptive situation - it is a bug. Nevertheless, it should be possible to specify which exception to throw or what else to do on the failure. By default it should be some `ContractAssertionException : RuntimeException`
  * Place:
      * Inside the block
      * **Outside of it** *<- It is better to choose this one because the reader does not want to look inside the implementation to see something not connected with it such as contracts*
  * Think about a possible syntax for the idea in consideration of already implemented contracts (listed at https://ru.wikipedia.org/wiki/Контрактное_программирование)
      * Special constructions for each type of contract -> so it is difficult to combine them
      * Contracts are written in the string (like in examples bellow) -> no highlighting, but the wider syntax is provided
      * Suggested (by me) syntax
          1. Contracts are written with DSL. Each contract can be written with its own lambda with infix functions inside to unite constructions inside to make it more readable.
          2. So it is more like the second way, but with code instead of plain text.
          3. You are able to write even complex `Kotlin` expressions.
          4. Possible implementation usage:
              * ```kotlin
                Contract { null follows null, other follows newValue }
                ```
              * ```kotlin
                Contract { x follows { if (it == null) then null else newValue } }
                ```
              * ```kotlin
                Contract { x follows when (x) {
                        is RandomAccess -> null
                        is Integer -> newValue
                        else -> fail
                    }
                }
                ```
    * A very good article is given in `Useful links` paragraph
  * Add documentation generation based on the contracts
    * It would be great if `KDoc` could add contracts to the documentation (at least if it is marked as documentable, for example) because this would help the user to guess the behavior of methods
2. Problems
  * Code duplication: contracts may repeat the body of the method (this can be a typical problem for complicated contracts).
    * Example (`Java`):
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
      2. *With 3 examples*
        * The opposite solution is to generate code by the given contract *(May be, only if annotated with something like `@Generative`)*. Something like:
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
        * We can modify `Kotlin` example given above *(before `problems` section)* to allow it to auto-generate code
          * ```kotlin
            Contract {
                x follows when (x) {
                    is RandomAccess -> null
                    is Integer -> new Integer()
                    else -> fail<IllegalArgumentException>()
                }
            }
            ```
        
      
3. Reasons for lack of usage of contracts in other languages
    | Reason                          | Solution      |
    |---------------------------------|---------------|
    | Their compilation is slow       | It should be able to choose the level of checking (like `-O0`..`-O3`) |
    | Their execution overhead is big | Not really big, but the solution is the same with one in the previous line |
    | False positives <ul><li>This can happen in complicated algorithm when it is impossible to prove correctness without knowledge of what algorithm it is</li> <li>For example:<ul><li>It is impossible to check that loop in binary search is finite</li> <li>The stored graph is a tree, so bfs without checking that vertex is already used will end</li></ul></li><ul>                | Customizable checks + supressings (as for usual warnings) |
    | No proper tools                 | Some big company such as `JetBrains` should create them |
3. Useful links
  * https://ru.stackoverflow.com/questions/458342/Контрактное-программирование-code-contracts
  * http://sergeyteplyakov.blogspot.com/2015/08/should-i-use-code-contracts.html
