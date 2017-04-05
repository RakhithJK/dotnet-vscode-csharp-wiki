## Summary
.NET Core introduces a new symbol file (PDB) format - portable PDBs. Unlike traditional PDBs which are Windows-only, portable PDBs can be created and read on all platforms. The new .NET Core debugger for Visual Studio Code only supports this new portable format. Portable PDBs can be generated both from [C# VS projects (.csproj)](#csproj) and [project.json projects](#net-cli-projects-projectjson).

More information about portable PDBs can be found on the [.NET team's GitHub page](https://github.com/dotnet/core/blob/master/Documentation/diagnostics/portable_pdb.md).

## How to Generate Portable PDBs
### .csproj 
With .NET Core projects .csproj's (the new type of project supported by VS 2017 or .NET CLI preview 3 or newer), Portable PDBs are already enabled by default. 

For other .csproj files such as portable class libraries (PCLs), portable PDBs can be explicitly enabled by modifying the 'DebugType' property in the .csproj file to –

        <DebugType>portable</DebugType>

**NOTE**: For legacy reasons, the C# compiler option (and hence the name of the msbuild/project.json flags) to generate Windows PDBs is 'full'. However, this should NOT imply that Windows-only PDBs have more information than Portable PDBs. 

### .NET CLI projects (project.json)
The following option can be used in project.json to force the use of portable PDBs. This is currently not necessary when building on OSX/Linux, but is on Windows --

    "buildOptions": {
        "debugType": "portable"
    },
