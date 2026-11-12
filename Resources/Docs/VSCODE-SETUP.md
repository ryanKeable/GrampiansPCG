# VS Code setup for Unreal C++

VS Code is for **editing and debugging** C++. Building stays in the Unreal editor (Compile button / Live Coding). Visual Studio Build Tools (MSVC) must still be installed as the compiler toolchain (see `.vsconfig`).

## Required extensions
- `ms-vscode.cpptools` (C/C++): IntelliSense and the `cppvsdbg` debugger. Recommended automatically by the workspace.
- No C# extension. `*.Build.cs` / `*.Target.cs` are edited without IntelliSense: the C# extension can't resolve `UnrealBuildTool` and shows false errors (e.g. `CS0518 System.Void`) or fails on the engine's .NET 10 `.csproj` files. If you installed C# / C# Dev Kit, disable it for this workspace.

## Open the workspace
1. Open `PGenAusLandscape.code-workspace` (File > Open Workspace from File). It lists the project folder and the engine folder `C:\Program Files\UE_5.8`.
2. IntelliSense reads `.vscode/c_cpp_properties.json`, which points at `.vscode/compileCommands_PGenAusLandscape.json`.

## Regenerate project files (new module, new files, fresh clone)
The `compileCommands_*` files and `.sln`/`.slnx` are generated and gitignored. Regenerate with either:
- Right-click `PGenAusLandscape.uproject` > Generate Visual Studio project files, or
- Run the "Generate Project Files" launch config in the workspace.

Then reload VS Code (or run "C/C++: Reset IntelliSense Database").

## Attach the debugger
1. Launch the Unreal editor (Development build is fine; DebugGame gives better locals).
2. In VS Code: Run and Debug > select **Attach to UnrealEditor** (defined in the workspace file) and start it.
3. Pick `UnrealEditor.exe`, set a breakpoint in project code under `Source/`, trigger it in the editor.

## Notes
- Paths in the workspace and `c_cpp_properties.json` are absolute to this machine's UE 5.8 and VS Build Tools install; edit them if those move.
