---
title: Coding Style
permalink: wiki/Coding_Style/
layout: wiki
tags:
 - DeveloperPages
---

This page describes the coding style used in
[xf86-input-wacom](xf86-input-wacom "wikilink"). If you submit a patch
please make sure the style adheres to this guideline or the patch may be
rejected.

C coding style
==============

-   Tab is set to 8 characters
-   Default indentation is one tab
-   Indentation with tabs and remaining spaces to lign up code where
    necessary. Example below shows tabs with "*&gt;----*" and spaces
    with "*.*".

<!-- -->

    >-------if.(InitValuatorClassDeviceStruct(pInfo->dev, nbaxes,
    #if.GET_ABI_MAJOR(ABI_XINPUT_VERSION) >= 7
    >------->------->------->------->-------..axis_labels,
    #endif
    >-------                ..GetMotionHistorySize(),
    >-------                ..(is_absolute(pInfo) ?  Absolute : Relative) | OutOfProximity) == FALSE)

-   Opening curly braces { go on a new line. Closing curly braces } go
    on the same line as else. e.g.

<!-- -->

    if (condition)
    {
            do_something();
    } else {
            do_something_else();
    }

-   Declarations *should* be aligned. e.g.

<!-- -->

    int a   = 0;
    int foo = 10;
    int bar = 11;

-   No [hungarian
    notation](http://en.wikipedia.org/wiki/Hungarian_notation). There
    are a few exceptions for this (*pInfo*, *pDev* and some remainders
    of old code).
-   Use const for pointers where appropriate.

Each new file requires the following line at the bottom of the file:

` /* vim: set noexpandtab tabstop=8 shiftwidth=8: */`

Doxygen style
=============

[xf86-input-wacom](xf86-input-wacom "wikilink") has a strict requirement
on [doxygen](http://www.doxygen.org). This means that *any* new function
must have doxygen comments explaining the function's purpose, it's input
and its output. Very few exception will be granted.

We use javadoc-style comments with autobrief enabled. Thus, [doxygen
keywords](http://www.stack.nl/~dimitri/doxygen/commands.html) need to be
prefixed with "*@*" and the first line of a comment must be a summary of
the function.

The three most common keywords and their usage are:

-   **@param <param name> <param description>**: describes a parameter
    of name *param name*. This keyword must be present for all
    parameters of a function.
    -   Special case: **@param\[out\] <param name> &lt;param
        description**: describes a parameter that returns a value.
    -   Special case: **@param\[in\|out\] <param name> &lt;param
        description**: describes a parameter that is used as input *and*
        output, thus returning a value.
    -   Special case: **@param\[in\] <param name> &lt;param
        description**: describes a parameter that is only used as input
        value. This case may be ommitted but if a function provides
        several *in* or *out* parameters, it is helpful to explicitly
        specify it.
-   **@return <return value description>**: describes the return value
    for a function. This keyword must be present for all functions that
    return a value, unless *@retval* is used.
-   **@retval <return value> <return value description>**: describes a
    specific return value that may be returned by the function. This
    keyword may be used instead of *@return* provided all possible
    values are described. This keyword may be used in addition to
    *@return* for special return values (e.g. error codes).

A function description should be brief and comprehensive, describing the
**intent** of the function, but not necessarily the details of how it
works.

The [xf86-input-wacom](xf86-input-wacom "wikilink") driver is not a
library, no-one cares about our header files. Our comments are to aid
driver developers only and thus comments must be placed before the
*code* of the function, not in the declaration of the function.

Example:

    /**
     * Copy matching foo structs into freshly allocated memory.
     *
     * This function determines the required size to hold a number of foo structs
     * and copies the matching foo structs into this memory. Structs that don't 
     * match the filter are skipped.
     * 
     * This function allocates memory to be freed by the caller.
     *
     * @param foo The list of foos to copy.
     * @param filter Only foos matching this filter will be copied.
     * @param[out] data The list of newly copied foos. Memory is allocated and must
     *                  be freed by the caller. If 0 or -1 is returned, the value
     *                  of data is undefined.
     *
     * @return Returns the number of foos copied into data.
     * @retval 0 List of foos was empty or no foos matched the filter.
     * @retval -1 Allocation error.
     */
    int allocate_and_copy(const struct foo *foos, int filter, char **data)
    {
       ...
    }

The aim for any function description is that anyone reading the comment
should be able to [write a test](#Writing_tests "wikilink") against this
function *without ever looking at the function code.*
