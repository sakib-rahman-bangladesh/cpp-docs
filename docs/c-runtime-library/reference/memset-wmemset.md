---
title: "memset, wmemset"
description: "Learn more about: memset, wmemset"
ms.date: "1/15/2021"
api_name: ["wmemset", "memset", "_o_memset"]
api_location: ["msvcrt.dll", "msvcr80.dll", "msvcr90.dll", "msvcr100.dll", "msvcr100_clr0400.dll", "msvcr110.dll", "msvcr110_clr0400.dll", "msvcr120.dll", "msvcr120_clr0400.dll", "ntdll.dll", "ucrtbase.dll", "api-ms-win-crt-string-l1-1-0.dll", "ntoskrnl.exe", "api-ms-win-crt-private-l1-1-0.dll"]
api_type: ["DLLExport"]
topic_type: ["apiref"]
f1_keywords: ["memset", "wmemset"]
helpviewer_keywords: ["wmemset function", "memset function"]
---
# `memset`, `wmemset`

Sets buffers to a specified character.

## Syntax

```C
void *memset(
   void *dest,
   int c,
   size_t count
);
wchar_t *wmemset(
   wchar_t *dest,
   wchar_t c,
   size_t count
);
```

### Parameters

*`dest`*\
Pointer to destination.

*`c`*\
Character to set.

*`count`*\
Number of characters.

## Return Value

The value of *`dest`*.

## Remarks

Sets the first *`count`* characters of *`dest`* to the character *`c`*.

**Security Note** Make sure that the destination buffer has enough room for at least *`count`* characters. For more information, see [Avoiding Buffer Overruns](/windows/win32/SecBP/avoiding-buffer-overruns).

By default, this function's global state is scoped to the application. To change this, see [Global state in the CRT](../global-state.md).

## Requirements

|Routine|Required header|
|-------------|---------------------|
|**`memset`**|`<memory.h>` or `<string.h>`|
|**`wmemset`**|`<wchar.h>`|

For more compatibility information, see [Compatibility](../../c-runtime-library/compatibility.md).

## Libraries

All versions of the [C run-time libraries](../../c-runtime-library/crt-library-features.md).

## Example

```C
// crt_memset.c
/* This program uses memset to
* set the first four chars of buffer to "*".
*/

#include <memory.h>
#include <stdio.h>

int main( void )
{
   char buffer[] = "This is a test of the memset function";

   printf( "Before: %s\n", buffer );
   memset( buffer, '*', 4 );
   printf( "After:  %s\n", buffer );
}
```

### Output

```Output
Before: This is a test of the memset function
After:  **** is a test of the memset function
```

Here's an example of the use of wmemset:

```C
// crt_wmemset.c
/* This program uses memset to
* set the first four chars of buffer to "*".
*/

#include <wchar.h>
#include <stdio.h>

int main( void )
{
   wchar_t buffer[] = L"This is a test of the wmemset function";

   wprintf( L"Before: %s\n", buffer );
   wmemset( buffer, '*', 4 );
   wprintf( L"After:  %s\n", buffer );
}
```

### Output

```Output
Before: This is a test of the wmemset function
After:  **** is a test of the wmemset function
```

## See also

[Buffer Manipulation](../../c-runtime-library/buffer-manipulation.md)\
[`_memccpy`](memccpy.md)\
[`memchr`, `wmemchr`](memchr-wmemchr.md)\
[`memcmp`, `wmemcmp`](memcmp-wmemcmp.md)\
[`memcpy`, `wmemcpy`](memcpy-wmemcpy.md)\
[`_strnset`, `_strnset_l`, `_wcsnset`, `_wcsnset_l`, `_mbsnset`, `_mbsnset_l`](strnset-strnset-l-wcsnset-wcsnset-l-mbsnset-mbsnset-l.md)