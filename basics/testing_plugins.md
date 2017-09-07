---
title: Testing Plugins
---

Most of the tests in the *IntelliJ Platform* codebase are *model level functional tests*. What this means is the following:

* The tests run in a headless environment which uses real production implementations for the majority of components, except for a number of UI components.
* The tests usually test a feature as a whole, rather than individual functions that comprise its implementation.
* The tests do not test the Swing UI and work directly with the underlying model instead.
* Most of the tests take a source file or a set of source files as input data, execute a feature, and then compare the output with expected results. Results can be specified as another set of source files, as special markup in the input file, or directly in the test code.

The biggest benefit of this test approach is that tests are very stable and require very little maintenance once they have been written, no matter how much the underlying implementation is refactored or rewritten.

In a product with 10+ years of lifetime that has gone through a large number of internal refactorings, we find that this benefit greatly outweighs the downsides of slower test execution and more difficult debugging of failures being compared to more isolated unit tests.

Another consequence of our testing approach is what our test framework does not provide:

* We do not provide a recommended approach to mocking. We have a few tests in our codebase that use JMock, but in general we find it difficult to mock all of the interactions with *IntelliJ Platform* components that your plugin class will need to have, and we recommend working with real components instead.
* We do not provide a general-purpose framework for Swing UI testing. You can try using tools such as [FEST](https://code.google.com/p/fest/) or [Sikuli](http://www.sikuli.org/) for plugin UI testing, but we don't use either of them and cannot provide any guidelines for their use. Internally, we use manual testing for testing our Swing UIs.

* [Tests and Fixtures](/basics/testing_plugins/tests_and_fixtures.md)
* [Light and Heavy Tests](/basics/testing_plugins/light_and_heavy_tests.md)
* [Test Project and Testdata Directories](/basics/testing_plugins/test_project_and_testdata_directories.md)
* [Writing Tests](/basics/testing_plugins/writing_tests.md)
* [Testing Highlighting](/basics/testing_plugins/testing_highlighting.md)

Check out [this step-by-step tutorial](/tutorials/writing_tests_for_plugins.md) teaching how to write and run automated tests for your custom language plugin (source code included).
