# ExploTest

Untiy3d Unit Testing C# nano-framework from ExplodedViews.

# Rationale

The API is slightly inspired by another Unity specific unit testing
framework - [SharpUnit][]

[SharpUnit]: http://www.unifycommunity.com/wiki/index.php?title=SharpUnit

The main ExploTest's difference with SharpUnit is that when an assertion
fails test still continues. I also didn't try to make the framework
unity agnostic, which means I don't need "adaptor" classes to plug into
unity's log widget.

There are some other seemingly consmetic, but in truth conceptual,
differences which aren't described here.

# Using the framework

## Get the framework into your project

Place the framework sources anywhere in the project. I put them in
`Assets/Standard Assets/Testing/Framework`, but the exact place doesn't
really matter, as long as your **test runner class** (see below) can
access the framework classes. Refer to [script compilation order][order]
documentation if in doubt.

[order]: http://unity3d.com/support/documentation/ScriptReference/index.Script_compilation_28Advanced29.html

## Write test cases

Test cases are methods of a class derived from `Test.Case`. I followed the
SharpUnit example misnaming the class, which is actually more like a
fixture then a test case, I might rename it at some point.

Any method whose name starts with `Test_` (case insensitive, so `test_` or
`TEST_` will also work) is considered an individual test. It is useful
to give tests semantic names, like `Test_EnemyDiesWhenShot`, as they
will appear in fail messages.

A new instance of your `Test.Case` derived class will be created for
running each test method.  Setup common to all tests of a particular
class should go to the constructor, tear down code should go into
override of a Dispose method (`Test.Case` is [IDisposable][])

Use `Test.Case`'s `Assert_*` method to assert some conditions during the
test.

[IDisposable]: http://msdn.microsoft.com/en-us/library/system.idisposable.aspx

## Make a test runner

Test runner is a piece of code that calls `Test.Harness.Run(...)` static
method and provides it with a "test suite". There are no particular
constrains as to where this piece of code should be located - you can
have some test runners in your editor classes, running from the menu
command, others - in a `Start()` implementations of `MonoBehavior` based
script, or even inside an `Update()` implementation or a coroutine.

The test suite (a sequence of `Test.Case` descendant classes to run the
test methods of) is a params array of `Type`'s:

```
[MenuItem('Run the tests &t')]
static void RunTests()
{
  Test.Harness.Run(
    typeof(CharacterTests),
    typeof(ObstacleTests),
    typeof(EnemyTests),
  );
}
```
