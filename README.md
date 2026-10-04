# C Speed

C Speed is an early language project aimed at high-performance data analysis, AI, rocket and aerospace calculations, scientific computing, and graphics-heavy games.

[Open the beginner's guide](./LEARN.md) for runnable syntax examples and an explanation of what the current prototype supports versus what is still planned.

## Download

Download the latest Windows build from the project's [GitHub Releases](https://github.com/OWNER/REPOSITORY/releases/latest) page. On the Releases page, open the latest release and download the C Speed `.exe` asset.

> Replace `OWNER/REPOSITORY` in this link with the GitHub account and repository name when the project is published.

The current framework-dependent build requires the .NET 8 Windows Desktop Runtime. After downloading, open PowerShell in the folder containing `c-speed.exe` and run:

```powershell
.\c-speed.exe --install-command
```

Open a new terminal afterward, `cd` to the folder containing your C Speed program, and run `cspeed main.csp`.

## Run a C Speed program in its own window

The prototype understands page text, named color classes, numeric variables, and math expressions. Running a `.csp` file opens a standalone Windows app window with the C Speed logo and the output written by the program. It does not create an HTML file unless you explicitly request HTML export.

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

## Syntax supported by the prototype

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

The prototype supports CSS hexadecimal colors in `#RGB`, `#RGBA`, `#RRGGBB`, or `#RRGGBBAA` form; `f64` numeric variables; `+`, `-`, `*`, `/`, `%`, and `**`; parentheses; and math functions such as `math.sqrt`, `math.sin`, `math.cos`, `math.abs`, `math.log`, `math.min`, and `math.max`. Constants `math.pi` and `math.e` are available. `let mut` and `set` support changing a variable. Strings support `\"`, `\\`, `\n`, `\r`, and `\t`. Unknown statements, invalid colors, division by zero, and uses of undefined names produce errors.

## How the language prototype works

Open `compiler\Cspeed\Program.cs` while following these stages:

1. **Read:** The command-line entry point loads the `.csp` file.
2. **Recognize statements:** `PageCompiler` checks declarations such as `let`, `set`, `class`, and `for page include`.
3. **Read math:** `NumericExpression` splits a calculation into tokens, parses operator precedence (so `3 + 4 * 2` means `3 + (4 * 2)`), and evaluates it. The `**` operator is right-associative.
4. **Check and execute:** The prototype checks variable/class names and evaluates math functions and arithmetic.
5. **Display output:** The app opens a separate C Speed window with the logo and program output. HTML export is optional.

This first tool is an **interpreter prototype**, not the eventual optimizing native compiler. Its math support is currently limited to `f64` expressions; it does not yet support user-defined functions, general loops, ownership checks, AI libraries, or GPU code. We will add language features in small steps and later replace the prototype backend with native code generation.

### A first learning exercise

Change `x` and `y` in `examples\hello.csp`, then run the program again to see the updated result in its C Speed window. If you make a typo in a variable name, the interpreter reports an error.

## Tests

Run the smoke tests after changing the interpreter:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\compiler\tests\SmokeTests.ps1
```

## Project files

- `C-SPEED-SPEC.md` — current design goals and proposed language syntax.
- `compiler\Cspeed\` — starter C Speed interpreter and native output window.
- `examples\hello.csp` — first program in the language.
