# Learn C Speed

Welcome! This guide teaches you how to write and run programs using the **current C Speed prototype**.

> **Important:** C Speed is still being built. The current program runner can display text and the C Speed logo in its own Windows window, apply named text colors, store and change numeric variables, and calculate `f64` math expressions. It is an interpreter prototype, not yet the planned high-performance native compiler. The language examples about files, AI, graphics, and native speed later in this guide describe ideas to build toward; they are **not executable in this version**.

## 1. Create and run your first program

Create a folder for your project, for example `C:\MyCspeed`. Inside it, create a file named `main.csp`.

Put this in `main.csp`:

```text
for page include ("Hello from C Speed!")
```

Open PowerShell, change to your folder, and run the program:

```powershell
cd "C:\MyCspeed"
cspeed main.csp
```

This opens a C Speed window, displays the program's output with the C Speed logo, and exits when you close the window. You can also double-click a `.csp` file if Windows file associations have been set up.

If the `cspeed` command is not recognized, open a **new** terminal after installing C Speed. You can run using the full path to `c-speed.exe`, or run from the project source with the .NET SDK:

```powershell
dotnet run --project "C:\path\to\islam c\compiler\Cspeed\Cspeed.csproj" -- "C:\MyCspeed\main.csp"
```

The `.csp` file is the source code you write. The runner reads and interprets the supported C Speed statements.

## 2. Show text and choose its color

Text is black by default:

```text
for page include ("This text is black.")
```

To define a reusable text color, make a class and assign it to an output statement:

```text
class 'greeting'
    color #f245

for page include ("Hello in color!") = class 'greeting'
for page include ("This second line is black.")
```

- `class 'greeting'` defines a named text style.
- The next indented line gives that style its color.
- `for page include ("...")` displays text.
- `= class 'greeting'` applies the named style to that text.

Accepted CSS hexadecimal color lengths are `#RGB`, `#RGBA`, `#RRGGBB`, and `#RRGGBBAA`. For example, `#f00` is short red, `#f008` is short red with alpha, and `#ff0000` is red. Alpha is transparency, so some colors may appear faint. Names in quotes must match exactly.

The class is validated after the whole source file has been read, so a class can be declared before or after its use. A class name can only be defined once in a program.

## 3. Store numbers in variables

Use `let` to create a number:

```text
let speed: f64 = 300.0
let time: f64 = 2.0
let distance: f64 = speed * time
for page include (distance)
```

This calculates `600` and displays it. `f64` means a 64-bit floating-point number, which can represent integers and decimal numbers.

The `: f64` type can be omitted because this prototype currently supports only `f64` numeric variables:

```text
let distance = 300.0 * 2.0
for page include (distance)
```

Variable names start with a letter or underscore and can then use letters, numbers, or underscores. Names are case-sensitive. A variable must be defined before an expression uses it, and cannot be defined twice in one program.

Variables are immutable by default. Use `let mut` if a value needs to change, then update it using `set`:

```text
let mut total: f64 = 10.0
set total = total + 5.0
for page include (total)
```

Changing a regular `let` variable is an error. Variables currently store numeric values only; storing text or collections is not supported yet.

## 4. Do calculations

The prototype supports these operators:

| Operator | Meaning | Example |
|---|---|---|
| `+` | Add | `10 + 3` gives `13` |
| `-` | Subtract | `10 - 3` gives `7` |
| `*` | Multiply | `10 * 3` gives `30` |
| `/` | Divide | `10 / 4` gives `2.5` |
| `%` | Remainder | `10 % 4` gives `2` |
| `**` | Power | `2 ** 3` gives `8` |

Parentheses control grouping:

```text
let regular_order = 2 + 3 * 4
let grouped_order = (2 + 3) * 4
for page include (regular_order)
for page include (grouped_order)
```

The results are `14` and `20`: multiplication happens before addition. Powers are evaluated from right to left, so `2 ** 3 ** 2` means `2 ** (3 ** 2)`, or `512`. A leading `-` makes a number negative; for example, `-2 ** 2` evaluates to `-4`.

The prototype reports an error for division or remainder by zero. It does not yet provide integer types, checked integer arithmetic, or exact-decimal arithmetic.

## 5. Use the math library

Math functions use the `math.` prefix. This example calculates the distance from `(x, y)` to zero:

```text
let x: f64 = 3.0
let y: f64 = 4.0
let distance: f64 = math.sqrt(x ** 2 + y ** 2)
for page include (distance)
```

Supported one-argument functions:

```text
math.sqrt(value)
math.abs(value)
math.floor(value)
math.ceil(value)
math.round(value)
math.sin(value)
math.cos(value)
math.tan(value)
math.asin(value)
math.acos(value)
math.atan(value)
math.log(value)
math.log10(value)
math.exp(value)
```

Supported two-argument functions:

```text
math.min(first, second)
math.max(first, second)
math.pow(base, exponent)
```

Constants:

```text
let circle_area = math.pi * 5 ** 2
let e_value = math.e
for page include (circle_area)
```

Trigonometry uses **radians**, not degrees. `math.log` is the natural logarithm. `math.round` currently uses the .NET runtime's default midpoint behavior. The language's final precision and edge-case rules are not decided yet.

## 6. Put it together

This complete program displays a styled heading, performs a calculation, and displays the result:

```text
class 'result'
    color #05a

for page include ("Rocket travel-distance example") = class 'result'

let speed_meters_per_second: f64 = 300.0
let time_seconds: f64 = 2.0
let distance_meters: f64 = speed_meters_per_second * time_seconds

for page include ("Distance in meters:")
for page include (distance_meters) = class 'result'
```

Run it from the folder where `main.csp` is saved:

```powershell
cspeed main.csp
```

The window and color are just for displaying an example; this prototype is not a certified tool for real rocket or flight-control calculations. Safety-critical work requires qualified engineering, independent verification, and tested software.

## 7. Strings and comments

Text for the page goes between double quotes:

```text
for page include ("The answer is:")
```

Strings support these escapes:

- `\"` puts a quote inside the text.
- `\\` puts a backslash inside the text.
- `\n` starts a new line within the text.
- `\r` is a carriage return.
- `\t` inserts a tab.

Use `//` to make a whole-line comment:

```text
// Calculate the area of a circle.
let radius: f64 = 2.0
let area: f64 = math.pi * radius ** 2
for page include (area)
```

Comments on a line that already has code are not supported yet.

## 8. Use other files

### What works now

The current runner reads one `.csp` source file each time you start it:

```powershell
cspeed main.csp
```

You can put the `.csp` file in any folder you can access and give the runner its path:

```powershell
cspeed "C:\MyCspeed\main.csp"
```

Each `.csp` program is currently interpreted on its own. C Speed does **not** yet have `import`, module, or `include` statements for loading another source file, and its programs cannot yet read or write arbitrary data files.

### What file support might look like later

This is an illustration of a possible future API, **not valid syntax in the current prototype**:

```text
let data = file.read_text("input.csv")
let rows = csv.parse(data)
```

Supporting this safely means designing file handles, encodings, errors, permissions, structured formats such as CSV, and the syntax for sharing code between modules. We should add and document those features before using such examples in real `.csp` programs.

## 9. Make AI programs

### What works now

The current C Speed prototype **cannot create, train, or run an AI model yet**. It has no tensor/array library, machine-learning framework, GPU execution, network client, or bindings to AI libraries. The `f64` calculations are an early language feature, not an AI system.

Building AI support will take several pieces:

1. **Data support:** Efficient arrays or tensors, and safe ways to load datasets.
2. **Math kernels:** Fast, well-tested vector, matrix, and tensor operations.
3. **Model support:** Interfaces to existing machine-learning libraries or a C Speed AI library.
4. **Hardware support:** A way to run model operations on supported CPUs and GPUs.
5. **Training and inference APIs:** Tools for training models and using trained models to make predictions.

An eventual program might have an API like this, but the snippet below is **only a sketch for a future design**; it will not run today:

```text
// FUTURE EXAMPLE — not supported by the current C Speed prototype.
let training_data = data.read_csv("training-data.csv")
let model = ai.create_model("classifier")
model.train(training_data)
let prediction = model.predict(new_example)
for page include (prediction)
```

For now, you can use C Speed to experiment with its supported math and display features. You will need to wait for file access, arrays, and AI libraries before building AI applications entirely in C Speed.

## 10. What is not supported yet

The following examples appear in the wider design discussion, but are **not implemented in the runner yet**:

- User-defined `function` declarations and `return`.
- `if` / `else` decisions.
- `for each` and other loops.
- Arrays, collections, and string variables.
- Reading or writing data files from a program.
- Importing other `.csp` modules.
- Network requests, package libraries, and third-party code.
- AI, machine-learning, graphics, or GPU programming APIs.
- Native code generation and C++-comparable performance.

If you try unsupported syntax, C Speed will report an error rather than execute it. These features should be implemented one by one with tests and examples.

## 11. Optional HTML export

By default, running `cspeed main.csp` opens the C Speed desktop window and does not create an HTML file. To export a browser page instead:

```powershell
cspeed main.csp --html
```

This creates `main.html` in the same folder. To choose a different path:

```powershell
cspeed main.csp --html "C:\MyCspeed\result.html"
```

The HTML export includes the C Speed logo, and HTML-escapes text so page text is displayed safely.

## 12. Troubleshooting

**`cspeed` is not recognized:** Open a new terminal after installing C Speed. Check that `%LOCALAPPDATA%\C-Speed` is in your user PATH. You can also run the executable by its full path.

**The source file is not found:** Change directory to the folder containing it with `cd`, or pass the full path in quotes.

**A variable or class is unknown:** Check spelling, capitalization, and that each variable is defined before it is used. Make sure the exact class name appears in both the definition and the `= class '...'` assignment.

**The program shows an error:** Read the `.csp` line number in the error message. The current prototype accepts only the syntax described in this guide; future-design examples will fail until implemented.

**The app does not open:** The published app needs the .NET 8 Windows Desktop Runtime unless it is published as self-contained. Run from source with the .NET 8 SDK or install that runtime.

## 13. Learn how the interpreter works

The starter implementation is in `compiler\Cspeed\`:

1. The command-line entry point opens and reads your `.csp` file.
2. The page compiler recognizes statements and stores variables and style declarations.
3. The numeric-expression parser turns math text into tokens and applies operator precedence.
4. The interpreter validates references and calculates values.
5. The Windows app creates controls to show the logo and program output.

An interpreter reads and runs source instructions. A native compiler, which is a later C Speed goal, translates source into machine code before the program runs. A file named `c-speed.exe` is currently the **C Speed interpreter app**—it does not yet compile `.csp` programs to native code.

## Tests and more information

Run the prototype checks from the project folder:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\compiler\tests\SmokeTests.ps1
```

See [README.md](./README.md) for installation and run commands, and [C-SPEED-SPEC.md](./C-SPEED-SPEC.md) for the current design goals and ideas for the future language.

## 14. Read the sample program line by line

Open `examples\hello.csp`. Its contents are:

```text
class 'hello snippet'
    color #f245

for page include ("Hello from C Speed!") = class 'hello snippet'
for page include ("This is your first C Speed program.")

let x: f64 = 3.0
let y: f64 = 4.0
let distance: f64 = math.sqrt(x ** 2 + y ** 2)
for page include (distance)
```

Here is what each part does:

1. `class 'hello snippet'` starts a class declaration. In this prototype a class is a named **text-color style**, not a general programming class that contains functions and data.
2. `    color #f245` is indented to show it belongs to the declaration. The four spaces are for readability; the runner checks that this line is indented and has an accepted hexadecimal color.
3. The blank line separates declarations visually. Blank lines are ignored.
4. `for page include ("Hello from C Speed!")` creates a text item. The `= class 'hello snippet'` ending says to give that particular item the class's color.
5. The next text item has no class, so it uses the default black text.
6. `let x: f64 = 3.0` stores the number `3.0` in the variable named `x`. `: f64` declares its numeric type.
7. The next line makes a second variable, `y`.
8. `x ** 2` squares `x`; `y ** 2` squares `y`; `+` adds the squares; and `math.sqrt(...)` takes the square root. For `x = 3` and `y = 4`, this calculates `sqrt(9 + 16)`, which is `5`.
9. `for page include (distance)` displays the calculated number instead of quoted text.

When you run this file, the interpreter reads and validates it first. The C Speed window opens after processing the complete program; it contains the logo followed by the output items in the same order as their `for page include` statements.

## 15. Build your own example from scratch

Try this program by saving it as `circle.csp`:

```text
class 'answer'
    color #087

for page include ("Circle calculator") = class 'answer'

let radius: f64 = 3.0
let area: f64 = math.pi * radius ** 2
let circumference: f64 = 2 * math.pi * radius

for page include ("Area:")
for page include (area) = class 'answer'
for page include ("Circumference:")
for page include (circumference)
```

Run it by navigating to the folder containing `circle.csp`:

```powershell
cd "C:\MyCspeed"
cspeed circle.csp
```

The output is approximately `28.274333882308138` for the area and `18.84955592153876` for the circumference. Those long decimal results are normal for floating-point calculations. `f64` stores a binary approximation; many decimal fractions cannot be represented exactly.

To experiment:

- Change `radius` to `5.0` and run the program again.
- Change the color from `#087` to `#00aa77`.
- Try `math.round(area)` and compare the displayed rounded result.
- Try writing `area = 100` as a statement. The current language does not support that assignment syntax; mutable variables need `let mut` and `set`.
- Try changing `let radius` to `let mut radius`, then add `set radius = 4.0` on a later line.

## 16. Understand the grammar

The current prototype uses a very small, line-oriented grammar. The statements it understands are:

```text
class 'name'
    color #abc

let variable = numeric_expression
let variable: f64 = numeric_expression
let mut variable = numeric_expression
set variable = numeric_expression

for page include ("text")
for page include (numeric_expression)
for page include ("text") = class 'name'
for page include (numeric_expression) = class 'name'
```

The literal words `class`, `color`, `let`, `mut`, `set`, `for`, `page`, `include`, and `math` have special roles in these forms. Parentheses group the value passed to `include` or a math function. Double quotes identify page text; single quotes identify class names. A class definition has a following, indented color line.

### Whitespace and lines

- Each statement is written on its own line.
- Empty lines do nothing.
- A line whose first non-space characters are `//` is a comment and does nothing.
- Leading/trailing whitespace is removed before matching normal statements.
- The color line is the exception: it must include indentation after the class declaration.
- Statements are processed top to bottom. A numeric variable has to exist before the line that uses it.
- A style can be used before its declaration because the program checks class references after processing all lines.

### Names

A variable name must start with an ASCII letter or underscore (`A`-`Z`, `a`-`z`, `_`). The rest of the name can use letters, digits, and underscores. Thus `distance2` and `_count` are valid examples, while `2distance` and `my-distance` are not variable names.

Variable names are case-sensitive: `speed` and `Speed` are different names. A variable cannot be declared twice in one program. Class names can contain spaces and are written in single quotes; their spelling and case must match between the class definition and each assignment.

### Numeric type and number notation

All variables and math results in this prototype are .NET `double` values, which correspond to the common 64-bit floating-point idea represented by the proposed `f64` type. Writing `: f64` is permitted but optional. Writing a different explicit type such as `i32` causes a clear error because other types have not been implemented.

Numbers can be whole or decimal:

```text
let count = 12
let seconds = 1.25
let tiny = 2.5e-4
let large = 6.02E23
```

The last two examples use scientific notation: `2.5e-4` means `2.5 × 10⁻⁴`; `6.02E23` means `6.02 × 10²³`. Integers are still represented as floating-point values in this prototype, so this is not a precise integer type.

## 17. Math expressions in detail

The expression parser recognizes number literals, variable names, math function calls, `math.pi`, `math.e`, parentheses, commas, decimal points, and the supported arithmetic operators. Spaces inside a calculation do not matter.

These expressions are supported:

```text
10 + 3
(10 + 3) * 2
2 ** 8
math.pow(2, 8)
math.sqrt(16)
math.min(10, 20)
2.5e-3 * 4
```

### Operator order

The current parser uses these operator precedence levels:

1. Parenthesized expressions and function arguments are calculated first.
2. Power (`**`) is next and groups right to left.
3. Multiplication (`*`), division (`/`), and remainder (`%`) are next.
4. Addition (`+`) and subtraction (`-`) are last.

Unary `+` and `-` bind before multiplication but after power in the current parser. So:

```text
let first = -2 ** 2
let second = (-2) ** 2
for page include (first)
for page include (second)
```

produces `-4` and `4`. Parentheses are the best way to make a complicated formula's intent obvious.

### Function argument rules

All math functions need parentheses. `math.sqrt(9)` is valid; `math.sqrt 9` is not. `sqrt(9)` is not recognized either: use the `math.` prefix. `math.sqrt(x, y)` fails because `sqrt` expects one argument, while `math.min(x, y)` expects two. Arguments are expressions, so you can write:

```text
let smaller = math.min(10 + 2, 3 * 5)
```

### Special floating-point results

The runner explicitly reports division by zero and remainder by zero. Functions like `math.sqrt(-1)`, `math.log(0)`, and powers outside floating-point range may return `NaN` or infinity according to the underlying .NET math library. The final C Speed rules for these edge cases have not been decided. Never use this early prototype's result as the sole validation for a critical engineering calculation.

## 18. Strings and displaying values

Strings are currently **display-only**. They are not assigned to variables and cannot be concatenated, searched, or passed to general functions. Numbers can be assigned to variables and displayed as page output:

```text
let temperature = 23.5
for page include ("Temperature:")
for page include (temperature)
```

The runner converts numeric output using invariant formatting, so it uses a dot for the decimal separator regardless of the computer's regional settings.

To put a quote or backslash inside displayed text, escape it:

```text
for page include ("She said: \"hello\"")
for page include ("Use C:\\MyCspeed")
for page include ("First line\nSecond line")
```

Supported escapes are `\"`, `\\`, `\n`, `\r`, and `\t`. An unknown escape, such as `\q`, or a backslash at the end of a string is a syntax error. A line break directly inside the quoted string is not supported; use `\n`.

The desktop window displays each `for page include` as a label. The HTML export escapes special HTML characters like `<`, `>`, and `&`; for example, text containing `<script>` is displayed as text rather than executed as an HTML element. Keep untrusted text escaped if a future HTML feature is added.

## 19. Color details

The class's `color` line uses a **hex color**, not a color name:

```text
class 'red'
    color #f00

class 'teal'
    color #008080

class 'semi transparent'
    color #f008

for page include ("Red text") = class 'red'
for page include ("Teal text") = class 'teal'
```

Accepted lengths are:

- `#RGB`, three hexadecimal digits.
- `#RGBA`, four hexadecimal digits, with the last digit controlling alpha.
- `#RRGGBB`, six hexadecimal digits.
- `#RRGGBBAA`, eight hexadecimal digits, with the final pair controlling alpha.

Only digits `0`-`9` and letters `a`-`f` / `A`-`F` are accepted. For short colors, each digit is expanded by repeating it: `#f08` becomes `#ff0088`. The class style applies to individual output items, not to the logo or the whole program window.

## 20. Variables: common questions

### Why can't I just change a variable?

Values made with `let` are immutable. This avoids accidental updates. Mark a variable as mutable when it needs to change:

```text
let mut total = 0
set total = total + 5
set total = total + 2
for page include (total)
```

The result is `7`. `set` does not create a new variable: `total` must have been declared earlier using `let mut`. The current prototype only reassigns numeric values.

### Can I use variables before I create them?

No. Numeric statements run top to bottom:

```text
let a = b + 1
let b = 2
```

The first line fails because `b` does not yet exist. Declare `b` first.

### Can I use the same variable name twice?

No. A second `let` for the same name produces a “already defined” error. Use `set` for a mutable variable, or choose a different name.

### Do variables store the output of a math function?

Yes:

```text
let result = math.sqrt(81)
for page include (result)
```

The variable receives the numeric result (`9`).

## 21. Files and connecting programs

“Run a file” can mean two different things:

1. **Run a C Speed source file:** This works. Pass the path to the `.csp` file, either from its folder or as a full path.
2. **Read a data file from inside a C Speed program:** This does not work yet. The current language has no file-reading statement.

To run source from a different folder:

```powershell
cspeed "C:\MyCspeed\main.csp"
```

To work in that folder first:

```powershell
cd "C:\MyCspeed"
cspeed main.csp
```

Quotes around paths are important when folder names contain spaces. `cd` changes the terminal's current folder; it does not change or copy your `.csp` program.

The following are examples of **future syntax only**. They will fail in today's interpreter:

```text
let text = file.read_text("notes.txt")
let data = csv.read("measurements.csv")
let helper = import "helpers.csp"
```

Before adding file I/O, C Speed needs a carefully designed library for opening, reading, and closing files; handling path errors and permissions; selecting text encoding; and representing bytes, text, and records. For scientific datasets, we will also need efficient formats, validation, and clear errors when the data is malformed. Loading a file should not silently hide failures or unexpectedly consume excessive memory.

“Connect to different files” can also mean sharing code among source files. This is usually handled by modules or imports; a future module system needs rules for how imports are found, how duplicate names are handled, and how each file participates in compilation. There is no module/import support yet.

## 22. AI: what it takes and what you can learn now

An AI is not created just by adding a keyword or writing one math formula. An AI application commonly needs:

1. **A problem definition:** What input will it receive, and what should it predict or generate?
2. **Data:** Examples with correct results, permissions to use that data, and checks for missing or biased records.
3. **A representation:** Numbers organized into vectors, matrices, or tensors.
4. **A model:** A mathematical function with parameters to learn.
5. **A training algorithm:** A method to update those parameters to reduce error.
6. **Evaluation:** Tests on examples not used during training, to see whether the model generalizes.
7. **Deployment:** A safe way to load the model, accept inputs, and return predictions.
8. **Resources:** Enough memory and CPU/GPU compute for the model and its data.

The current C Speed prototype has none of the essential AI libraries yet: no arrays/tensors, datasets, file-reading API, random-number API, model/training tools, network support, or GPU kernels. Its ordinary math examples only calculate scalar `f64` values. **You cannot train or run a real AI entirely in current C Speed yet.**

### A math-only learning exercise, not AI

You can experiment with a simple score formula:

```text
let signal_a = 2.0
let signal_b = 3.0
let weighted_score = signal_a * 0.25 + signal_b * 0.75
for page include ("Weighted score:")
for page include (weighted_score)
```

This is a fixed arithmetic calculation, not a trained AI: its weights (`0.25` and `0.75`) never change, it has no data-driven learning, and it cannot make a general model. Calling a hand-written formula “AI” would be misleading.

### Possible future AI API

This is only a picture of a possible later design and **cannot run now**:

```text
// FUTURE DESIGN SKETCH — not supported today.
let records = data.load_csv("training.csv")
let model = ai.linear_model()
model.train(records.features, records.labels)
let prediction = model.predict(new_measurement)
for page include (prediction)
```

We will need to choose whether to implement machine-learning algorithms directly, connect C Speed to established native AI libraries, or support both. Existing libraries can speed up development, but interoperability, compatible licenses, platform support, tensor ownership, GPU support, and dependency installation all need careful work. A general AI framework will take significant work after the language has arrays, modules, file I/O, and a stable native interface.

## 23. Speed and native compilation

There are two separate things to understand:

- **The speed of programs written in a language** depends on its compiler, runtime, libraries, algorithms, and target hardware.
- **The speed of the compiler/interpreter itself** depends on how that tool is implemented.

The current runner reads a `.csp` source file at runtime, parses its supported statements, evaluates math on the .NET runtime, and draws the result in a Windows application. It does **not** turn `.csp` calculations into CPU machine instructions. A C++-like speed goal cannot be claimed for this version.

A future native compiler will need at least:

1. A formal language grammar and a parser.
2. A type checker for variable types and function calls.
3. A representation of the program that the compiler can analyze.
4. A native code-generation backend (for example, a carefully selected LLVM-based toolchain or another backend).
5. A linker and a way to package runtime/library dependencies.
6. Debug symbols, useful errors, and reproducible performance benchmarks.
7. Correctness tests comparing compiled program results to known answers.

Even after a native compiler exists, speed must be measured with representative programs on multiple machines. “As fast as C++” is a target to test—not something a language can promise for every program.

## 24. Common errors and how to fix them

The current executable is a Windows GUI app. During normal window mode, an error is shown in a Windows message box, including the `.csp` path and line number when available. The optional HTML-export/console modes write errors to the terminal instead.

| Error | Typical reason | What to do |
|---|---|---|
| `Unrecognized statement` | The line uses syntax not implemented yet, or has a typo. | Check the supported grammar and capitalization; functions, `if`, and loops are not implemented. |
| `Unknown variable or math function` | The name was not defined, has a typo, or a math name lacks the `math.` prefix. | Check spelling/case and define the value before use. |
| `Variable ... already defined` | The program declares the same name twice with `let`. | Use a fresh name; for mutable values, update using `set`. |
| `Variable ... is immutable` | `set` was used on a plain `let` variable. | Declare that variable with `let mut`. |
| `has not been defined` for a class | The class name in the `= class` part has no matching declaration. | Match the name exactly, including spaces and capitalization. |
| Expected an indented color line | A `class` declaration is missing its next-line `color`. | Add an indented `color #...` line immediately after the class. |
| Invalid color declaration | A color does not have 3, 4, 6, or 8 hex digits after `#`, or contains non-hex characters. | Use one of the accepted forms, such as `#abc` or `#aabbcc`. |
| `Division by zero` / `Modulo by zero` | The right-hand value was zero. | Check the formula; this version stops instead of silently continuing. |
| Math function expects a different argument count | A function was called with too few or too many values. | Check whether it expects one argument or two in the math reference above. |
| `Unsupported string escape` | A backslash escape is not one of the supported escapes. | Use `\"`, `\\`, `\n`, `\r`, or `\t`. |
| `cspeed` is not recognized | Windows PATH is not refreshed or the command was not installed. | Open a new terminal and check `%LOCALAPPDATA%\C-Speed` is on your user PATH. |
| Windows says an app/runtime is needed | The framework-dependent build requires the .NET 8 Windows Desktop Runtime. | Install the matching Windows Desktop Runtime, or use a self-contained release if one is provided. |

When a syntax error gives a line number, open the `.csp` file in a code editor and check that line first. The error is about your `.csp` source unless it says `c-speed:` without a source line.

## 25. Command reference

Run from the folder containing the source file:

```powershell
cspeed main.csp
```

Provide a full source path:

```powershell
cspeed "C:\MyCspeed\main.csp"
```

Create optional HTML instead of opening a desktop window:

```powershell
cspeed main.csp --html
cspeed main.csp --html "C:\MyCspeed\output.html"
```

When developing C Speed itself, run the source version:

```powershell
dotnet run --project "C:\path\to\islam c\compiler\Cspeed\Cspeed.csproj" -- "C:\MyCspeed\main.csp"
```

Build and run the focused smoke tests from the repository folder:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\compiler\tests\SmokeTests.ps1
```

The smoke tests verify numeric precedence and functions, mutable variables, color use, safe HTML text export, selected errors, and that normal app mode does not create an HTML file.

## 26. Tiny glossary

| Word | Meaning in this project |
|---|---|
| **Source code** | Instructions in a text file such as `main.csp`. |
| **Syntax** | The spelling and arrangement of the instructions. |
| **Statement** | One instruction, such as `let x = 3` or `for page include ("Hi")`. |
| **Variable** | A named place for a value, such as `distance`. |
| **Expression** | A calculation that produces a value, such as `speed * time`. |
| **Type** | The kind of value, such as the planned `f64` number type. |
| **Parser** | The part of a language tool that reads text and recognizes its grammar. |
| **Interpreter** | A program that reads source instructions and runs them without first compiling them to native code. |
| **Compiler** | A program that translates source into another form, often native machine instructions. |
| **Runtime** | Support code and services available while a program runs. |
| **Library** | Reusable functions and data structures a program can call. |
| **Module/import** | A planned way for one source file to reuse code from another file. |
| **Tensor** | A multi-dimensional numeric collection commonly used in AI and scientific computing. |
| **Training** | Adjusting a model's parameters from examples to reduce its prediction error. |
| **Inference** | Using an already-trained model to calculate an output for new input. |
| **GPU** | A processor that can perform many suitable calculations in parallel; it is not automatically used by C Speed today. |

## 27. What to learn next

Use the current features to get comfortable with source files, variables, expressions, and output. Then a sensible order for building the language is:

1. Add comparisons and `if`/`else` with tests.
2. Add functions and function parameters.
3. Add arrays and loops for collections of values.
4. Add structured diagnostics and documented number semantics.
5. Add safe file reading and writing with explicit errors.
6. Add imports/modules and a package/build workflow.
7. Add vector/matrix math and benchmarks for data workloads.
8. Add native library interoperability and CPU parallelism.
9. Explore GPU support and AI libraries.
10. Build and benchmark a native compiler backend.

At each step, keep working examples and tests. Do not treat future snippets in this guide as implemented until a C Speed release explicitly documents and tests that feature.

## 28. Detailed walkthrough of the tutorial programs

This section revisits the runnable examples so you can understand the role of every line rather than memorizing a finished block of code.

### Hello program

```text
for page include ("Hello from C Speed!")
```

- `for page include` is the output statement implemented in this version.
- `(` starts its value.
- `"Hello from C Speed!"` is a text literal. Double quotes tell C Speed not to treat its contents as a variable or calculation.
- `)` ends the value.
- There is no semicolon. One source line is one statement.
- When the statement is interpreted, a text item is added to the program output. When all source lines are processed, the desktop UI shows that text below the built-in logo.

### Styled-text program

```text
class 'greeting'
    color #f245

for page include ("Hello in color!") = class 'greeting'
for page include ("This second line is black.")
```

- `class 'greeting'` stores a style under the exact name `greeting`.
- The next line has four leading spaces and describes the style. `color` is the property; `#f245` is its value.
- A blank line is ignored.
- The first output statement stores both the text and the requested class name. It does not need to look up a class immediately.
- The class-usage check runs after the source pass; then the class's `#f245` color is attached to the first text item.
- The last statement has no class name, so its color is black.
- This class has no effect on variables or calculations. It is just reusable presentation information.

### Immutable calculation

```text
let speed: f64 = 300.0
let time: f64 = 2.0
let distance: f64 = speed * time
for page include (distance)
```

- The first `let` declares the name `speed`.
- `: f64` asks the current checker to accept the declaration as a floating-point number. It is the only currently accepted explicit numeric type.
- `= 300.0` initializes the value. The decimal point makes the example visibly floating-point; even `300` is parsed as an `f64` in this prototype.
- The next line declares `time` separately.
- The third line calculates the product using the values already in the variable table, then stores that result as `distance`.
- `for page include (distance)` sees an identifier instead of a string literal, looks it up, formats the numeric value, and adds that formatted value to output.
- None of these variables can be changed later because none was declared with `mut`.

### Mutable calculation

```text
let mut total: f64 = 10.0
set total = total + 5.0
for page include (total)
```

- `let mut` declares that `total` may be updated.
- `set total = ...` first verifies that `total` exists and is mutable.
- The right side is evaluated before replacing the old value. It finds the old `total` (`10.0`), adds `5.0`, then stores `15.0`.
- The last line reads the updated value and displays it.
- To experiment, change the addition to `total * 2`; the output should then be `20`.

### Circle formula

```text
let radius: f64 = 3.0
let area: f64 = math.pi * radius ** 2
let circumference: f64 = 2 * math.pi * radius
for page include ("Area:")
for page include (area)
for page include ("Circumference:")
for page include (circumference)
```

- `radius` is input typed directly in the program. The prototype does not prompt for user input.
- `math.pi` is the built-in constant π (approximately 3.14159).
- `radius ** 2` calculates radius squared.
- Multiplication has higher precedence than addition, and the expression is evaluated using normal arithmetic precedence.
- `2 * math.pi * radius` evaluates left to right among multiplication operations.
- The text and numeric values use separate statements because variables cannot currently be converted to or combined with strings.
- To use a different radius, edit the source, save it, and rerun the command.

### Weighted-score formula

```text
let signal_a = 2.0
let signal_b = 3.0
let weighted_score = signal_a * 0.25 + signal_b * 0.75
for page include ("Weighted score:")
for page include (weighted_score)
```

- The first two statements create numeric inputs.
- The next statement multiplies `signal_a` by `0.25`, multiplies `signal_b` by `0.75`, then adds the two results.
- The two weights are fixed constants typed by you. No training changes them.
- The output is a normal arithmetic result, **not an AI prediction**. The example only teaches variables, multiplication, addition, and displaying a result.

### HTML-export command

```powershell
cspeed main.csp --html "C:\MyCspeed\output.html"
```

- `cspeed` starts the installed command-line host.
- `main.csp` is the source file to interpret.
- `--html` selects optional HTML export instead of the normal desktop window.
- The quoted last argument selects where the generated HTML is written.
- If you omit the output path and run only `cspeed main.csp --html`, the prototype chooses `main.html` beside the `.csp` source.
- HTML export is a debugging/sharing option; it is not needed to run the program in the native C Speed window.

## 29. What happens when you type `cspeed main.csp`

There are several layers between a terminal command and the visible result:

1. **PowerShell locates `cspeed`.** Windows searches directories listed on PATH for a matching executable. The per-user installation puts `cspeed.exe` and `c-speed.exe` in `%LOCALAPPDATA%\C-Speed`.
2. **The operating system starts the app.** The executable is a Windows desktop application built on .NET Windows Forms. The logo is included inside the executable, so it does not need to find the logo next to your `.csp` file.
3. **The command-line argument is passed in.** `main.csp` tells C Speed which source file to read. The current folder is where PowerShell looks for that relative path.
4. **C Speed resolves and reads the file.** It converts the source path to a full path and reads its lines.
5. **The statement parser does a source pass.** It examines each nonempty, non-comment line and recognizes one of the supported statement forms.
6. **Numbers are evaluated immediately.** Variable declarations and updates are calculated as their lines are reached; values are stored in a dictionary under variable names.
7. **Text output is collected.** Each `for page include` becomes an item containing displayed text and, if requested, a color.
8. **Class references are checked.** Once all lines have been read, every named style used by output must exist.
9. **The GUI is constructed.** `CSpeedWindow` adds the embedded logo, creates one text label for each output item, and applies each item's color.
10. **The event loop keeps the window open.** The app remains alive until you close the window. This is what makes it behave like a separate desktop program instead of immediately closing after printing text.

### Why output statements do not show up while parsing

The current prototype collects page text and displays it after the whole program is compiled/interpreted. It is not a live window being updated one statement at a time. That makes it possible to catch class names used before their declaration, but it also means a long calculation blocks until interpretation finishes. There are no loops in the current grammar, so programs cannot yet express that sort of long-running computation.

### How a math expression is read

For the expression:

```text
let result = 3 + 4 * 2
```

the expression reader performs these conceptual steps:

1. **Tokenize:** It separates the source into number `3`, `+`, number `4`, `*`, and number `2`.
2. **Recognize precedence:** It knows multiplication binds more tightly than addition.
3. **Build the calculation order:** It treats the expression as `3 + (4 * 2)`.
4. **Evaluate:** It calculates `4 * 2` as `8`, then calculates `3 + 8` as `11`.
5. **Return a numeric value:** The declaration stores the value `11` in `result`.

The parser is written as a precedence-aware expression parser rather than calling the .NET parser on user-provided text. It only recognizes the explicitly listed operators/functions. This helps produce errors for unsupported characters and unknown names rather than executing arbitrary code.

### Why HTML export escapes text

In the desktop window, text is assigned to a Windows label control. In HTML export, text goes into a markup document, where characters such as `<` and `>` have special meaning. The renderer HTML-encodes text before writing it. So:

```text
for page include ("<hello>")
```

is meant to display the literal characters `<hello>`, not to create a tag or execute embedded markup.

## 30. Set up a project folder

A simple beginner project can look like this:

```text
C:\MyCspeed\
    main.csp
    notes.txt
```

Only `main.csp` is read by the current C Speed runner. `notes.txt` is just an ordinary file; current C Speed code cannot open it. Having both files in the same folder does not automatically connect them.

In PowerShell:

```powershell
cd "C:\MyCspeed"
Get-ChildItem
cspeed main.csp
```

- `cd` changes the PowerShell working directory.
- `Get-ChildItem` lists items in that folder so you can check that `main.csp` exists.
- `cspeed main.csp` runs that source file.

You can run a specific file from another folder without changing directory:

```powershell
cspeed "C:\MyCspeed\main.csp"
```

Do not type the PowerShell commands into `main.csp`. The `.csp` file contains C Speed language statements; commands like `cd` and `cspeed` belong in the terminal.

## 31. Publishing and installation concepts

The source project contains the interpreter's C# implementation, the embedded image, examples, and documentation. Publishing packages that implementation as a Windows app. It is different from compiling an individual `.csp` source program to native code.

The user-level `--install-command` action:

1. Copies the published app into `%LOCALAPPDATA%\C-Speed`.
2. Creates `cspeed.exe` as the everyday short command and `c-speed.exe` as its alias.
3. Adds the install folder to your **user PATH** so new terminals can find the command.
4. Registers a `.csp` file association for the current Windows account so Explorer can display the app icon and launch the app when a `.csp` file is opened.

The current app installer does not ask for administrator permission or change a machine-wide configuration. After installation, updating the app means publishing a new executable and installing/copying that version to the same per-user location. A polished public release should eventually use an installer/update process and specify supported Windows/.NET requirements.

GitHub's Releases page is where project maintainers can upload a packaged Windows build for users. The project README currently uses a placeholder GitHub release URL until a real repository URL is configured.

## 32. AI learning roadmap in more detail

This section is educational background, not a claim that C Speed already has these APIs.

### Data and features

An ML program first turns raw observations into numeric values the model can process. A dataset might have one row per observation and one column per feature. For example, a toy temperature dataset might use:

```text
// Illustrative data table, not C Speed syntax:
// hours_since_sunrise, temperature_celsius
0, 12.0
1, 13.5
2, 16.0
```

A future data library would need to load rows, validate columns, identify missing or malformed measurements, and represent the result in arrays or tensors. The current interpreter cannot do any of those operations; the `.csp` runner presently accepts only scalar numeric literals and page strings.

### Model and loss

A simple model might predict a value using `prediction = weight * input + bias`. Learning means choosing and updating `weight` and `bias` from examples so that the model's loss—a number measuring prediction errors—gets smaller. C Speed today can calculate one scalar formula, but it does not contain training loops, arrays of examples, gradient calculations, or an optimizer.

### Training and testing

Training data and evaluation data should be separated. If a model is repeatedly adjusted using all the test data, a good test score may no longer tell you whether the model generalizes. A real AI toolkit should support reproducible random seeds, evaluation metrics, and clear separation of training/evaluation data.

### GPU does not automatically make AI fast

A GPU helps when the software has supported GPU kernels, compatible drivers and hardware, and enough work to offset transferring data to the device. Merely installing a GPU does not make this C Speed prototype use it. Our future implementation needs a GPU backend and libraries designed to use it before an AI program can take advantage of one.

### Recommended build order for C Speed AI features

1. Implement arrays with dimensions, numeric types, and explicit memory behavior.
2. Add file input and a first well-defined dataset format.
3. Implement and benchmark vector/matrix operations.
4. Decide how C Speed calls established native ML libraries.
5. Build a small CPU inference example and compare its results with a trusted reference.
6. Design GPU support and validate supported hardware/drivers.
7. Add training APIs only after the data, tensor, numerical, and test foundations are reliable.

## 33. File support roadmap

File operations have different needs:

- **Reading text** needs path rules, encodings, and errors.
- **Writing text** needs file creation/overwrite behavior and error handling.
- **CSV** needs quoting, separators, headers, line endings, and malformed-row behavior.
- **Binary/scientific data** needs fixed-width formats, endianness, versioning, validation, and bounded memory use.
- **Imports** need a module search path, dependency order, naming rules, and protection against import cycles.

For large analysis datasets, reading the whole file into a string may use too much memory. The eventual API may need streaming, memory mapping, or chunked reads. Those are design choices; none are implemented now. Always check that the documentation for a C Speed release lists a file feature before trying to use it.

## 34. Run, edit, repeat

The most important beginner workflow is:

1. Open `main.csp` in a text editor.
2. Change a number or output string.
3. Save the file.
4. Switch to a terminal in that file's folder.
5. Run `cspeed main.csp`.
6. Read the error if it appears; correct the source and run again.

For example, change:

```text
let x: f64 = 3.0
```

to:

```text
let x: f64 = 6.0
```

in the distance program, save the file, and rerun. The program is interpreted again from the saved source. C Speed does not automatically watch for edits or update an already-open output window.

If the window remains open, close it before running the program again. Each run starts a separate process/window; the current prototype does not reload code into an existing window.

## 35. Keep a small reference nearby

### Working today

```text
// whole-line comments

class 'style name'
    color #abc

let value = 2 + 3 * 4
let mut changeable: f64 = 1.5
set changeable = changeable + value

for page include ("plain text")
for page include ("styled text") = class 'style name'
for page include (value)
```

### Available math names

```text
math.pi
math.e
math.sqrt(x)       math.abs(x)
math.floor(x)      math.ceil(x)
math.round(x)      math.exp(x)
math.sin(x)        math.cos(x)
math.tan(x)        math.asin(x)
math.acos(x)       math.atan(x)
math.log(x)        math.log10(x)
math.min(x, y)     math.max(x, y)
math.pow(x, y)
```

### Not working yet

```text
function ...
if ... else ...
for each ...
file.read_text(...)
import ...
ai.create_model(...)
gpu.run(...)
```

These last forms are reminders of future goals only, not statements that the current compiler understands.
