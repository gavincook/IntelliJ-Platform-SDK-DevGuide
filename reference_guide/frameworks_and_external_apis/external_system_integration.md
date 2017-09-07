---
title: External System Integration
---

# Purpose

This page provides high-level overview of *External System* sub-system.

# Rationale

There are multiple project management systems ([maven](http://maven.apache.org/), [gradle](http://www.gradle.org/), [sbt](http://www.scala-sbt.org/) etc) and it's good to support them at the IDE. Luckily, they all provide a similar set of facilities from the integration point of view:

*   build IDE project from external system config (pom.xml, build.gradle etc);
*   provide a list of available tasks;
*   allow to execute a particular task;
*   ...

That means that we can separate external system-specific logic and general IDE processing. *'External system'* sub-system provides simple api for wrapping external system and extensible IDE-specific processing logic.

# Project management

## Project data domain

**General**  
External system wrapper is required to be able to build project info on the basis of the given external system config. That information is built using in terms of [DataNode](upsource:///platform/external-system-api/src/com/intellij/openapi/externalSystem/model/DataNode.java), [Key](upsource:///platform/external-system-api/src/com/intellij/openapi/externalSystem/model/Key.java) and [ProjectEntityData](upsource:///platform/external-system-api/src/com/intellij/openapi/externalSystem/model/project/ProjectEntityData.java).

![DataNode](/reference_guide/img/data-node.png)

Here *DataNode* class is just a holder for the target data (data type is defined by the *Key*). Multiple DataNode objects might be organized in directed graph where every edge identifies parent-child relation.

For example, simple one-module project might look as below: 
 
![DataNode Example](/reference_guide/img/data-node-example.png)


**Consequence**  
The IDE provides a set of built-in *Key*s and *ProjectEntityData*s but any external system integration or third-party plugin developer might enhance project data by defining her own *Key* and *ProjectEntityData* and storing them at a child of appropriate *DataNode*.

## Managing project data

We need to process project data is built on external system config basis. Here comes [ProjectDataService](upsource:///platform/external-system-api/src/com/intellij/openapi/externalSystem/service/project/manage/ProjectDataService.java). It is a strategy which knows how to manage particular *ProjectEntityData*. For example, when we want to import a project from external model, we can start by the top level *DataNode* which references project info and then import its data using corresponding service.

Custom services can be defined via *'externalProjectDataService'* extension.

The good thing is that we can separate project parsing and management here. That means that a set of *DataNode*, *Key* and *ProjectDataServices* can be introduced for particular technology and then every external system integration can build corresponding data if necessary using it.

## Importing from external model

IntelliJ platform provides standard api for that. Namely, [ProjectImportBuilder](upsource:///projectImport/ProjectImportBuilder.java) and [ProjectImportProvider](upsource:///projectImport/ProjectImportProvider.java). There are two classes built on *template method* pattern - [AbstractExternalProjectImportBuilder](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/project/wizard/AbstractExternalProjectImportBuilder.java) and [AbstractExternalProjectImportProvider](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/project/wizard/AbstractExternalProjectImportProvider.java). They might be sub-classes and that concrete implementations should be registered at IoC descriptor (plugin.xml).

Here is an example from the gradle integration plugin:

    <projectImportProvider implementation="org.jetbrains.plugins.gradle.service.settings.GradleProjectImportProvider"/>
    <projectImportBuilder implementation="org.jetbrains.plugins.gradle.service.settings.GradleProjectImportBuilder"/>

Note that [AbstractExternalProjectImportBuilder](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/project/wizard/AbstractExternalProjectImportBuilder.java) is built on top of the 'external system settings' controls.

## Auto-import

It's possible to configure external system integration to automatically refresh project structure when external project's config file is modified. Basically, end-user should check corresponding box at external system settings for that:

![Auto-import](/reference_guide/img/use-auto-import.png)

Built-in support covers only root config files of linked external projects. However, there is a possible situation that particular external project has another config files which affect resulting project structure as well (for example, it might be a multi-project where every sub-project has its own config file). That's why it's possible to enhance that processing by making target external system implementation (*ExternalSystemManager*) implement *ExternalSystemAutoImportAware*. That allows to provide custom logic for mapping file modification events to the target external project affected by that.

**Note:** *ExternalSystemAutoImportAware.getAffectedExternalProjectPath()* is called quite often, that's why it's expected to return control as soon as possible. Helper *CachingExternalSystemAutoImportAware* class might be used for caching, i.e. *ExternalSystemManager* which implements *ExternalSystemAutoImportAware* can have a field like *'new CachingExternalSystemAutoImportAware(new MyExternalSystemAutoImportAware())'* and delegate *ExternalSystemAutoImportAware.getAffectedExternalProjectPath()* calls to it.

# Settings

The general idea is that all external system settings controls are represented by implementations of [ExternalSettingsControl](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/settings/ExternalSettingsControl.java) interface. There are also external system project-local settings and global external system settings. So, basically particular external system settings UI looks as below:

![Configurable](/reference_guide/img/configurable.png)

It's recommended to extend from [AbstractExternalProjectSettingsControl](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/settings/AbstractExternalProjectSettingsControl.java) for implementing project-level settings control as it already handles some of them.

Similar approach is used for providing 'import from external system' UI - implementation is expected to extended [AbstractImportFromExternalSystemControl](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/settings/AbstractImportFromExternalSystemControl.java) and it has not linked external projects list but target external project path control:

![Import from external system](/reference_guide/img/import.png)

# Using with 12.1.x IJ branch

Though main development is performed at the 'master' branch, there is a special 12.1.x-compatible version. Here are instructions on how to use it:

1.  Download external-system.zip;
2.  Unpack it and define *external-system.jar* as a library dependency;
3.  Configure sources from *external-system-src.jar* if necessary;
4.  Add definitions from *ExternalSystemExtensionPoints.xml* and *ExternalSystemExtensions.xml* to your *plugin.xml* (that **.xml files are bundled into *external-system-src.jar*). *Note:** it's very important to use custom namespace there because there is a possible case that more than one external system integration uses this approach to be compatible with 12.1.x;
