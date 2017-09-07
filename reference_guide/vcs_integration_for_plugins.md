---
title: VCS Integration Plugins
---



This page gives an overview of the Version Control Integration API in IntelliJ IDEA 7.0.
Most of the concepts described here also apply to IntelliJ IDEA 6.0.
The VCS API was significantly refactored in version 6.0, and this document does not describe the API of older versions.

## Key Concepts

### FilePath

A [FilePath](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java)
represents a path to a file or directory on disk or in the VCS repository.
Unlike a
[VirtualFile](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFile.java),
a
[FilePath](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java)
can represent a path to a file which doesn't exist on disk.
The main difference between a
[FilePath](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java)
and a
[java.io.File](http://docs.oracle.com/javase/8/docs/api/java/io/File.html)
is that a
[FilePath](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java)
caches the
[VirtualFile](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFile.java)
corresponding to the path, so it can be retrieved without doing a VFS search.

To create instances of
[FilePath](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java),
the
[VcsContextFactory](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/actions/VcsContextFactory.java)
API is used.
It can be accessed as`PeerFactory.getInstance().getVcsContextFactory()`

[FilePath](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java)
representing paths in a VCS repository, rather than local paths, are created using
`VcsContextFactory.createFilePathOnNonLocal()`. `FilePath.isNonLocal()` returns true for such files.

### Revision Number

A
[VcsRevisionNumber](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/history/VcsRevisionNumber.java)
represents a revision number of the file.
If the VCS stores revision numbers as simple integers, the standard
[VcsRevisionNumber](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/history/VcsRevisionNumber.java).
Int implementation can be used.
If the VCS has a more complex format of revision numbers (like CVS, which uses a series of numbers delimited with dots), the plugin can provide a custom implementation.

### ContentRevision

A
[ContentRevision](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/changes/ContentRevision.java)
represents a particular revision of a file, which exists either locally or in a VCS repository.
It has three main attributes:

*  `FilePath` specifying the file of which this is a revision. If some version of the file exists locally, this should be a local path.

*  [`VcsRevisionNumber`](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/history/VcsRevisionNumber.java) specifying the revision number of the revision, or `VcsRevisionNumber.NULL` if the revision exists only locally.

*  Content of the revision.

The content is returned as string, and the VCS plugin is responsible for converting the binary file content to correct encoding.
To detect the encoding automatically based on the IDE settings and the byte order mark, the method `CharsetToolkit.bytesToString()` can be used (this API is new in IDEA 7.0.2).
Revisions of binary files can also be represented as BinaryContentRevision, which is a subclass of ContentRevision.
For binary revisions, the result of getContent() is undefined, and getBinaryContent() can be used to retrieve the contents as a byte array.

A useful class which can be used to represent the current on-disk version of a particular file is
[CurrentContentRevision](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/CurrentContentRevision.java).

### FileStatus

A
[FileStatus](upsource:///platform/editor-ui-api/src/com/intellij/openapi/vcs/FileStatus.java)
represents a status of a file in regard to VCS (unversioned, not changed, added, modified and so on).
It determines the color used to render the name of the file in the UI.

### Change

A
[Change](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/changes/Change.java)
represents a single file operation (creation, modification, move/rename or deletion) from a VCS point of view.
A Change can represent either a modification which the user has performed locally and not yet committed, a committed modification, or some other type of modification (for example, a shelved change or a difference between two arbitrary revisions).

A
[Change](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/changes/Change.java)
essentially consists of two content revisions:

*  before revision (`null` if the *Change* represents file creation)

*  after revision (`null` if the *Change* represents file deletion)

A move or rename is represented by a Change where the before revision and the after revision have different file paths.
A custom file status can be specified for a
[Change](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/changes/Change.java)
if it represents a non-standard modification of the file (for example, a file which has been merged with conflicts).
If a custom file status has not been specified, the status is calculated automatically from the change type.

### ChangeList

A
[ChangeList](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangeList.java)
represents a named group of related changes.
There are two main kinds of changelists:

*  [LocalChangeList](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/LocalChangeList.java) represents a group of modifications done by a user locally.
   If the VCS also supports the concept of changelists (like Perforce does), the VCS plugin can synchronize IDEA's local changelist structure with that of the VCS.
   Otherwise, a local changelist is simply a subset of the files checked out or modified by the user.

*  [CommittedChangeList](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/versionBrowser/CommittedChangeList.java)
   represents a set of modifications checked in to the VCS repository.
   For VCSes which support atomic commit, every committed revision is represented by a CommittedChangeList.
   For VCSes which use per-file commit (like CVS), the plugin can use heuristics to group a sequence of individual file commits into a
   [CommittedChangeList](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/versionBrowser/CommittedChangeList.java)

> **Note** The *Unversioned Files*, *Locally Deleted Files* etc. nodes in the *Changes* view are not actually changelists, and files under those nodes are not represented by Change objects.

## Plugin Components

This section describes the different components which comprise a VCS integration plugin, roughly in the same order as they should be implemented.

### AbstractVcs

This is the main entry point for a VCS plugin, which is used by the IntelliJ Platform to retrieve all other services provided by the plugin.
The recommended way to register an AbstractVcs implementation is to add an extension declaration to plugin.xml, as shown in the following example:

```xml
<idea-plugin>
  ...
  <extensions defaultExtensionNs="com.intellij">
    <vcs name="svn" vcsClass="org.jetbrains.idea.svn.SvnVcs"/>
  </extensions>
</idea-plugin>
```

Here `name` is the unique name of the VCS (this must match the string returned by your implementation of `AbstractVcs.getName()`), and *vcsClass* is your implementation class.

### ChangeProvider

This component is responsible for tracking user changes to the working copy, and reporting these changes to the IntelliJ Platform core.
An implementation of this class is returned from
[AbstractVcs.getChangeProvider()](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/AbstractVcs.java).

The ChangeProvider works in tandem with
[VcsDirtyScopeManager](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/VcsDirtyScopeManager.java),
which is a component in IntelliJ Platform core.
[VcsDirtyScopeManager](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/VcsDirtyScopeManager.java)
keeps track of the 'dirty scope' - the set of files for which the VCS file status may be out of date.
Files are added to the dirty scope either when they are modified on disk, or when their VCS status is invalidated by an explicit call to
[VcsDirtyScopeManager.fileDirty()](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/VcsDirtyScopeManager.java)
or
[VcsDirtyScopeManager.dirDirtyRecursively()](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/VcsDirtyScopeManager.java).

After some files have been added to the dirty scope, the dirty scope is passed to
[ChangeProvider.getChanges()](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangeProvider.java),
along with a
[ChangelistBuilder](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangelistBuilder.java)
instance, which serves as a sink to which the
[ChangeProvider](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangeProvider.java)
feeds the data about the changed files.
This processing happens asynchronously in a background thread.

The
[ChangeProvider](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangeProvider.java)
can either iterate all files under the dirty scope using
[VcsDirtyScope.iterate()](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/VcsDirtyScope.java),
or retrieve information about its contents using the `getDirtyFiles()` and `getDirtyDirectoriesRecursively()` methods.
If it is possible to retrieve the information about the local changes from the VCS in batch, it's strongly preferable to use the second method, as it scales much better for large working copies.

The
[ChangeProvider](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangeProvider.java)
reports data to ChangelistBuilder using the following methods:

*  `processChange()` is called for files which have been checked out (or modified if the VCS doesn't use an explicit checkout model), scheduled for addition or deletion, moved or renamed.

*  `processUnversionedFile()` is called for files which exist on disk, but are not managed by the VCS, not scheduled for addition, and not ignored through *.cvsignore* or a similar mechanism.

*  `processLocallyDeletedFile()` is called for files which exist in the VCS repository, but do not exist on disk and are not scheduled for deletion.

*  `processIgnoredFile()` is called for files which are not managed by the VCS but are ignored through *.cvsignore* or a similar mechanism.

*  `processSwitchedFile()` is called for files or directories for which the working copy corresponds to a different branch compared to the working copy of their parent directory.
   This can be called for the same files for which processSwitchedFile() has already been called.


