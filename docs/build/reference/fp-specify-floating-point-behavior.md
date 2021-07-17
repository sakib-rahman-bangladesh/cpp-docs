---
description: "Learn more about: /fp (Specify floating-point behavior)"
title: "/fp (Specify floating-point behavior)"
ms.date: 07/15/2021
f1_keywords: ["VC.Project.VCCLCompilerTool.floatingPointModel", "VC.Project.VCCLWCECompilerTool.FloatingPointExceptions", "/fp", "VC.Project.VCCLWCECompilerTool.floatingPointModel", "VC.Project.VCCLCompilerTool.FloatingPointExceptions"]
helpviewer_keywords: ["-fp compiler option [C++]", "/fp compiler option [C++]"]
ms.assetid: 10469d6b-e68b-4268-8075-d073f4f5d57e
---
# /fp (Specify floating-point behavior)

Specifies how the compiler treats floating-point expressions, optimizations, and exceptions. The **/fp** options specify whether the generated code allows floating-point environment changes to the rounding mode, exception masks, and subnormal behavior, and whether floating-point status checks return current, accurate results. It controls whether the compiler generates code that maintains source operation and expression ordering and conforms to the standard for NaN propagation, or if it instead generates more efficient code that may reorder or combine operations and use simplifying algebraic transformations that aren't allowed by the standard.

## Syntax

**`/fp:precise`**\
**`/fp:strict`**\
**`/fp:fast`**\
**`/fp:except`**\[**`-`**]

### Arguments

#### <a name="precise"></a> `/fp:precise`

By default, the compiler uses **`/fp:precise`** behavior.

Under **`/fp:precise`**, the compiler preserves the source expression ordering and rounding properties of floating-point code when it generates and optimizes object code for the target machine. The compiler rounds to source code precision at four specific points during expression evaluation: at assignments, typecasts, when floating-point arguments get passed to a function call, and when a function call returns a floating-point value. Intermediate computations may be performed at machine precision. Typecasts can be used to explicitly round intermediate computations.

The compiler doesn't perform algebraic transformations on floating-point expressions, such as reassociation or distribution, unless it can guarantee the transformation produces a bitwise identical result. Expressions that involve special values (NaN, +infinity, -infinity, -0.0) are processed according to IEEE-754 specifications. For example, `x != x` evaluates to **`true`** if `x` is NaN. Floating-point *contractions*, machine instructions that combine floating-point operations, may be generated under **`/fp:precise`**.

The compiler generates code intended to run in the [default floating-point environment](#the-default-floating-point-environment). It also assumes the floating-point environment isn't accessed or modified at runtime. That is, it assumes the code doesn't unmask floating-point exceptions, read or write floating-point status registers, or change rounding modes.

If your floating-point code doesn't depend on the order of operations and expressions in your floating-point statements (for example, if you don't care whether `a * b + a * c` is computed as `(b + c) * a` or `2 * a` as `a + a`), consider the [`/fp:fast`](#fast) option, which can produce faster, more efficient code. If your code both depends on the order of operations and expressions, and accesses or alters the floating-point environment (for example, to change rounding modes or to trap floating-point exceptions), use [`/fp:strict`](#strict).

#### <a name="strict"></a> `/fp:strict`

**`/fp:strict`** has behavior similar to **`/fp:precise`**, that is, the compiler preserves the source ordering and rounding properties of floating-point code when it generates and optimizes object code for the target machine, and observes the standard when handling special values. The program may also safely access or modify the floating-point environment at runtime.

Under **`/fp:strict`**, the compiler generates code that allows the program to safely unmask floating-point exceptions, read or write floating-point status registers, or change rounding modes. It rounds to source code precision at four specific points during expression evaluation: at assignments, typecasts, when floating-point arguments get passed to a function call, and when a function call returns a floating-point value. Intermediate computations may be performed at machine precision. Typecasts can be used to explicitly round intermediate computations. The compiler doesn't make any algebraic transformations on floating-point expressions, such as reassociation or distribution, unless it can guarantee the transformation produces a bitwise identical result. Expressions that involve special values (NaN, +infinity, -infinity, -0.0) are processed according to IEEE-754 specifications. For example, `x != x` evaluates to **`true`** if `x` is NaN. Floating-point contractions aren't generated under **`/fp:strict`**.

**`/fp:strict`** is computationally more expensive than **`/fp:precise`** because the compiler must insert extra instructions to trap exceptions and allow programs to access or modify the floating-point environment at runtime. If your code doesn't use this capability, but requires source code ordering and rounding, or relies on special values, use **`/fp:precise`**. Otherwise, consider using **`/fp:fast`**, which can produce faster and smaller code.

#### <a name="fast"></a> `/fp:fast`

The **`/fp:fast`** option allows the compiler to reorder, combine, or simplify floating-point operations to optimize floating-point code for speed and space. The compiler may omit rounding at assignment statements, typecasts, or function calls. It may reorder operations or make algebraic transforms, for example, by use of associative and distributive laws. It may reorder code even if such transformations result in observably different rounding behavior. Because of this enhanced optimization, the result of some floating-point computations may differ from the ones produced by other **`/fp`** options. Special values (NaN, +infinity, -infinity, -0.0) may not be propagated or behave strictly according to the IEEE-754 standard. Floating-point contractions may be generated under **`/fp:fast`**. The compiler is still bound by the underlying architecture under **`/fp:fast`**, and more optimizations may be available through use of the [`/arch`](arch-minimum-cpu-architecture.md) option.

Under **`/fp:fast`**, the compiler generates code intended to run in the default floating-point environment and assumes the floating-point environment isn't accessed or modified at runtime. That is, it assumes the code doesn't unmask floating-point exceptions, read or write floating-point status registers, or change rounding modes.

`/fp:fast` is intended for programs that don't require strict source code ordering and rounding of floating-point expressions, and don't rely on the standard rules for handling special values such as NaN. If your floating-point code requires preservation of source code ordering and rounding, or relies on standard behavior of special values, use [`/fp:precise`](#precise). If your code accesses or modifies the floating-point environment to change rounding modes, unmask floating-point exceptions, or check floating-point status, use [`/fp:strict`](#strict).

#### <a name="except"></a> `/fp:except`

The **`/fp:except`** option generates code to ensures that any unmasked floating-point exceptions are raised at the exact point at which they occur, and that no other floating-point exceptions are raised. By default, the **`/fp:strict`** option enables **`/fp:except`**, and **`/fp:precise`** doesn't. The **`/fp:except`** option isn't compatible with **`/fp:fast`**. The option can be explicitly disabled by us of **`/fp:except-`**.

By itself, **`/fp:except`** doesn't enable any floating-point exceptions. However, it's required for programs to enable floating-point exceptions. For more information on how to enable floating-point exceptions, see [`_controlfp`](../../c-runtime-library/reference/control87-controlfp-control87-2.md).

## Remarks

Multiple **`/fp`** options can be specified in the same compiler command line. Only one of **`/fp:strict`**, **`/fp:fast`**, and **`/fp:precise`** options can be in effect at a time. If you specify more than one of these options on the command line, the later option takes precedence and the compiler generates a warning. The **`/fp:strict`** and **`/fp:except`** options aren't compatible with **`/clr`**.

The [`/Za`](za-ze-disable-language-extensions.md) (ANSI compatibility) option isn't compatible with **`/fp`**.

### Using compiler directives to control floating-point behavior

The compiler provides three pragma directives to override the floating-point behavior specified on the command line: [`float_control`](../../preprocessor/float-control.md), [`fenv_access`](../../preprocessor/fenv-access.md), and [`fp_contract`](../../preprocessor/fp-contract.md). You can use these directives to control floating-point behavior at function-level, not within a function. These directives don't correspond directly to the **`/fp`** options. This table shows how the **`/fp`** options and pragma directives map to each other. For more information, see the documentation for the individual options and pragma directives.

| Option | float_control(precise) | float_control(except) | fenv_access | fp_contract |
|--|--|--|--|--|
| `/fp:fast` | off | off | off | on |
| `/fp:precise` | on | off | off | on |
| `/fp:strict` | on | on | on | off |

### The default floating point environment

When a process is initialized, the *default floating point environment* is set. This environment masks all floating point exceptions, sets the rounding mode to round to nearest (`FE_TONEAREST`), preserves subnormal (denormal) values, uses the default precision of significand (mantissa) for **`float`**, **`double`**, and **`long double`** values, and where supported, sets the infinity control to the default affine mode.

### Floating-point environment access and modification

The Microsoft Visual C++ runtime provides several functions to access and modify the floating-point environment. These include [`_controlfp`](../../c-runtime-library/reference/control87-controlfp-control87-2.md), [`_clearfp`](../../c-runtime-library/reference/clear87-clearfp.md), and [`_statusfp`](../../c-runtime-library/reference/status87-statusfp-statusfp2.md) and their variants. To ensure correct program behavior when your code accesses or modifies the floating-point environment, `fenv_access` must be enabled, either by the **`/fp:strict`** option or by use of the `fenv_access` pragma, for these functions to have any effect. When `fenv_access` isn't enabled, access or modification of the floating-point environment may result in unexpected program behavior:

- Code may not honor requested changes to the floating-point environment,

- The floating-point status registers may not report expected or current results,

- Unexpected floating-point exceptions may occur or expected floating-point exceptions may not occur.

When your code accesses or modifies the floating-point environment, you must be careful when you combine code where `fenv_access` is enabled with code that doesn't have `fenv_access` enabled. In code where `fenv_access` isn't enabled, the compiler assumes the platform default floating-point environment is in effect. It also assumes the floating-point status isn't accessed or modified. We recommend you save and restore the local floating-point environment to its default state before control is transferred to a function that doesn't have `fenv_access` enabled. This example demonstrates how the `float_control` pragma can be set and restored:

```cpp
#pragma float_control(precise, on, push)
// Code that uses /fp:strict mode
#pragma float_control(pop)
```

### Floating-point rounding modes

Under both **`/fp:precise`** and **`/fp:fast`**, the compiler generates code intended to run in the default floating-point environment. It assumes that the environment isn't accessed or modified at runtime. That is, the compiler assumes the code never unmasks floating-point exceptions, reads or writes floating-point status registers, or changes rounding modes. However, some programs need to alter the floating-point environment. For example, this sample computes error bounds of a floating-point multiplication by altering floating-point rounding modes:

```cpp
// fp_error_bounds.cpp
#include <iostream>
#include <limits>
using namespace std;

int main(void)
{
    float a = std::<float>::max();
    float b = -1.1;
    float cLower = 0.0;
    float cUpper = 0.0;
    unsigned int control_word = 0;
    int err = 0;

    // compute lower error bound.
    // set rounding mode to -infinity.
    err = _controlfp_s(&control_word, _RC_DOWN, _MCW_RC);
    if (err)
    {
        cout << "_controlfp_s(&control_word, _RC_DOWN, _MCW_RC) failed with error:" << err << endl;
    }  
    cLower = a * b;

    // compute upper error bound.
    // set rounding mode to +infinity.
    err = _controlfp_s(&control_word, _RC_UP, _MCW_RC);
    if (err)
    {
        cout << "_controlfp_s(&control_word, _RC_UP, _MCW_RC) failed with error:" << err << endl;
    }
    cUpper = a * b;

    // restore default rounding mode.
    err = _controlfp_s(&control_word, _CW_DEFAULT, _MCW_RC);
    if (err)
    {
        cout << "_controlfp_s(&control_word, _CW_DEFAULT, _MCW_RC) failed with error:" << err << endl;
    }
    // display error bounds.
    cout << "cLower = " << cLower << endl;
    cout << "cUpper = " << cUpper << endl;
    return 0;
}
```

Since the compiler assumes the default floating point environment under **`/fp:fast`** and **`/fp:precise`**, it's free to ignore the calls to `_controlfp_s`. For example, when compiled by using both **`/O2`** and **`/fp:precise`** for the x86 architecture, the bounds aren't computed, and the sample program outputs:

```Output
cLower = -inf
cUpper = -inf
```

When compiled by using both **`/O2`** and **`/fp:strict`** for the x86 architecture, the sample program outputs:

```Output
cLower = -inf
cUpper = -3.40282e+38
```

### Floating-point special values

Under **`/fp:precise`** and **`/fp:strict`**, expressions that involve special values (NaN, +infinity, -infinity, -0.0) behave according to the IEEE-754 specifications. Under **`/fp:fast`**, the behavior of these special values may be inconsistent with IEEE-754.

This sample demonstrates the different behavior of special values under **`/fp:precise`**, **`/fp:strict`**, and **`/fp:fast`**:

```cpp
// fp_special_values.cpp
#include <stdio.h>
#include <cmath>

float gf0 = -0.0;

int main()
{
    float f1 = INFINITY;
    float f2 = NAN;
    float f3 = -INFINITY;
    bool a, b;
    float c, d, e;
    a = (f1 == f1);
    b = (f2 == f2);
    c = (f1 - f1);
    d = (f2 - f2);
    printf("INFINITY == INFINITY : %d\n", a);
    printf("NAN == NAN           : %d\n", b);
    printf("INFINITY - INFINITY  : %f\n", c);
    printf("NAN - NAN            : %f\n", d);

    e = gf0 / abs(f3);
    printf("std::signbit(-0.0/-INFINITY): %d\n", std::signbit(c));
    return 0;
}
```

When compiled by using `/O2 /fp:precise` or `/O2 /fp:strict` for x86 architecture, the outputs are consistent with the IEEE-754 specification:

```Output
INFINITY == INFINITY : 1
NAN == NAN           : 0
INFINITY - INFINITY  : -nan(ind)
NAN - NAN            : -nan(ind)
std::signbit(-0.0/-INFINITY): 1
```

When compiled by using `/O2 /fp:fast`** for x86 architecture, the outputs aren't consistent with IEEE-754:

```Output
INFINITY == INFINITY : 1
NAN == NAN           : 1
INFINITY - INFINITY  : 0.000000
NAN - NAN            : 0.000000
std::signbit(-0.0/-INFINITY): 0
```

### Floating-point algebraic transformations

Under **`/fp:precise`** and **`/fp:strict`**, the compiler doesn't perform mathematical transformations unless the transformation is guaranteed to produce a bitwise identical result. The compiler may make such transformations under **`/fp:fast`**. For example, the expression `a * b + a * c` in the sample function `algebraic_transformation` may be compiled into `a * (b + c)` under **`/fp:fast`**. Such transformations aren't done under **`/fp:precise`** or **`/fp:strict`**, and the compiler generates `a * b + a * c`.

```cpp
float algebraic_transformation (float a, float b, float c)
{
    return a * b + a * c;
}
```

### Floating-point explicit casting points

Under **`/fp:precise`** and **`/fp:strict`**, the compiler rounds to source code precision at four specific points during expression evaluation: at assignments, typecasts, when floating-point arguments get passed to a function call, and when a function call returns a floating-point value. Typecasts can be used to explicitly round intermediate computations. Under **`/fp:fast`**, the compiler doesn't generate explicit casts at these points to guarantee source code precision. This sample demonstrates the behavior under different **`/fp`** options:

```cpp
float casting(float a, float b)
{
    return 5.0*((double)(a+b));
}
```

When compiled by using `/O2 /fp:precise` or `/O2 /fp:strict`, you can see that explicit type casts are inserted at both the typecast and at the function return point in the generated code for the x64 architecture:

```asm
        addss    xmm0, xmm1
        cvtss2sd xmm0, xmm0
        mulsd    xmm0, QWORD PTR __real@4014000000000000
        cvtsd2ss xmm0, xmm0
        ret      0
```

Under `/O2 /fp:fast` the generated code is simplified, because all type casts are optimized away:

```asm
        addss    xmm0, xmm1
        mulss    xmm0, DWORD PTR __real@40a00000
        ret      0
```

### To set this compiler option in the Visual Studio development environment

1. Open the project's **Property Pages** dialog box. For details, see [Set C++ compiler and build properties in Visual Studio](../working-with-project-properties.md).

1. Select the **Configuration Properties** > **C/C++** > **Code Generation** property page.

1. Modify the **Floating Point Model** property.

### To set this compiler option programmatically

- See <xref:Microsoft.VisualStudio.VCProjectEngine.VCCLCompilerTool.floatingPointModel%2A>.

## See also

[MSVC compiler options](compiler-options.md)<br/>
[MSVC compiler command-line syntax](compiler-command-line-syntax.md)<br/>
