---
title: PSI Elements
---

A PSI (Program Structure Interface) file represents a hierarchy of PSI elements (so-called _PSI trees_). A single PSI file may include several PSI trees in a particular programming language. A PSI element, in its turn, can have child PSI elements.

PSI elements and operations on the level of individual PSI elements are used to explore the internal structure of source code as it is interpreted by the **IntelliJ Platform**. For example, you can use PSI elements to perform code analysis, such as [code inspections](http://www.jetbrains.com/idea/help/code-inspection.html) or [intention actions](http://www.jetbrains.com/idea/help/intention-actions.html).

The [PsiElement](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java) class is the common base class for PSI elements.

## How do I get a PSI element?

* From an action: `e.getData(LangDataKeys.PSI_ELEMENT)`. Note: if an editor is currently open and the element under caret is a reference, this will return the result of resolving the reference. This may or may not be what you need.
* From a file by offset: `PsiFile.findElementAt()`. Note: this returns the lowest level element at the specified offset, which is normally a lexer token.
Most likely you should use `PsiTreeUtil.getParentOfType()` to find the element you really need.
* By iterating through a PSI file: using a [`PsiRecursiveElementWalkingVisitor`](upsource:///platform/core-api/src/com/intellij/psi/PsiRecursiveElementWalkingVisitor.java).
* By resolving a reference: `PsiReference.resolve()`

## What can I do with PSI elements?

See [PSI Cook Book](/basics/psi_cookbook.md)
