The C# extension currently supports running and debugging a unit test via CodeLens annotations on test methods. Just click the 'run test' or 'debug test' links:

![CodeLens](https://raw.githubusercontent.com/wiki/OmniSharp/omnisharp-vscode/images/unit-test-codelens.png)

### Notes

* Because `dotnet test` will run the test code in a child process, it isn't possible to configure a "unit test debugging" configuration in launch.json
* There currently isn't a VS Code command to run the current test, though there is an [issue for this in the backlog](https://github.com/OmniSharp/omnisharp-vscode/issues/421).