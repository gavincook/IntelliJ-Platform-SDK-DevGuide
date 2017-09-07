---
title: Custom Language Support
---


*IntelliJ Platform* is a powerful platform for building development tools targeting *any* language.
Most of IDE features consist of language-independent and language-specific parts, and you can support a particular feature for your language with a small amount of effort:
you just need to implement the language-specific part, and the language-independent part is provided for you by the platform.

This part of the documentation will explain the main concepts of the *Language API* and will guide you through the sequence of steps which are usually required to develop a custom language plugin.
You can obtain additional information about the *Language API* from the JavaDoc comments for the *Language API* classes and from the source code of the Properties language support, which is part of the
[IntelliJ IDEA Community Edition](https://github.com/JetBrains/intellij-community)
source code.


If you prefer a full example to the detailed description offered on this page, please check out a step-by-step tutorial how to define custom language support on example of ".properties" files:
[Custom Language Support Tutorial](/tutorials/custom_language_support_tutorial.md)

Providing custom language support includes the following major steps:

* [Registering File Type](/reference_guide/custom_language_support/registering_file_type.md)
* [Implementing Lexer](/reference_guide/custom_language_support/implementing_lexer.md)
* [Implementing Parser and PSI](/reference_guide/custom_language_support/implementing_parser_and_psi.md)
* [Syntax Highlighting and Error Highlighting](/reference_guide/custom_language_support/syntax_highlighting_and_error_highlighting.md)
* [References and Resolve](/reference_guide/custom_language_support/references_and_resolve.md)
* [Code Completion](/reference_guide/custom_language_support/code_completion.md)
* [Find Usages](/reference_guide/custom_language_support/find_usages.md)
* [Rename Refactoring](/reference_guide/custom_language_support/rename_refactoring.md)
* [Safe Delete Refactoring](/reference_guide/custom_language_support/safe_delete_refactoring.md)
* [Code Formatter](/reference_guide/custom_language_support/code_formatting.md)
* [Code Inspections and Intentions](/reference_guide/custom_language_support/code_inspections_and_intentions.md)
* [Structure View](/reference_guide/custom_language_support/structure_view.md)
* [Surround With](/reference_guide/custom_language_support/surround_with.md)
* [Go to Class and Go to Symbol](/reference_guide/custom_language_support/go_to_class_and_go_to_symbol.md)
* [Documentation](/reference_guide/custom_language_support/documentation.md)
* [Additional Minor Features](/reference_guide/custom_language_support/additional_minor_features.md)


Please ask questions or suggest missing topics in [plugin development forum](https://intellij-support.jetbrains.com/hc/en-us/community/topics/200366979-IntelliJ-IDEA-Open-API-and-Plugin-Development).
