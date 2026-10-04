# C Speed 

C Speed is an early language project aimed at high-performance data analysis, AI, rocket and aerospace calculations, scientific computing, and graphics-heavy games. Version 2.00 expands the interpreter with source imports, functions, booleans, comparisons, conditionals, and loops.

[Open the beginner's guide](./LEARN.md) for runnable syntax examples and an explanation of what the current prototype supports versus what is still planned.

## C Speed IDLE editor

The `Cspeed idle` folder contains a small Windows code editor for writing `.csp` programs. It includes syntax coloring, line numbers, file open/save, and a Run button (or **F5**). Running a file saves it first and launches that program with the C Speed interpreter; the program output appears in its normal C Speed window.

To open the standalone Windows x64 editor, double-click [`Cspeed idle\C Speed IDLE.exe`](./Cspeed%20idle/C%20Speed%20IDLE.exe). It includes the .NET runtime and does not need a separate .NET installation. To build it again, follow the instructions in [`Cspeed idle\README.md`](./Cspeed%20idle/README.md).

To build and open the editor from the repository folder:

```powershell
dotnet run --project ".\Cspeed idle\CspeedIdle.csproj"
```

Open an existing source file from the editor's File menu, or pass a file path when launching:

```powershell
dotnet run --project ".\Cspeed idle\CspeedIdle.csproj" -- ".\examples\control-flow.csp"
```

The editor uses the C Speed compiler project from this repository when available. When launched separately, it looks for the per-user C Speed installation or the `c-speed` command on PATH. Install the interpreter command with `c-speed.exe --install-command` if it cannot be found.

## Download

Download the latest Windows build from the project's [GitHub Releases](https://github.com/aidaan214/cspeed/releases/latest) page. On the Releases page, open the latest release and download the C Speed `.exe` asset.

> Replace `OWNER/REPOSITORY` in this link with the GitHub account and repository name when the project is published.

The current framework-dependent build requires the .NET 8 Windows Desktop Runtime. After downloading, open PowerShell in the folder containing `c-speed.exe` and run:

```powershell
.\c-speed.exe --install-command
```

Open a new terminal afterward, `cd` to the folder containing your C Speed program, and run `cspeed main.csp`.

## Run a C Speed program in its own window

The C Speed 2.00 interpreter understands page text, named color classes, `f64`/`bool` variables, math expressions, functions, control flow, loops, and relative `.csp` imports. Running a `.csp` file opens a standalone Windows app window showing the output written by the program. The C Speed icon identifies the app but is not added to your page. It does not create an HTML file unless you explicitly request HTML export.

1. Install the .NET 8 SDK to build and run from source. The published `.exe` needs the .NET 8 Windows Desktop Runtime.
2. Open PowerShell in the folder containing your `.csp` file:

   ```powershell
   cd "C:\path\to\my-cspeed-program"
   ```

3. Run your program:

   ```powershell
   cspeed main.csp
   ```

The command opens your program's output in a separate C Speed window. `c-speed main.csp` works too.

## Serve a local HTML/CSS/JS project over HTTPS

From the folder containing your website files, run:

```powershell
cspeed https server
```

The server listens at `https://localhost:8443/` and serves files from the current folder. Put an `index.html` there to use it as the home page. You can choose another port:

```powershell
cspeed https server 9443
```

This is a local development server: it binds only to `127.0.0.1` and cannot be used by other computers on your network. It generates a temporary, self-signed certificate each time it starts, so the browser will warn that the connection is not trusted. That warning is expected for this local-only development server; do not use it for public hosting or enter sensitive information. Press **Ctrl+C** in the terminal to stop it. Only static files are served; this does not add networking or web-server syntax to `.csp` programs.

## Install `cspeed` for your account

From the project folder, publish the Windows app:

```powershell
dotnet publish .\compiler\Cspeed\Cspeed.csproj -c Release -r win-x64 --self-contained false -p:PublishSingleFile=true
```

Install the `cspeed` and `c-speed` commands and associate `.csp` files with C Speed:

```powershell
.\compiler\Cspeed\bin\Release\net8.0-windows\win-x64\publish\c-speed.exe --install-command
```

This installs for your Windows account only and does not need administrator access. It copies C Speed into `%LOCALAPPDATA%\C-Speed`, adds that folder to your user PATH, and sets `.csp` files to display the C Speed icon and open in the C Speed window. Keep the installed folder in place. Open a **new terminal** after setup so Windows refreshes PATH for it.

You can also run straight from source, without installing the command, from the project folder:

```powershell
dotnet run --project .\compiler\Cspeed\Cspeed.csproj -- .\examples\hello.csp
```

To create an HTML export instead of opening the C Speed window:

```powershell
cspeed main.csp --html
cspeed main.csp --html output.html
```

## Syntax supported by C Speed 2.00

Run the C Speed 2.00 example that imports helper functions and uses a loop and conditional:

```powershell
cspeed .\examples\control-flow.csp
```

The `helpers.csp` module is beside the example file and is imported relative to it.

```text
class 'hello snippet'
    color #f245

for page include ("Hello!") = class 'hello snippet'
for page include ("This text is black by default.")

let x: f64 = 3.0
let y: f64 = 4.0
let distance: f64 = math.sqrt(x ** 2 + y ** 2)
for page include (distance)
```

The interpreter supports CSS hexadecimal colors in `#RGB`, `#RGBA`, `#RRGGBB`, or `#RRGGBBAA` form; `f64` and `bool` variables; `+`, `-`, `*`, `/`, `%`, and `**`; parentheses; and math functions such as `math.sqrt`, `math.sin`, `math.cos`, `math.abs`, `math.log`, `math.min`, and `math.max`. Constants `math.pi` and `math.e` are available. `let mut` and `set` support changing a variable. Strings support `\"`, `\\`, `\n`, `\r`, and `\t` for display. Unknown statements, invalid colors, division by zero, and uses of undefined names produce errors.

Version 2.00 also supports `bool` values (`true`/`false`), comparisons (`==`, `!=`, `<`, `<=`, `>`, `>=`), short-circuit boolean operators (`and`, `or`, `not`), user-defined functions with parameters and return values, `if`/`else if`/`else`, `while`, `for ... in range(...)`, `break`, `continue`, and local relative imports such as `import helpers.csp`. Imports can import nested modules; duplicate imports are loaded once and import cycles are rejected.

Example:

```text
import helpers.csp

function square(value: f64) -> f64:
    return value * value

let mut total: f64 = 0
for index in range(1, 4):
    set total = total + square(index)

if total == 14:
    for page include ("Total:")
    for page include (total)
else:
    for page include ("Check the calculation")
```

`range(end)`, `range(start, end)`, and `range(start, end, step)` use an exclusive end. Loops have a one-million-iteration safety cap; function calls have a recursion-depth cap. Function parameters and local variables support `f64` and `bool`. Imported paths are relative to the importing source file; absolute imports are not allowed.

The interpreter is still a learning implementation, not a native compiler. Strings remain display-only, arrays/collections are not implemented, `for each` is not supported, functions return one value, function local scopes cannot update caller variables, and there is no AI/GPU/graphics library or ownership checker.

## How the language prototype works

Open `compiler\Cspeed\CspeedInterpreter.cs` while following these stages:

1. **Read:** The command-line entry point loads the `.csp` file.
2. **Load modules:** `CspeedInterpreter` recursively resolves relative `.csp` imports and detects missing modules and import cycles.
3. **Parse statements:** It builds structured statements for declarations, functions, conditionals, loops, and output.
4. **Evaluate expressions:** A precedence-aware parser checks arithmetic, comparison, and boolean expressions. The `**` operator is right-associative.
5. **Execute:** The interpreter checks variable types and mutability, invokes user functions, runs bounded loops, and collects page output.
6. **Display output:** The app opens a separate C Speed window with the program output. HTML export is optional.

This is an **interpreter prototype**, not the eventual optimizing native compiler. It does not yet support arrays, string variables, `for each`, ownership checks, AI libraries, or GPU code. We will add language features in small steps and later replace the prototype backend with native code generation.

### A first learning exercise

Change `x` and `y` in `examples\hello.csp`, then run the program again to see the updated result in its C Speed window. If you make a typo in a variable name, the interpreter reports an error.

## Tests

Run the smoke tests after changing the interpreter:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\compiler\tests\SmokeTests.ps1
```

The smoke-test script also checks the local HTTPS static server.

## Project files

- `C-SPEED-SPEC.md` — current design goals and proposed language syntax.
- `compiler\Cspeed\` — starter C Speed interpreter and native output window.
- `examples\hello.csp` — first program in the language.
- `examples\control-flow.csp` and `examples\helpers.csp` — imports, functions, booleans, conditions, and loops.
  (offcial site)= https://cspeed.vercel.app/
