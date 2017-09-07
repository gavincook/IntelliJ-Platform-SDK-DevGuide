---
title: Test Project and Testdata Directories
---

The test fixture creates a *test project* environment. Unless you customize the project creation, the test project will have one module with one source root called `src`. The files for the test project exist either in a temporary directory or in an in-memory file system, depending on which implementation of [`TempDirTestFixture`](upsource:///platform/testFramework/src/com/intellij/testFramework/fixtures/TempDirTestFixture.java) is used.

[`LightPlatformCodeInsightFixtureTestCase`](upsource:///platform/testFramework/src/com/intellij/testFramework/fixtures/LightPlatformCodeInsightFixtureTestCase.java) uses an in-memory implementation; if you set up the test environment by calling `IdeaTestFixtureFactory.createCodeInsightFixture`, you can specify the implementation to use.

> **Note** If your tests use the in-memory implementation, and you abort the execution of your tests, the persisted filesystem caches may get out of sync with the in-memory structures, and you may get spurious errors in your tests.
>
> If you get an unexpected error after a series of successful runs, **try running the test again**, and if that doesn't help, **delete the "system" subdirectory** under the sandbox directory specified in the *IntelliJ Platform* SDK settings.

In your plugin, you normally store the test data for your tests (such as files on which plugin features will be executed and expected output files) in the `testdata` directory. This is just a directory under the content root of your plugin, but not under a source root. Files in `testdata` are normally not valid source code and must not be compiled.

To specify the location of `testdata`, you must override the `LightPlatformCodeInsightFixtureTestCase.getTestDataPath()` method. The default implementation assumes running as part of the *IntelliJ Platform* source tree and is not appropriate for third-party plugins.

> **Note** A very common pattern in *IntelliJ Platform* tests is to use the name of the test method being executed as the base for building the `testdata` file paths. This allows to reuse most of the code between different test methods that test different aspects of the same feature, and this approach is also recommended for third-party plugin tests. The name of the test method can be retrieved using `UsefulTestCase.getTestName()`.

To copy files or directories from your `testdata` directory to the test project directory, you can use the `copyFileToProject()` and `copyDirectoryToProject()` methods in the [`CodeInsightTestFixture`](upsource:///platform/testFramework/src/com/intellij/testFramework/fixtures/CodeInsightTestFixture.java) class.

Most operations in plugin tests require a file open in the in-memory editor, in which highlighting, completion and other operations will be performed. The in-memory editor instance is returned by `CodeInsightTestFixture.getEditor()`. To copy a file from the `testdata` directory to the test project directory and immediately open it in the editor, you can use the `CodeInsightTestFixture.configureByFile()` or `configureByFiles()` methods. The latter copies multiple files to the test project directory and opens the *first* of them in the in-memory editor.

Alternatively, you can use one of the other methods which take parameters annotated with `@TestDataFile`. These methods copy the specified files from the `testdata` directory to the test project directory, open the first of the specified files in the in-memory editor, and then perform the requested operation such as highlighting or code completion.

When a file is opened in the in-memory editor, special markup in the file content can be used to specify the caret position or selection. You can use one of the following markers:

* `<caret>` specifies the position where the caret should be placed.
* `<selection>` and `</selection>` specify the start and end of the text range to be selected.
* `<block>` and `</block>` specify the start and end points of the column selection.
