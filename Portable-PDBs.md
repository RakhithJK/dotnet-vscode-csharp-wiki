## Summary
.NET Core introduces a new symbol file (PDB) format - portable PDBs. Unlike traditional PDBs which are Windows-only, portable PDBs can be created and read on all platforms. The new .NET Core debugger for Visual Studio Code only supports this new portable format. Portable PDBs can be generated both from [C# VS projects (.csproj)](#csproj) and the new [.NET CLI-based project.json projects](#net-cli-projects-projectjson).

## How to Generate Portable PDBs
### .csproj
If you are using a .csproj to compile your C# code, you need to upgrade to Visual Studio 2015 Update 1 or newer, and modify the 'DebugType' property in the .csproj file to –

        <DebugType>portable</DebugType>

**NOTE**: For legacy reasons, the C# compiler option (and hence the name of the msbuild/project.json flags) to generate Windows PDBs is 'full'. However, this should NOT imply that Windows-only PDBs have more information than Portable PDBs. 

###.NET CLI projects (project.json)
The following option can be used in project.json to force the use of portable PDBs. This is currently not necessary when building on OSX/Linux, but is on Windows. In the future we expect to unite all the platforms, but we aren't there quite yet --

    "compilationOptions": {
        "debugType": "portable"
    },

### Downloading a .NET CLI which supports 'debugType' option
Support for the 'debugType' option is project.json is very new. Newer than the build referenced from http://dotnet.github.io/getting-started as of 3/8/2016 (getting-started references build #1598). On Windows, in our testing, https://dotnetcli.blob.core.windows.net/dotnet/beta/Installers/1.0.0.001661/dotnet-win-x64.1.0.0.001661.exe has worked.

##### Unblocking the installer
The CI builds of the .NET CLI are not signed, which can cause Windows Defender or other antivirus software to try and prevent you from running it. For example, Microsoft edge will put up this dialog:

![dotnet-win-x64.1.0.0.001661.exe is not commonly downloaded and could harm your computer.](https://raw.githubusercontent.com/wiki/OmniSharp/omnisharp-vscode/images/edge-blocks-installer.jpg)

To unblock it:

1. Open the folder where the installer was downloaded to (ex: c:\users\me\downloads)
2. Right click on the file and bring up properties.
3. Click the 'Unblock' checkbox

![Unblock button](https://raw.githubusercontent.com/wiki/OmniSharp/omnisharp-vscode/images/unblock-windows-program.jpg)
  
## Supported scenarios
Today, neither portable PDBs nor Windows PDBs are supported everywhere. So you need to consider where your project will want to be used (or at least debugged) to decide which format to use.
Windows PDBs can only be written on read on Windows. All Windows tooling supports them, except for Visual Studio Code (as Visual Studio Code strives for consistent behavior across all platforms), and scenarios where Visual Studio is debugging to a remote Linux/OSX computer (as the PDBs must be read on the remote computer).
Portable PDBs can be read on any operating system, but there are a number of places where they aren't supported yet. Here are a few –

* Older version of the Visual Studio debugger (versions before VS 2015 Update 2)
* Edit-and-continue in Visual Studio
* Code inside the .NET Framework that prints stack traces with mappings back to line numbers (such as in an ASP.NET error page)
* C# Code analysis (aka FxCop)
* Symbol server (ex: SymbolsSource.org)
* Profiling tools
* Running any post-compilation build step that consumes or modifies the PDB, such as CCI based tools (CodeContracts) or the .Net Native compiler
* Using .Net decompilers such as ildasm or .Net reflector and expecting to see source line mappings or local parameter names

If you have a project that you want to be able to use and debug in both formats, you can use different build configurations and build the project twice to have it both ways.

## What is a PDB?
For anyone not familiar, a PDB file is an auxiliary file produced by a compiler to provide other tools, especially debuggers, information about what is in the main executable file and how it was produced. For example, a PDB is how a debugger can map foo.cs line 12 to the right executable location so that it can set a breakpoint, and how it maps back from some other executable location to its associated source line.
The Windows PDB format has been around a long time now (~25 years), and it evolved from other native debugging symbol formats which were even older. It started out its life as a format for native (C/C++) programs. For the first release of the .NET Framework, the Windows PDB format was extended to support .NET.
While the Windows PDB format has worked okay over the years, with .NET Core the Roslyn decided it was time to go back to the drawing board and come up with a new format. A few of the reasons:

* The Windows PDB format is complex, and not terribly well documented. This complexity is important for some of the native code scenarios that the format was designed for, but it isn't so important for the .NET scenarios. The portable format is [open source](https://github.com/dotnet/roslyn/tree/master/src/Debugging/Microsoft.DiaSymReader.PortablePdb) and [documented](https://github.com/dotnet/corefx/blob/master/src/System.Reflection.Metadata/specs/PortablePdb-Metadata.md).
* At the time the effort was begun at least, there wasn't a cross-platform reader for the lower level format. So a new one would need to be created.
* The Windows PDB format is not a particularly compact representation for managed code. The result is that significant size reductions can be obtained with a new format.