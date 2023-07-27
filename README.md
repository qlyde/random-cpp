# learn-cpp

## Variable assignment and initialization

```cpp
int a;         // default initialization
int b = 5;     // copy initialization
int c( 6 );    // direct initialization

// List initialization methods (C++11) (preferred)
int d { 7 };   // direct list initialization
int e = { 8 }; // copy list initialization
int f {};      // value initialization
```

List initialization is also called **uniform** or **brace** initialization.

List initialization disallows **narrowing conversions**:

```cpp
int width { 4.5 }; // error
```

Since C++17:

```cpp
[[maybe_unused]] int x { 5 }; // attribute: avoid compiler warnings
```

## Introduction to iostream: cout, cin, and endl

* **Insertion operator:** `<<`
* **Extraction operator:** `>>`

`std::cout` is buffered and flushed periodically. `std::endl` flushes the buffer and can be inefficient.

## Introduction to literals and operators

The number of operands that an operator takes as input is called the operator's **arity.**

For example, **binary**, **ternary** and **nullary**.

**Side effect** operators: `operator<<`, `operator=`, etc.

## Introduction to functions

Nested functions are not supported in C++.

## Naming collisions and an introduction to namespaces

The `::` symbol is an operator called the **scope resolution operator**. When an identifier includes a namespace prefix, the identifier is called a **qualified name**.

**Using directive** statement (should be avoided):

```cpp
using namespace std;
```

## Introduction to the preprocessor

**Preprocessor directives** start with a `#` symbol and don't end with a semicolon.

`#include` is a directive that the preprocessor replaces with the contents of the included file.

When the preprocessor has finished processing a code file, the result is called a **translation unit**, which is what is compiled.

`#define` is a directive that is used to define **macros**: *object-like macros* and *function-like macros*.

**Conditional compilation** directives: `#if`, `#ifdef`, `#ifndef` and `#if`. Alternatively, `#if defined(...)` or `#if !defined(...)`.

## Header files

Avoid #including .cpp files.

When we include with `<>`, we're telling the preprocessor that this is a header file we didn't write ourselves. The preprocessor will search for the header only in the directories specified by the *include directories*, and won't search the project's source code directory.

When we include with `""`, the preprocessor will first search for the header file in the current directory, then it will search the *include directories*.

## Header guards

Also called **include guards**:

```cpp
#ifndef SQUARE_H
#define SQUARE_H

// ...

#endif
```

Modern compilers support the `#pragma` preprocessor directive (not fully recommended, because it is not an official part of the C++ language):

```cpp
#pragma once

// ...
```

## Object sizes and the sizeof operator

The C++ standard does not define the exact size of any of the fundamental types. Instead, it only defines a minimum size for the integral and char types, and leaves the actual size of all types to be implementation-defined.

The `sizeof` operator is a unary operator that takes either a type or a variable and returns its size in bytes. You cannot use this operator on the `void` type (since it has no size).

```cpp
sizeof(long)
```

## Fixed-width integers and size_t

C99 defined a set of **fixed-width integers** in `stdint.h` that are guaranteed to be the same size on any architecture. C++ adopted these as part of C++ in `<cstdint>`.

```cpp
#include <cstdint>

std::int8_t
std::uint8_t
std::int16_t
std::uint16_t
std::int32_t
std::uint32_t
std::int64_t
std::uint64_t
```

However, fixed-width integers are not guaranteed to be defined on all architectures, and may be slower than a wider type on some architectures.

So, C++ defines **fast and least integers**.

The **fast** types (`std::int_fast#_t` and `std::uint_fast#_t`) provide the fastest signed/unsigned integer with a width of at least `#` bits (8, 16, 32 or 64).

The **least** types (`std::int_least#_t` and `std::uint_least#_t`) provide the smallest signed/unsigned integer with a width of at least `#` bits (8, 16, 32 or 64).

The `sizeof` operator returns a value of type `std::size_t`. It's defined as an unsigned integral type and is used to represent the size/length of objects.

```cpp
#include <cstddef> // for std::size_t

sizeof(std::size_t)
```

## Floating point numbers

`std::cout` has a default precision of 6. We can override this using an *output manipulator* function `std::setprecision`, defined in the `<iomanip>` header.

## Boolean values

`std::cout` prints `0` for `false` and `1` for `true`. To print "true" or "false" instead:

```cpp
std::cout << std::boolalpha;
std::cout << std::noboolalpha;
```

To allow `std::cin` to accept "false" and "true" as inputs:

```cpp
std::cin >> std::boolalpha;
```

## Introduction to type conversion and static_cast

```cpp
static_cast<int>(5.5);
```

## Const variables and symbolic constants

Use const before the type: `const double` over `double const`.

Const variables must be initialized.

Don't use const when passing by value, since the compiler creates a copy anyway which is okay to modify.

Don't use const when returning by value.

## Compile-time constant, constant expressions, and constexpr

A **constant expression** is an expression that can be evaluated by the compiler at compile-time. All the values in the expression must be known at compile-time, and all of the operators and functions called must support compile-time evaluation.

When using `const`, our variables could end up as either a **compile-time const** or a **runtime const**, depending on whether the initializer is a compile-time expression.

```cpp
constexpr double gravity { 9.8 };
```

Any variable that should not be modifiable after initialization and whose initializer is known at compile-time should be declared as `constexpr`.

Any variable that should not be modifiable after initialization and whose initializer is **not** known at compile-time should be declared as `const`.

Some types may not be compatible with `constexpr` due to dynamic memory allocation, such as `std::string` and `std::vector`.

**Constant folding** is a compiler optimization:

```cpp
constexpr inst x { 3 + 4 };
std::cout << x << '\n';

// compiled to
std::cout << 3 + 4 << '\n';

// or
std::cout << 7 << '\n';
```

## Numeral systems (decimal, binary, hexadecimal, and octal)

```cpp
int x{ 012 }; // 0 prefix means octal
int bin{ 0x0001 }; // 0x prefix means hexadecimal
int bin{ 0b1 }; // 0b prefix means binary
```

**Digit separator** `'`:

```cpp
int bin{ 0b1011'0010 };
long value{ 2'132'673'462 }; // easier to read
```

You can change the output format via:

```cpp
std::cout << std::hex;
std::cout << std::oct;
std::cout << std::dec;
```

To output binary:

```cpp
#include <bitset>

std::cout << std::bitset<4>{ 0b1010 } << '\n';
```

## Introduction to std::string

When using `operator>>` to extract a string from `std::cin`, only characters up to the first whitespace is returned. Remaining characters are left inside the `std::cin` buffer, waiting for the next extraction.

To read a full line of input text, use `std::getline()`:

```cpp
std::string name{};
std::getline(std::cin >> std::ws, name);
```

`std::ws` is an **input manipulator** that tells `std::cin` to ignore any leading whitespace before extraction.

Use `name.length()` to get the length of a string `name`, which returns a `size_t`.

In C++20, you can use `std::ssize()` to get the length of a `std::string` as a signed integral value.

We can create string literals with type `std::string` by using an `s` suffix:

```cpp
using namespace std::string_literals;
std::cout << "goo\n"s; // std::string literal
```

## Introduction to std::string_view

C++17 introduced `std::string_view` in `<string_view>` header. It provides read-only access to an *existing* string (a C-style string, a `std::string` or another `std::string_view`) without making a copy.

```cpp
#include <iostream>
#include <string_view>

// str provides read-only access to whatever argument is passed in
void printSV(std::string_view str) // now a std::string_view
{
    std::cout << str << '\n';
}

int main()
{
    std::string_view s{ "Hello, world!" }; // now a std::string_view
    printSV(s);

    return 0;
}
```

We can create string literals with type `std::string_view` by using a `sv` suffix:

```cpp
using namespace std::string_literals;
using namespace std::string_view_literals;

std::cout << "foo\n";   // no suffix is a C-style string literal
std::cout << "goo\n"s;  // s suffix is a std::string literal
std::cout << "moo\n"sv; // sv suffix is a std::string_view literal
```

Unlike `std::string`, `std::string_view` has full support for constexpr, making it preferred when string symbolic constants are needed.

```cpp
constexpr std::string_view s{ "Hello, world!" };
```

`std::string` makes its own copy of the initializer value, which is expensive. It is an owner, not a viewer.

A **dangling view** is a view in which the object being viewed is destroyed while the view is still being used.

Prefer `std::string_view` over `const std::string&` in most cases as a read-only function parameter.

When a `std::string` is modified, all views into that string are *invalidated*.

Be careful returning a `std::string_view` to a local variable, since these are destroyed at the end of a function, invalidating the view.

```cpp
// View modification functions
#include <string_view>

std::string_view str{ "Peach" };

// Remove 1 character from the left side of the view
str.remove_prefix(1);

// Remove 2 characters from the right side of the view
str.remove_suffix(2);

// Reset the view
str = "Peach";
```

`std::string_view` can be useful when viewing **substrings**.

## Remainder and Exponentiation

`x % y` always returns results with the sign of `x`.

```cpp
bool isOdd(int x) {
    return (x % 2 == 1); // FAILS when x is -5
}
```

To do exponents in C++:

```cpp
#include <cmath>

double x{ std::pow(3.0, 4.0) }; // 3^4
```

## Comma and conditional operators

The **comma operator** evaluates the left operand, then the right operand, then returns the result of the right operand.

```cpp
int x{ 1 };
int y{ 2 };

std::cout << (++x, ++y) << '\n'; // prints 3
```

Avoid the comma operator, except in for loops.

## Bit flags and bit manipulation via std::bitset

- `test()`
- `set()`
- `reset()`
- `flip()`

Each take the position of the bit (right-to-left).

## User-defined namespaces and the scope resolution operator

```cpp
namespace Foo // start with a capital letter
{
    // ...
}
```

`::foo` will look for `foo` in the **global namespace**.

Namespaces can be nested. And since C++17, they can be declared in this way:

```cpp
namespace Foo::Goo
{
    // ...
}
```

You can create **namespace aliases**:

```cpp
namespace Active = Foo::Goo; // to avoid pain of Foo::Goo
```

## Introduction to global variables

Global variables are created when the program starts, and destroyed when it ends. This is called **static duration**. Variables with static duration are called **static variables**.

Consider using a `g_` prefix for global variables.

Variables with static duration are zero-initialized by default.

## Internal linkage

An identifier with **internal linkage** can be seen and used within a single translation unit.

Global variables with internal linkage are sometimes called **internal variables**.

To make a non-constant global variable internal, we use the `static` keyword.

```cpp
static int g_x{}; // internal linkage
const int g_y{ 1 }; // const globals have internal linkage by default
constexpr int g_z{ 2 }; // constexpr globals have internal linkage by default
```

Functions default to external linkage, but can be set to internal using `static`.

```cpp
static int add(int x, int y) // can only be used within this file
{
    return x + y;
}
```

In modern C++, it's common to use **unnamed namespaces** to achieve internal linkage to a range of identifiers.

## External linkage and variable forward declarations

An identifier with **external linkage** can be seen and used both from the file in which it is defined, and from other code files (via a forward declaration).

Global variables with external linkage are sometimes called **external variables**.

For variables, creating a forward declaration is also done via the `extern` keyword.

```cpp
// a.cpp
int g_x { 2 }; // non-const globals have external linkage by default
extern const int g_y { 3 }; // g_y has external linkage

// main.cpp
extern int g_x;
extern const int g_y;
```

`constexpr` variables cannot be forward declared.

Functions do not need the `extern` keyword.

## Sharing global constants across multiple files (using inline variables)

`inline` has evolved to mean "multiple definitions are allowed". Inline global variables have external linkage by default.

```cpp
// constants.h
#ifndef CONSTANTS_H
#define CONSTANTS_H

// define your own namespace to hold constants
namespace constants
{
    inline constexpr double pi { 3.14159 }; // note: now inline constexpr
    inline constexpr double avogadro { 6.0221413e23 };
    inline constexpr double myGravity { 9.2 }; // m/s^2 -- gravity is light on this planet
    // ... other related constants
}
#endif

// main.cpp
#include "constants.h"

#include <iostream>

int main()
{
    std::cout << "Enter a radius: ";
    double radius{};
    std::cin >> radius;

    std::cout << "The circumference is: " << 2 * radius * constants::pi << '\n';

    return 0;
}
```

## Static local variables

Using `static` on a local variable changes its duration from **automatic duration** to **static duration**.

```cpp
#include <iostream>

void incrementAndPrint()
{
    static int s_value{ 1 }; // static duration via static keyword.  This initializer is only executed once.
    ++s_value;
    std::cout << s_value << '\n';
} // s_value is not destroyed here, but becomes inaccessible because it goes out of scope

int main()
{
    incrementAndPrint();
    incrementAndPrint();
    incrementAndPrint();

    return 0;
}
```

Common to use `s_` prefix for static local variables.

Useful for unique ID generators.

## Scope, duration, and linkage summary

- **Automatic duration:** Point of definition -> exit of block
- **Static duration:** Start of program -> end of program
- **Dynamic duration:** Programmer's discretion

An identifier’s linkage determines whether multiple declarations of an identifier refer to the same entity (object, function, reference, etc…) or not.
- **No linkage:** Identifier only refers to itself:
    - Local variables
    - Program-defined type definitions (enums, classes) declared inside a block
- **Internal linkage:** Can be accessed anywhere within the file it is declared:
    - Static global variables (initialized or uninitialized)
    - Static functions
    - Const global variables
    - Functions declared inside an unnamed namespace
    - Program-defined type definitions (enums, classes) declared inside an unnamed namespace
- **External linkage:** Can be accessed anywhere within the file it is declared, or other files (via a forward declaration):
    - Functions
    - Non-const global variables (initialized or uninitialized)
    - Extern const global variables
    - Inline const global variables

## Inline functions

`inline` hints the compiler that a function would benefit from being expanded inline (bad practice).

```cpp
#include <iostream>

inline int min(int x, int y) // hint to the compiler that it should do inline expansion of this function
{
    return (x < y) ? x : y;
}

int main()
{
    std::cout << min(5, 6) << '\n';
    std::cout << min(3, 2) << '\n';
    return 0;
}
```

## Constexpr and consteval function

A **constexpr function** is a function whose return value may be computed at compile-time.

```cpp
#include <iostream>

constexpr int greater(int x, int y) // now a constexpr function
{
    return (x > y ? x : y);
}

int main()
{
    constexpr int x{ 5 };
    constexpr int y{ 6 };

    // We'll explain why we use variable g here later in the lesson
    constexpr int g { greater(x, y) }; // will be evaluated at compile-time

    std::cout << g << " is greater!\n";

    return 0;
}
```

To be eligible for compile-time evaluation, a function must have a constexpr return type and not call any non-constexpr functions. A call to the function must have constexpr arguments.

An eligible constexpr function *must* be evaluated at compile-time if the return value is used where a constant expression is required.

Since C++20, `std::is_constant_evaluated()` in `<type_traits>` returns a `bool` indicating whether the current function call is executing in a constant context.

C++20 introduced `consteval` which is used to indicate that a function *must* evaluate at compile-time, otherwise a compile error will result. These functions are called **immediate functions**.

```cpp
#include <iostream>

consteval int greater(int x, int y) // function is now consteval
{
    return (x > y ? x : y);
}

int main()
{
    constexpr int g { greater(5, 6) };              // ok: will evaluate at compile-time
    std::cout << g << '\n';

    std::cout << greater(5, 6) << " is greater!\n"; // ok: will evaluate at compile-time

    int x{ 5 }; // not constexpr
    std::cout << greater(x, 6) << " is greater!\n"; // error: consteval functions must evaluate at compile-time

    return 0;
}
```

## Unnamed and inline namespaces

**Inline namespaces** are typically used to version content.

```cpp
#include <iostream>

inline namespace V1 // declare an inline namespace named V1
{
    void doSomething()
    {
        std::cout << "V1\n";
    }
}

namespace V2 // declare a normal namespace named V2
{
    void doSomething()
    {
        std::cout << "V2\n";
    }
}

int main()
{
    V1::doSomething(); // calls the V1 version of doSomething()
    V2::doSomething(); // calls the V2 version of doSomething()

    doSomething(); // calls the inline version of doSomething() (which is V1)

    return 0;
}
```

## Constexpr if statements

C++17 introduced the **constexpr if statement**, which requires the conditional to be a constant expression.

```cpp
#include <iostream>

int main()
{
	constexpr double gravity{ 9.8 };

	if constexpr (gravity == 9.8) // now using constexpr if
		std::cout << "Gravity is normal.\n";
	else
		std::cout << "We are not on Earth.\n";

	return 0;
}

// compiles this
int main()
{
	std::cout << "Gravity is normal.\n";

	return 0;
}
```

## Switch fallthrough and scoping

Use the `[[fallthrough]]` attribute to indicate intentional fallthrough.

```cpp
#include <iostream>

int main()
{
    switch (2)
    {
    case 1:
        std::cout << 1 << '\n';
        break;
    case 2:
        std::cout << 2 << '\n'; // Execution begins here
        [[fallthrough]]; // intentional fallthrough -- note the semicolon to indicate the null statement
    case 3:
        std::cout << 3 << '\n'; // This is also executed
        break;
    }

    return 0;
}
```

You can declare or define (but not initialize) variables inside the switch, both before and after the case labels.

## Halts (exiting your program early)

`std::exit()` causes *normal* termination.

`std::abort()` causes *abnormal* termination.

`std::terminate()` is usually used with exceptions, and calls `std::abort()`.

## Assert and static_assert

```cpp
#include <cassert> // for assert()

assert(found && "Car could not be found in database");

// must be a constant expression
static_assert(sizeof(long) == 8, "long must be 8 bytes"); // checked at compile-time
