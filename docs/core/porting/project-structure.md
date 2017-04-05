---
title: Organizing Your Project to Support .NET Framework and .NET Core
description: Organizing Your Project to Support .NET Framework and .NET Core
keywords: .NET, .NET Core
author: conniey
ms.author: mairaw
ms.date: 03/21/2017
ms.topic: article
ms.prod: .net-core
ms.devlang: dotnet
ms.assetid: 3af62252-1dfa-4336-8d2f-5cfdb57d7724
---

# Organizing Your Project to Support .NET Framework and .NET Core

This article is to help project owners who want to compile their solution against .NET Framework and .NET Core side-by-side.  It provides several options to organize projects to help developers achieve this goal.  Here are some typical scenarios to consider when you are deciding how to setup your project layout with .NET Core.  They may not cover everything you want; prioritize based on your project's needs.

* [**Combine existing projects and .NET Core projects into single projects**][option-csproj]

  *What this is good for:*
  * Simplifying your build process by compiling a single project rather than compiling multiple projects, each targeting a different .NET Framework version or platform.
  * Simplifying source file management for multi-targeted projects because you have to manage a single project file.  When adding/removing source files, the alternatives require you to manually sync these with your other projects.
  * Easily generating a NuGet package for consumption.
  * Allows you to write code for a specific .NET Framework version in your libraries through the use of compiler directives.

  *Unsupported scenarios:*
  * Does not allow developers without Visual Studio 2017 to open existing projects. To support older versions of Visual Studio, [keeping your project files in different folders](#support-vs) is a better option.

* <a name="support-vs"></a>[**Keep existing projects and new .NET Core projects separate**][option-csproj-folder]

  *What this is good for:*
  * Continuing to support development on existing projects without having to upgrade for developers/contributors who may not have Visual Studio 2017.
  * Decreasing the possibility in creating new bugs in existing projects because no code churn is required in those projects.

## Example

Consider the repository below:

![Existing project][example-initial-project]

[**Source Code**][example-initial-project-code]

There are several different ways to add support for .NET Core for this repository depending on the constraints and complexity of existing projects which are described below.

## Replace Existing Projects with a Multi-targeted .NET Core Project

The repository can be reorganized so that any existing `*.csproj` files are removed and a single `*.csproj` file is created that targets multiple frameworks.  This is a great option because a single project is able to compile for different frameworks.  It also has the power to handle different compilation options, dependencies, etc. per targeted framework.

![Create an csproj that targets multiple frameworks][example-csproj]

[**Source Code**][example-csproj-code]

Changes to note are:
* Replacement of `packages.config` and `*.csproj` with new .NET Core `*.csproj`
* Changes in the [Car's project.json][example-csproj-projectjson] and its [test project][example-csproj-projectjson-test] to support building for the existing .NET Framework as well as others

## Keep Existing Projects and Create a .NET Core Project

If there are existing projects that target older frameworks, you may want to leave these projects untouched and use a .NET Core project to target future frameworks.

![.NET Core project with existing PCL in different folder][example-csproj-different-folder]

[**Source Code**][example-csproj-different-code]

Changes to note are:
* The .NET Core and existing projects are kept in separate folders.
    * This avoids the package restore issue that was mentioned above due to multiple project.json/package.config files being in the same folder.
    * Keeping projects in separate folders avoids forcing you to have Visual Studio 2015 (due to project.json).  You can create a separate solution that only opens the old projects.

## See Also

Please see [.NET Core porting documentation][porting-doc] for more guidance on moving to project.json and csproj.

[porting-doc]: index.md
[example-initial-project]: media/project-structure/project.png "Existing project"
[example-initial-project-code]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library/

[example-csproj]: media/project-structure/project.csproj.png "Create an csproj that targets multiple frameworks"
[example-csproj-code]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library-csproj/
[example-csproj-projectjson]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library-csproj/src/Car/project.json
[example-csproj-projectjson-test]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library-csproj/tests/Car.Tests/project.json

[example-csproj-different-folder]: media/project-structure/project.csproj.different.png ".NET Core project with existing PCL in different folder"
[example-csproj-different-code]: https://github.com/dotnet/docs/tree/master/samples/framework/libraries/migrate-library-csproj-keep-existing/

[option-csproj]: #replace-existing-projects-with-a-multi-targeted-net-core-project
[option-csproj-folder]: #keep-existing-projects-and-create-a-net-core-project

[how-to-multitarget]: ../tutorials/libraries.md#how-to-multitarget
