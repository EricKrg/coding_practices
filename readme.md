# Coding Practices
- Based on The Power of Ten, DRY and SOLID  

Since the applications developed in our group are not as mission critical as those of the NASA JPL and most of our Projects are not written in C/C++ this is a heavily adapted and edited list of **the Power of Ten** with additions we found useful in solving recurring code quality problems.  
This list is aimed to give a baseline for code quality agreements and to make code reviews easier. 

1. **Avoid complex flow constructs, such as goto (and recursion)**
    1. write small Functions see 3.  
    2. DRY (dont repeat yourself)  
    3. Remove dead code 
        > *pieces of code that are never executed at run-time or that are executed but whose result is never used and does not affect the rest of the codebase*
    4. Reduce the number of decision structures 
        > *see Decortator Pattern or Strategy Pattern*  
    5. Keep an eye on the **cyclomatic complexity**
        > For most routines, a cyclomatic complexity below 4 is considered good; a cyclomatic complexity between 5 and 7 is considered medium complexity, between 8 and 10 is high complexity, and above that is extreme complexity.  

        see [Example](https://brandonsavage.net/code-complexity-and-clean-code/#:~:text=For%20most%20routines%2C%20a%20cyclomatic,above%20that%20is%20extreme%20complexity.), read [more](https://www.ibm.com/docs/en/raa/6.1?topic=metrics-cyclomatic-complexity)

    **On Recursions**  
    Putting depth bounds on recursive procedures makes them every bit as safe as loops with fixed bounds.   
    If recursion is used obey these three important laws:

    > 1. A recursive algorithm must call itself, recursively. 
    > 2. A recursive algorithm must have a base case.
    > 3. A recursive algorithm must change its state and move toward the base case.
<hr>

2. **All loops must have fixed bounds (this prevents runaway code)**

    You have to try to make the bounds reasonably tight, and you have to regard hitting an artificial bound as a run-time error. *Whats the difference between a loop which runs 300.000.000 Years and 'will never stop'?*

    1. Use list **iterators** when possible 

    Example:

    ```kotlin
    val numbers = listOf("one", "two", "three", "four")
    for (item in numbers) {
        println(item)
    }
    // or 
    val numbersIterator = numbers.iterator()
    while (numbersIterator.hasNext()) {
        println(numbersIterator.next())
    }

    // IN FAVOR OF
    while (i <= numbers.size) {
        println(numbers[i])
        i++
    } // causes IndexOutofBoundEx
    ```
<hr>

3. **Restrict functions to a single printed page**

    Roughly about 60 LoC.  
    Reducing the length of functions to a single page makes it easier to grasp all the functionalities of a specific routine of your program.

    1. remember DRY
    2. keep an Eye on cyclomatic complexity

<hr>

4. **The assertion density of the code should average to a minimum of two assertions per function.**

    > Assertions are used to check for anomalous conditions that should never happen in real-life executions. Assertions must always be side-effect free and should be defined as Boolean tests. When an assertion fails, an explicit recovery action must be taken, e.g., by returning an error condition to the caller of the function that executes the failing assertion. 

    ```kotlin
    fun divide(a: Int, b: Int): Int {
        // Assertion 1: Ensure 'b' is not zero
        require(b != 0) { "Divisor 'b' must be non-zero." }

        val result = a / b

        // Assertion 2: Ensure the division result is positive
        assert(result >= 0) { "Division result should be non-negative." }

        return result
    }
    ```

<hr>

5. **Data objects must be declared at the smallest possible level of scope.**  

    - limit visibility and lifespan of variables to only the necessary parts
    - improves code organization, reduces potential naming conflicts  
    - makes it easier to reason about behavior and state of data objects  

    > Explicitly declaring scalar variables in the smallest scope possible simplifies data flow analysis, which may be difficult, time-consuming and error-prone especially in big code bases that contain many function calls spread along multiple files. It makes it easier for the compiler to track the usage of variables. By minimizing the number of statements where the value can be modified, it is easier to diagnose why a variable is taking an erroneous value. Additionally, it reduces the likelihood of reusing variables for multiple, incompatible purposes, making code testing significantly easier.   
    
    [see](https://www.codee.com/knowledge/pwr002/   )

    ```kotlin
    fun processData(input: String): Unit {
        val data: List<Int> = parseData(input)
        
        // Perform operations on the data object
        // ...

        // Declare a nested data object with limited scope
        if (data.isNotEmpty()) {
            val subData: List<Int> = data.subList(1, data.size)
            
            // Perform operations on the subData object
            // ...
        }

        // More code...
    }

    fun parseData(input: String): List<Int> {
        // Parse the input and return the data object
        // ...
    }
    ```
<hr>

6. **The return value of non-void function should be used.**
  
    It is possible to call a non-void function without using its return value afterward. There could be an error behind such behavior.

    Values returned by non-void functions must always be used.

    ```kotlin

    fun foo(x: Int): Int = x + x

    // BAD
    fun bar(int x): Unit {
     foo(x); // return value not used
    }

    // BETTER
    fun bar(int x): Unit {
     foo(x) as Unit 
     // explicit cast to Void, so it is clear that this was intentionally
    }
    ```





<hr>

7. **All code must be compiled, from the first day of development, with all compiler warnings enabled at the compiler’s most pedantic setting.**  
    Use static source code analyzer, it should pass the analyses with zero warnings.

    1. Fail loudly
    2. Fail fast
    3. Use a pedantic Linter (language specific)
<hr>

8. **Tests Tests Tests** 

<hr>

- Use a language specific linter
- Write code for humans
- provide a minimum of Documentation



[The Power of Ten](https://spinroot.com/gerard/pdf/P10.pdf)  
[Comments on the Power of Ten](http://www.cs.otago.ac.nz/cosc345/resources/nasa-10-rules.htm#:~:text=All%20loops%20must%20have%20a,the%20rule%20is%20considered%20violated.)

