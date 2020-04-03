# KotlinContracts
**Plan**
1. Purposes
  * Consider what functionality we want to have
  * Compile time/Runtime/Both
      * *At least compile time, because we want to give an IDE a hint to help the user (as it is said in the description of the internship task)*
  * What to do if it fails (return null, throw, crash)
  * Think about possible syntax for the idea in consideration of already implemented contracts (listed at https://ru.wikipedia.org/wiki/Контрактное_программирование)
    * A very good article is given in `Useful links` paragraph
  * Add documentation generation based on the contracts
2. Problems
  * Code duplication: contracts may repeat the body of the method (this can be a typical problem for complicated contracts).
    * Possible solutions
      1. Contracts should provide access to auto-generate contracts from the code and show them in the IDE by default.
      2. The opposite solution is to generate code by the given contract.
3. Reasons of lack of usage of contracts in other languages
  * 
3. Useful links
  * https://ru.stackoverflow.com/questions/458342/Контрактное-программирование-code-contracts
