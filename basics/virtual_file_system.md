---
title: Virtual File System
---

The virtual file system (VFS) is a component of *IntelliJ Platform* that encapsulates most of its activity for working with files. It serves the following main purposes:

* Providing a universal API for working with files regardless of their actual location (on disk, in archive, on a HTTP server etc.)
* Tracking file modifications and providing both old and new versions of the file content when a modification is detected.
* Providing a possibility to associate additional persistent data with a file in the VFS.

In order to provide the last two features, the VFS manages a _persistent snapshot_ of some of the contents of the user's hard disk. The snapshot stores only those files which have been requested at least once through the VFS API, and is asynchronously updated to match the changes happening on the disk.

The snapshot is application level, not project level - so, if some file (for example, a class in the JDK) is referenced by multiple projects, only one copy of its contents will be stored in the VFS.

All VFS access operations go through the snapshot. 

If some information is requested through the VFS APIs and is not available in the snapshot, it is loaded from disk and stored into the snapshot. If the information is available in the snapshot, the snapshot data is returned. The contents of files and the lists of files in directories are stored in the snapshot only if that specific information was accessed - otherwise, only file metadata like name, length, timestamp, attributes is stored.

> **Note** This means that the state of the file system and the file contents displayed in the IntelliJ Platform UI comes from the snapshot, which may not always match the actual contents of the disk. 
>
> For example, in some cases deleted files can still be visible in the UI for some time before the deletion is picked up by the IntelliJ Platform.

The snapshot is updated from disk during _refresh operations_, which generally happen asynchronously. All write operations made through the VFS are synchronous - i.e. the contents is saved to disk immediately.

A refresh operation synchronizes the state of a part of the VFS with the actual disk contents. Refresh operations are explicitly invoked by the *IntelliJ Platform* or plugin code - i.e. when a file is changed on disk while the IDE is running, the change will not be immediately picked up by the VFS. The VFS will be updated during the next refresh operation which includes the file in its scope.

*IntelliJ Platform* refreshes the entire project contents asynchronously on startup. By default, it performs a refresh operation when the user switches to it from another app, but users can turn this off via **Settings \| Appearance & Behavior \| System Settings \| Synchronize files on frame activation**.

On Windows, Mac and Linux, *IntelliJ Platform* starts a native file watcher process that receives file change notifications from the file system and reports them to *IntelliJ Platform*. If a file watcher is available, a refresh operation looks only at the files that have been reported as changed by the file watcher. If no file watcher is present, a refresh operation walks through all directories and files in the refresh scope.

Refresh operations are based on file timestamps. If the contents of a file was changed but its timestamp remained the same, *IntelliJ Platform* will not pick up the updated contents.

There is currently no facility for removing files from the snapshot. If a file was loaded there once, it remains there forever unless it was deleted from the disk and a refresh operation was called on one of its parent directories.

The VFS itself does not honor ignored files listed in **Settings \| File Types \| Files** and folders to ignore and excluded folders listed in **Project Structure \| Modules \| Sources \| Excluded**. If the application code accesses them, the VFS will load and return their contents. In most cases, the ignored files and excluded folders must be skipped from processing by higher level code.

During the lifetime of a running instance of an IntelliJ Platform IDE, multiple `VirtualFile` instances may correspond to the same disk file. They are equal, have the same `hashCode` and share the user data.

## Synchronous and asynchronous refreshes

From the point of view of the caller, refresh operations can be either synchronous or asynchronous. In fact, the refresh operations are executed according to their own threading policy, and the synchronous flag simply means that the calling thread will be blocked until the refresh operation (which will most likely run on a different thread) is completed.

Both synchronous and asynchronous refreshes can be initiated from any thread. If a refresh is initiated from a background thread, the calling thread must not hold a read action, because otherwise a deadlock would occur. See [IntelliJ Platform Architectural Overview](/basics/architectural_overview/general_threading_rules.md) for more details on the threading model and read/write actions.

The same threading requirements also apply to functions like [LocalFileSystem.refreshAndFindFileByPath()](upsource:///platform/platform-api/src/com/intellij/openapi/vfs/LocalFileSystem.java), which perform a partial refresh if the file with the specified path is not found in the snapshot.

In nearly all cases, using asynchronous refreshes is strongly preferred. If there is some code that needs to be executed after the refresh is complete, the code should be passed as a `postRunnable` parameter to one of the refresh methods:
 
* [RefreshQueue.createSession()](upsource:///platform/platform-api/src/com/intellij/openapi/vfs/newvfs/RefreshQueue.java)<!--#L36-->
* [VirtualFile.refresh()](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFile.java)<!--#L681-->
 
Synchronous refreshes can cause deadlocks in some cases, depending on which locks are held by the thread invoking the refresh operation.

## Virtual file system events

All changes happening in the virtual file system, either as a result of refresh operations or caused by user's actions, are reported as _virtual file system events_. VFS events are always fired in the event dispatch thread, and in a write action.

The most efficient way to listen to VFS events is to implement the `BulkFileListener` interface and to subscribe with it to the [VirtualFileManager.VFS_CHANGES](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFileManager.java)<!--#L34--> topic. 

This API gives you all the changes detected during the refresh operation in one list, and lets you process them in batch. Alternatively, you can implement the `VirtualFileListener` interface and register it using [VirtualFileManager.addVirtualFileListener()](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFileManager.java)<!--#L113-->. This will let you process the events one by one.

> **Note** VFS listeners are application level, and will receive events for changes happening in *all* the projects opened by the user. You may need to filter out events which aren't relevant to your task.

VFS events are sent both before and after each change, and you can access the old contents of the file in the before event. Note that events caused by a refresh are sent after the changes have already occurred on disk - so when you process the `beforeFileDeletion` event, for example, the file has already been deleted from disk. However, it is still present in the VFS snapshot, and you can access its last contents using the VFS API.

Note that a refresh operation fires events only for changes in files that have been loaded in the snapshot. For example, if you accessed a `VirtualFile` for a directory but never loaded its contents using [VirtualFile.getChildren()](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFile.java)<!--#L135-->, you may not get `fileCreated` notifications when files are created in that directory.

If you loaded only a single file in a directory using `VirtualFile.findChild()`, you will get notifications for changes to that file, but you may not get created/deleted notifications for other files in the same directory.
