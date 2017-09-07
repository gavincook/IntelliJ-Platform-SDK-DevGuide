---
title: Light and Heavy Tests
---

Plugin tests run in a real, rather than mocked, *IntelliJ Platform* environment and use real implementations for most of the application and project components. 

Loading all the project components for a project in order to run tests is a fairly expensive operation, and we want to avoid doing it for each test. Dependently on the loading and execution time we make a difference between *heavy* tests and *light* tests available in *IntelliJ Platform* test framework:
 
* *Heavy* tests create a new project for each test.
* *Light* reuse a project from the previous test run when possible.

Light and heavy tests use different base classes or fixture classes, as described below.

> **Note** Because of the performance difference, we recommend plugin developers to write light tests whenever possible.

The standard way of writing a light test is to extend the following classes:

* [`LightPlatformCodeInsightFixtureTestCase`](upsource:///platform/testFramework/src/com/intellij/testFramework/fixtures/LightPlatformCodeInsightFixtureTestCase.java) for tests that don't have any Java dependencies.
* [`LightCodeInsightFixtureTestCase`](upsource:///java/testFramework/src/com/intellij/testFramework/fixtures/LightCodeInsightFixtureTestCase.java) for tests that require the Java PSI or any related functionality.

When writing a light test, you can specify the requirements for the project that you need to have in your test, such as the module type, the configured SDK, facets, libraries etc. You do so by extending the [`LightProjectDescriptor`](upsource:///platform/testFramework/src/com/intellij/testFramework/LightProjectDescriptor.java) class and returning your project descriptor from `LightCodeInsightFixtureTestCase.getProjectDescriptor()`. Before executing each test, the project will be reused if the test case returns the same project descriptor as the previous one, or recreated if the descriptor is different.

> **Note** If you need to set up a multi-module project for your tests, you must write a heavy test. 

The setup code for a multi-module Java project looks something like that:

```java
final TestFixtureBuilder<IdeaProjectTestFixture> projectBuilder = IdeaTestFixtureFactory.getFixtureFactory().createFixtureBuilder(getName());

// Repeat the following line for each module
final JavaModuleFixtureBuilder moduleFixtureBuilder = projectBuilder.addModule(JavaModuleFixtureBuilder.class);

myFixture = JavaTestFixtureFactory.getFixtureFactory().createCodeInsightFixture(projectBuilder.getFixture());
```
