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

If you are using a build before ~2600, 'buildOptions' was called 'compilationOptions'.

### Downloading a .NET CLI which supports 'debugType' option
Support for the 'debugType' option is project.json is newer than the build referenced from http://dotnet.github.io/getting-started as of 3/8/2016 (getting-started references build #1598). On Windows, in our testing, https://dotnetcli.blob.core.windows.net/dotnet/beta/Installers/1.0.0.001661/dotnet-win-x64.1.0.0.001661.exe has worked.

##### Unblocking the installer
The CI builds of the .NET CLI are not signed, which can cause Windows Defender or other antivirus software to try and prevent you from running it. For example, Microsoft edge will put up this dialog:

![dotnet-win-x64.1.0.0.001661.exe is not commonly downloaded and could harm your computer.](https://raw.githubusercontent.com/wiki/OmniSharp/omnisharp-vscode/images/edge-blocks-installer.jpg)

To unblock it:

1. Open the folder where the installer was downloaded to (ex: c:\users\me\downloads)
2. Right click on the file and bring up properties.
3. Click the 'Unblock' checkbox

![Unblock button](https://raw.githubusercontent.com/wiki/OmniSharp/omnisharp-vscode/images/unblock-windows-program.jpg)
