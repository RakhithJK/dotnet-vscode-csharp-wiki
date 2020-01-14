## Introduction

This page contains more information about the error:

> The .NET Core SDK cannot be located. .NET Core debugging will not be enabled. Make sure the .NET Core SDK is installed and is on the path.

What this error means is that this extension ran the command `dotnet` and `dotnet` was **NOT** found on the `PATH` within the extension's process.

If you don't have the .NET Core SDK installed, fixing this error is usually simple enough: visit https://dot.net/core-sdk-vscode to download the .NET Core SDK.

If you do have the .NET Core SDK installed, then this means that the directory containing `dotnet` (Linux and macOS) or `dotnet.exe` (Windows) is not on your `PATH`, at least in this extension's process. The rest of this page will provide advice on understanding why.

## Known issues

Before we get to a list of troubleshooting steps, lets first enumerate a few known reasons why this error happens:

1. If you just installed the .NET SDK --
   * If you had Visual Studio Code open at the time you installed the .NET SDK, and you haven't restarted it, you should do so.
   * On Windows, on some machines, environment variable changes don't immediately take effect. Restart your computer to see if that resolves this problem.
2. If the .NET SDK was installed through Linux snap - see [Linux snap instructions](#linux-snap-instructions)

## General troubleshooting steps on Linux/Mac

TODO

## General troubleshooting steps on Windows

The first step in troubleshooting this problem is to see if this problem also happens is a command prompt:

* Start a command prompt:
    * Hit `WinKey+R` to bring up the Windows run dialog
    * Type in `cmd.exe`
* When the command prompt starts, type in `where.exe dotnet`. 

TODO

## Special instructions

#### Linux snap instructions

TODO