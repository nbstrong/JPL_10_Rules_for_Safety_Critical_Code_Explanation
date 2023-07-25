This is an explanation with code exambles for NASA Jet Propulsion Lab's 10 Rules for safety critical code.

Found here: https://en.wikipedia.org/wiki/The_Power_of_10:_Rules_for_Developing_Safety-Critical_Code

------------------

1. Avoid complex flow constructs, such as goto and recursion.

------------------

**Rule Explanation:**
This rule advises against the use of complex flow constructs such as "goto" statements and recursion. The "goto" statement can lead to code that's difficult to trace and understand, making it more prone to errors. Recursion, on the other hand, can lead to stack overflow issues if not used carefully, which can crash the program.

**Without Rule Applied:**
```c
// using goto statement
void someFunction(int num) {
    start:
        if(num > 0) {
            std::cout << num << std::endl;
            num--;
            goto start;
        }
}

// using recursion
int factorial(int n) {
    if(n <= 0)
        return 1;
    else
        return n * factorial(n - 1);
}

```
**With Rule Applied:**
```c
// replacing goto with a loop
void someFunction(int num) {
    while(num > 0) {
        std::cout << num << std::endl;
        num--;
    }
}

// replacing recursion with a loop
int factorial(int n) {
    int result = 1;
    for(int i = 1; i <= n; i++) {
        result *= i;
    }
    return result;
}

```
In the updated examples, control flow is easier to follow, and the risk of stack overflow is significantly reduced.

------------------

2. All loops must have fixed bounds. This prevents runaway code.

------------------

**Rule Explanation:**
This rule suggests that all loops should have a predefined limit to the number of iterations they can perform. This is critical in safety-related systems to prevent "runaway" code, where a loop could continue indefinitely and either freeze the system or consume resources excessively, potentially leading to system instability or failure.

**Without Rule Applied:**
```cpp
// loop with an unpredictable end
int i = 0;
while(i != 10) {
    // if due to some bug or condition, 'i' never reaches 10, this becomes an infinite loop
    i += 2;
    if(i > 20) i = 0;
}

```
**With Rule Applied:**
```cpp
// loop with a fixed bound
for(int i = 0; i < 10; ++i) {
    // the loop will execute exactly 10 times
}

```
In the updated example, the loop has a fixed upper bound (10 iterations), which prevents it from potentially becoming a runaway code.

------------------

3. Avoid heap memory allocation.

------------------

**Rule Explanation:**
This rule advises against using heap memory allocation. Memory allocation on the heap can lead to problems such as memory leaks (if not deallocated properly) or out-of-memory errors (if too much memory is allocated). Also, dynamic memory allocation can result in fragmented memory, which can degrade performance. These issues can be critical in a safety-related system.

**Without Rule Applied:**
```cpp
int* arr = new int[10];  // Allocating memory on the heap
//... use arr
delete[] arr;  // If this line is missed or not reached due to an early return, a memory leak occurs

```
**With Rule Applied:**
```cpp
int arr[10];  // Allocating memory on the stack
//... use arr
// No need for explicit deallocation. Memory gets automatically freed when arr goes out of scope

```
In the updated example, the array is allocated on the stack, which doesn't require explicit memory deallocation and is less prone to errors. Note that stack memory is limited, and large arrays may still need to be allocated on the heap, albeit with diligent management to avoid the aforementioned issues.

------------------

4. Restrict functions to a single printed page.

------------------

**Rule Explanation:**
This rule encourages limiting the length of a function to what can be viewed on a single printed page (or a single screen page), traditionally about 60 lines. Long functions tend to be more complex, harder to understand, test, and debug. Keeping functions short and focused on one task improves readability and maintainability of the code.

**Without Rule Applied:**
```cpp
void complexFunction() {
    // Block of code 1
    // ...
    // Block of code 2
    // ...
    // Block of code 3
    // ...
    // Block of code n
    // This function spans multiple pages/screens
}

```
**With Rule Applied:**
```cpp
void task1() {
    // Block of code 1
}

void task2() {
    // Block of code 2
}

void task3() {
    // Block of code 3
}

void simpleFunction() {
    task1();
    task2();
    task3();
    // This function fits in a single printed/screen page
}

```
In the updated example, the complex function has been broken down into smaller, more manageable functions, each performing a specific task. This enhances the readability and maintainability of the code.

------------------

5. Use a minimum of two runtime assertions per function.

------------------

**Rule Explanation:**
This rule suggests using at least two runtime assertions per function. Assertions are a defensive programming technique used to catch programming or logical errors. When the condition in the assertion statement evaluates to false, the program will typically halt execution and generate an error message. This aids in detecting and diagnosing bugs during development and testing.

**Without Rule Applied:**
```cpp
int divide(int numerator, int denominator) {
    return numerator / denominator;  // If denominator is zero, a divide by zero error will occur at runtime
}

```
**With Rule Applied:**
```cpp
#include <cassert>

int divide(int numerator, int denominator) {
    assert(denominator != 0 && "Denominator cannot be zero!");  // Runtime assertion
    int result = numerator / denominator;
    assert(result * denominator == numerator && "Division result incorrect!");  // Another runtime assertion
    return result;
}

```
In the updated example, we have two assertions to ensure that the denominator isn't zero (which would cause a divide-by-zero error) and to verify the correctness of the division operation. If any of these assertions fail, the program will halt, allowing developers to identify and correct the issues.

------------------

6. Restrict the scope of data to the smallest possible.

------------------

**Rule Explanation:**
This rule suggests that the scope of data (variables, constants, objects) should be restricted as much as possible. This helps in maintaining encapsulation, preventing accidental modification from other parts of the code, and reducing potential bugs and complexity.

**Without Rule Applied:**
```cpp
int globalVar = 10;  // Global variable

void function1() {
    globalVar += 5;  // Modifies global variable
}

void function2() {
    globalVar -= 2;  // Modifies global variable
}

```
**With Rule Applied:**
```cpp
void function1() {
    int localVar1 = 10;  // Local variable
    localVar1 += 5;  // Modifies local variable
}

void function2() {
    int localVar2 = 10;  // Local variable
    localVar2 -= 2;  // Modifies local variable
}

```
In the updated example, `globalVar` has been replaced with `localVar1` and `localVar2`, which are local to their respective functions. This prevents unintended side-effects from function calls and makes the code easier to understand and debug.

------------------

7. Check the return value of all non-void functions, or cast to void to indicate the return value is useless.

------------------

**Rule Explanation:**
This rule suggests that the return value of all non-void functions should be checked, or if it is useless, it should be cast to void. This helps prevent unnoticed errors when a function fails, and also makes the programmer's intentions clear when a return value is intentionally disregarded.

**Without Rule Applied:**
```cpp
double divide(int a, int b) {
    if(b == 0) return -1;  // Returns -1 when error occurs
    return static_cast<double>(a) / static_cast<double>(b);
}

void someFunction() {
    divide(10, 0);  // Return value not checked
}

```
**With Rule Applied:**
```cpp
double divide(int a, int b) {
    if(b == 0) return -1;  // Returns -1 when error occurs
    return static_cast<double>(a) / static_cast<double>(b);
}

void someFunction() {
    double result = divide(10, 0);
    if(result == -1) {
        std::cerr << "Error: Division by zero.\n";
    }
    // OR
    (void)divide(10, 0);  // Explicitly cast to void to indicate that we don't care about the result
}

```
In the updated example, the return value of the `divide` function is either checked for errors or explicitly cast to void, making the programmer's intentions clear and helping prevent unnoticed errors.

------------------

8. Use the preprocessor sparingly.

------------------

**Rule Explanation:**
This rule recommends using the preprocessor sparingly. The C++ preprocessor is a powerful tool that allows for macro substitution, conditional compilation, and inclusion of header files. However, misuse of the preprocessor can lead to code that is hard to understand and debug. Issues can arise due to unexpected macro expansion, name collisions, and difficulty in tracing the code.

**Without Rule Applied:**
```cpp
#define SQUARE(x) x * x

void someFunction() {
    int a = 5;
    int b = SQUARE(a + 1);  // This expands to a + 1 * a + 1 (which is 11, not 36 as expected due to operator precedence)
}

```
**With Rule Applied:**
```cpp
inline int square(int x) {
    return x * x;
}

void someFunction() {
    int a = 5;
    int b = square(a + 1);  // This correctly computes the square of (a + 1)
}

```
In the updated example, a function `square()` is used instead of a preprocessor macro. This makes the code easier to read, debug, and less prone to errors due to unexpected macro expansion. It also provides type safety which macros lack.

------------------

9. Limit pointer use to a single dereference, and do not use function pointers.

------------------

**Rule Explanation:**
This rule suggests limiting pointer use to a single dereference and avoiding function pointers. Multiple dereferences can make the code complex and harder to read, increasing the likelihood of errors. Function pointers add another layer of indirection and can make the code more difficult to follow, increasing the risk of programming mistakes.

**Without Rule Applied:**
```cpp
int a = 5;
int* p = &a;
int** pp = &p;

**pp = 10;  // Multiple dereferences

void someFunction() {  // Function pointer
    void (*fp)() = &someOtherFunction;
    (*fp)();
}

```
**With Rule Applied:**
```cpp
int a = 5;
int* p = &a;

*p = 10;  // Single dereference

void someFunction() {
    someOtherFunction();  // Direct function call
}

```
In the updated examples, only a single dereference is performed when using a pointer, and a direct function call is used instead of a function pointer, making the code clearer and easier to understand.

------------------

10. Compile with all possible warnings active; all warnings should then be addressed before release of the software.

------------------

**Rule Explanation:**
This rule suggests compiling code with all possible warnings enabled. Compiler warnings can often highlight potential issues in the code that might not necessarily prevent the code from compiling, but could lead to logical errors or undefined behavior during execution. Resolving these warnings before releasing the software will help ensure the software is robust and reliable.
Most compilers allow you to specify the warning level. For instance, in GCC and Clang, `-Wall` enables all the standard warnings, and `-Wextra` enables some additional warnings. You can also use `-Werror` to treat all warnings as errors, which forces you to fix them since the code won't compile otherwise.

**Without Rule Applied:**

Compiling code with default settings might not reveal all potential issues. For example, the code might compile successfully but still contain logical errors:
```cpp
int main() {
    int a;
    int b = a + 5;  // Using uninitialized variable 'a'
    return 0;
}

```
**With Rule Applied:**

If you compile with all warnings enabled (for example, using `g++ -Wall -Wextra` in GCC), the compiler will warn about the uninitialized variable:
```cpp
int main() {
    int a = 0;  // Variable 'a' is now initialized
    int b = a + 5;
    return 0;
}

```
In the updated example, the warning about the uninitialized variable 'a' is addressed by initializing 'a' before use. This prevents potential undefined behavior from occurring when the program runs.

------------------

