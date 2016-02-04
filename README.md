# Orleans.Activities

![Orleans logo](https://raw.githubusercontent.com/OrleansContrib/Orleans.Activities/master/src/Orleans.Activities.png)

Workflow Foundation (.Net 4.x System.Activities workflows) over Orleans framework to provide stable, long-running, extremely scalable processes with XAML designer support.

__NOTE:__ This project currently is an __experiment__, not production quality! There is no NuGet package for it.

[![Build status](https://ci.appveyor.com/api/projects/status/hm24xccsyc4e5nh1?svg=true)](https://ci.appveyor.com/project/lmagyar/orleans-activities)
~~NuGet~~
[![Issue Stats](http://www.issuestats.com/github/OrleansContrib/Orleans.Activities/badge/pr)](http://www.issuestats.com/github/OrleansContrib/Orleans.Activities)
[![Issue Stats](http://www.issuestats.com/github/OrleansContrib/Orleans.Activities/badge/issue)](http://www.issuestats.com/github/OrleansContrib/Orleans.Activities)

[![Help Wanted (filtered view)](https://badge.waffle.io/OrleansContrib/Orleans.Activities.svg?label=Status-Help%20Wanted&title=Help%20Wanted%20%28filtered%20view%29)](http://waffle.io/OrleansContrib/Orleans.Activities?label=Status-Help%20Wanted)
[![Up for Grabs (filtered view)](https://badge.waffle.io/OrleansContrib/Orleans.Activities.svg?label=Status-Up%20for%20Grabs&title=Up%20for%20Grabs%20%28filtered%20view%29)](http://waffle.io/OrleansContrib/Orleans.Activities?label=Status-Up%20for%20Grabs)
[![Ready](https://badge.waffle.io/OrleansContrib/Orleans.Activities.svg?label=Phase-Ready&title=Ready)](http://waffle.io/OrleansContrib/Orleans.Activities)
[![In Progress](https://badge.waffle.io/OrleansContrib/Orleans.Activities.svg?label=Phase-In%20Progress&title=In%20Progress)](http://waffle.io/OrleansContrib/Orleans.Activities)

~~Documentation~~ (see [HelloWorld](https://github.com/OrleansContrib/Orleans.Activities/blob/master/docs/HelloWorld/HelloWorld.md) sample)

[Coding Guidelines](https://github.com/dotnet/corefx/blob/master/Documentation/coding-guidelines/coding-style.md)

[Design Guidelines](https://github.com/dotnet/corefx/blob/master/Documentation/coding-guidelines/framework-design-guidelines-digest.md)

This project is licensed under the [Apache License](https://github.com/OrleansContrib/Orleans.Activities/blob/master/LICENSE).

## Concept

![Overview](https://raw.githubusercontent.com/OrleansContrib/Orleans.Activities/master/docs/Orleans.Activities-Overview.png)

This is a very high level view:

* Each WorkflowGrain is indistinguishable from a normal grain and backed by a WorkflowHost.
* The WorkflowHost is responsible to handle the lifecycle of the WorkflowInstance, mainly recreate it from a previous persisted state when it aborts.
* The communication between the WorkflowGrain and the WorkflowHost is based on 2 developer defined interfaces for the incoming and outgoing requests (TWorkflowInterface and TWorkflowCallbackInterface). These interfaces' methods can be referenced from the workflow activities to accept incoming or to initiate outgoing requests.
* The methods of the TWorkflowInterface and TWorkflowCallbackInterface are independent from the grain's external public interface, you can merge different public requests into one method or vice versa. Or a reentrant grain even can execute (read-only) public interface methods independently from the current running workflow operations.
* The method's signatures are restricted, their parameters and return values are lazy, async delegates with 1 optional parameter/return value. The delegates executed by the workflow activities if/when they accept them (command pattern).
* There are design-, build- and static-run-time checks to keep the interfaces and the workflows in sync.

A typical workflow grain manages operations in other grain(s) and handles only the process specific data in it's own state.

The goal, is to keep the C# code in the grain, and use the workflow only to decide what to do next. This way we can avoid a steep learning curve to use workflows: the developer doesn't need to write or to understand anything about activities, he/she can build workflows with the provided activities in a designer.

## Functionality

Implemented:

* Persistence (compatible with legacy workflow extensions), but it can run without any persistence
* Reminders (redirected from System.Activities Timers, 1 min. is the minimum)
* Tracking
* Designer support

Extra implemented features:

* TAP async API
* Optionally idempotent request processing for forward recovery
* Automatic reactivation after failure
* Workflow can be persisted during processing an incoming request (ReceiveRequestSendResponseScope is __NOT__ an implicit NoPersistScope)
* Executing code "in the background" on the tail of the request after the request returns it's response
* Workflow is informed whether it is running in a reloaded state after failure (to determine necessary recovery)
* Notification participant (to notify extensions when the workflow is idle)

Not tested, but should work:

* CancellationScope
* CompensableActivity and CompensationExtension

Under construction:

* Tests (currently semi manual, semi automatic MSTest, don't even look at them)
* More elaborate sample with
  * DI/Autofac
  * Strategy and Humble Object patterns, to show an architecture, where the application logic can be tested independently from Orleans and from Orleans.Activities workflows

Not implemented, help wanted (for design and for implementation):

* DynamicUpdateMap support (updating loaded workflows to a newer definition version), though the separation of the application logic (the plain C# delegates) and the process (the diagram) results in a very simple workflow diagram, that has a big chance you won't need to update when it runs
* TransactionScope activity support (see https://github.com/dotnet/orleans/issues/1090)
* See all [Help Wanted issues](http://waffle.io/OrleansContrib/Orleans.Activities?label=Status-Help%20Wanted) (filtered view)

And there are nearly unlimited [Open issues](http://waffle.io/OrleansContrib/Orleans.Activities)...

## Samples

[HelloWorld](https://github.com/OrleansContrib/Orleans.Activities/blob/master/docs/HelloWorld/HelloWorld.md)

## Details

This is still an overview, all the details of the classes are hidden. The goal is to give a map to understand the relations between the classes. See the comments in the source!

![Overview](https://raw.githubusercontent.com/OrleansContrib/Orleans.Activities/master/docs/Orleans.Activities-Details.png)