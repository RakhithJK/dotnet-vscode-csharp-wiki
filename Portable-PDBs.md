## Summary
.NET Core introduces a new symbol file (PDB) format - portable PDBs. Unlike traditional PDBs which are Windows-only, portable PDBs can be created and read on all platforms. The new .NET Core debugger for Visual Studio Code only supports this new portable format. Portable PDBs can be generated both from [C# VS projects (.csproj)](#csproj) and the new [.NET CLI-based project.json projects](#net-cli-projects-projectjson).

More information about portable PDBs can be found on the [.NET team's GitHub page](https://github.com/dotnet/core/blob/master/Documentation/diagnostics/portable_pdb.md).

## How to Generate Portable PDBs
### .csproj
If you are using a .csproj to compile your C# code, you need to upgrade to Visual Studio 2015 Update 1 or newer, and modify the 'DebugType' property in the .csproj file to –

        <DebugType>portable</DebugType>

**NOTE**: For legacy reasons, the C# compiler option (and hence the name of the msbuild/project.json flags) to generate Windows PDBs is 'full'. However, this should NOT imply that Windows-only PDBs have more information than Portable PDBs. 

###.NET CLI projects (project.json)
The following option can be used in project.json to force the use of portable PDBs. This is currently not necessary when building on OSX/Linux, but is on Windows. In the future we expect to unite all the platforms, but we aren't there quite yet --

    "buildOptions": {
        "debugType": "portable"
    },
