### 1.3.0 (July 20, 2016)

* Support for Unity and Mono development on macOS and Linux has been restored! This release brings back support for the Mono version of OmniSharp, which is used to provide *much* better support for .csproj/.sln projects. Please note that Mono version 4.0.1 or newer is required.
* Generation of tasks.json and launch.json files can now properly handle nested projects. [#170](https://github.com/OmniSharp/omnisharp-vscode/issues/170)
* New UI that makes it easy to select a process ID when attaching the debugger to another process. Note: If you have an existing launch.json file, you can re-generate it by deleting the file, closing your workspace in Visual Studio Code and opening it again. Or, you can open the launch.json file and change the `processId` value to `"${command.pickProcess}"`.
* Support for debugging in .cshtml files. To enable this, add a `sourceFileMap` entry to your launch.json with the following content: `"sourceFileMap": { "/Views": "${workspaceRoot}/Views" }`
* Support for conditional breakpoints
* New support for changing variable values in the debugger! To try this, just right-click on the variable name and select 'Set Value'. Note: To properly support this feature, we've changed the display of variable type names in the debugger to a shortened form. The full type name can be viewed by hovering over the name with the mouse.
* New configuration option to enable [stepping into properties and operators](https://github.com/OmniSharp/omnisharp-vscode/blob/release/debugger.md#stepping-into-properties-and-operators).
* Duplicate warnings and errors should no longer accumulate in Unity projects [#447](https://github.com/OmniSharp/omnisharp-vscode/issues/447)

### 1.2.0 (June 29, 2016)

* Adds debugger support for new Linux versions: Ubuntu 16.04, Fedora 23, openSUSE 13.2, and Oracle Linux 7.1
* Enhanced debug console output: module loads are now output, and there are launch.json options for controlling what is output
* Source file checksum support for breakpoints. This ensures that the debugger only sets breakpoints in code that exactly matches the open document.
* Support for editing the value of variables in the watch and locals window (requires VS Code 1.3)