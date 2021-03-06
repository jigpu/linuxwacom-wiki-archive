---
title: TestSuite
permalink: wiki/TestSuite/
layout: wiki
tags:
 - DeveloperPages
---

The driver features a built-in test suite for [white box
testing](http://en.wikipedia.org/wiki/White-box_testing). Simply running

` make check`

will compile and run the tests.

How it works
------------

All test are located in the `test/` directory and they re-use the driver
sources. The setup is rather special as we want to test functions in the
driver that are declared static (and thus not accessible from outside).

Thus, the environment is set up that the keywork *static* is undefined
on compilation. This will generate a number of warnings on compilation
time but the resulting object files will have any symbol accessible.

However, the disadvantage is that we cannot use the *static* keyword in
tests, especially not for variables.

Writing a test
--------------

To write a new test, look at `test/wacom-tests.c` and take guidance from
there. The basic approach is to add simply call the test function from
`main()`. Prefix all test functions with *test*.

The test function itself must set up the required environment and then
call the actual functions to test. Afterwards, use `assert` to ensure
the right values are returned.

    static void
    test_wcm_some_function(void)
    {

      ... do some setup here

      othervalue = wcm_some_function(somevalue);
      assert(somevalue > othervalue);
      assert(othervalue >= 0);
      ...
    }
