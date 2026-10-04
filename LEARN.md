# C Speed (working name) — Design Specification

**Status:** Initial proposal, version 0.1  
**Purpose:** A compiled programming language aimed at extremely demanding, compute-intensive software: large-scale data analysis, AI, scientific and rocket/aerospace calculations, and high-graphics games.

![C Speed icon](./assets/c-speed-icon.jpeg)

The first working prototype supports page text, color classes, and basic `f64` math expressions, and runs them in a standalone Windows application window. HTML is an optional export. This is a learning milestone, not yet the planned optimizing native compiler.

## 1. Design goals

1. **High performance:** Compile ahead of time to native machine code, with optimized-build performance aimed at the level of well-optimized C++ for comparable workloads. This is a benchmark goal, not a guarantee; generated code, libraries, hardware, and workload all matter.
2. **Predictable resource use:** No garbage collector or surprise pauses. Make allocations and data movement understandable and controllable.
3. **Safety by default:** Use compile-time ownership and borrowing to prevent common memory errors without requiring a garbage collector.
4. **Low-level capability:** Provide explicit allocators and a clearly marked `unsafe` escape hatch for operations that need direct machine or memory control.
5. **Numerical and data workloads:** Provide fixed-width numeric types, efficient arrays, parallel computation, and a path to GPU computing.
6. **Useful across domains:** Support small, native command-line programs as well as larger applications and libraries.

Performance is a design target, not a guarantee: actual results depend on the program, compiler, libraries, hardware, and measurement.

## Hardware expectations

C Speed is intended for demanding software, but the language and compiler should not require powerful hardware just to compile or run every program. For development and CPU-focused workloads, target a modern Intel Core i5-class processor or equivalent; integrated graphics should be sufficient for general use. GPU-accelerated AI, simulation, or graphics workloads may need a compatible dedicated GPU, depending on the software and workload. Actual requirements will vary and should be established with benchmarks.

## 2. Proposed language profile

- **Compilation:** Ahead-of-time (AOT) compilation to native executables and libraries.
- **Memory:** Compile-time ownership and borrowing; no tracing garbage collector.
- **Runtime:** Small optional runtime for features such as threading. Programs should be able to avoid it where practical. The current prototype uses a Windows desktop window to display page output.
- **Safety boundary:** Safe code is the default. Low-level operations that cannot be statically verified must be inside explicit `unsafe` blocks.
- **Portability:** The language should target common desktop and server platforms first, then expand to consoles, embedded systems, and accelerators as the toolchain matures.
- **Compiler backend:** Still undecided. LLVM is a strong initial candidate for optimization and target coverage, but the language design should not depend on a specific backend.

## 3. Illustrative syntax

The proposed syntax should be approachable, with minimal punctuation and no semicolons. Indentation groups statements inside functions and control-flow blocks. The page and class forms below preserve the syntax proposed by the language creator. Page text is black by default; a named class defines reusable styling:

```text
class 'hello snippet'
    color #f245

for page include ("hello") = class 'hello snippet'
```

The class declaration defines the style named `hello snippet`; the output statement assigns `hello` to that class. Without a class assignment, included text is black by default. `#f245` is an example color value; the accepted color formats and exact class grammar still need to be defined. The exact meaning of “page” and how programs provide that output (for example, a browser page or another display) still needs to be defined as the runtime takes shape.

### Proposed general-purpose syntax

The following forms are a starting proposal for writing calculations and application logic. They are not finalized language rules:

```text
function distance(x: f64, y: f64) -> f64:
    let squared = x ** 2 + y ** 2
    return math.sqrt(squared)

let result = distance(3.0, 4.0)

if result >= 5.0:
    for page include ("Distance is at least five")

function travel_distance(speed: f64, time: f64) -> f64:
    return speed * time

let speed: f64 = 300.0
let time: f64 = 2.0
let total: f64 = travel_distance(speed, time)

if total > 500.0:
    for page include ("That is fast!")
else:
    for page include ("Ready")

let samples: array<f64> = [1.5, 2.0, 3.25]
let mut sample_total: f64 = 0.0
for each sample in samples:
    set sample_total = sample_total + sample
```

Proposed basics: `let` creates a named value, types may be written after a name, `let mut` marks a value that can change, and `set` updates it. `function` defines reusable code, `if`/`else` choose a path, and `for each` visits collection items. A function can return a value. Expressions should feel familiar to Python users: arithmetic operators (`+`, `-`, `*`, `/`, `%`, and proposed `**` for powers), comparisons, parentheses, and a standard `math` library with functions such as `sqrt`. Unlike Python, C Speed is intended to use static types and ahead-of-time native compilation rather than dynamic interpretation. Blocks are grouped by indentation; semicolons and braces are not required. These choices aim to keep everyday code simple while allowing explicit types for performance-sensitive work.

## 4. Core language features

### Types and numerics

- Inferred local types where unambiguous; explicit types at public interfaces and where inference would obscure behavior.
- Fixed-width integer types (`i8` through `i128`, `u8` through `u128`) and standard floating-point types (`f32`, `f64`).
- `bool`, tuples, structs, enums, arrays, slices, and generic functions and types.
- A standard math library for common operations such as square root, powers, absolute value, rounding, and trigonometry, with behavior and precision documented for each numeric type.
- Explicitly specified integer overflow behavior and conversion rules. These semantics must be decided before the language is considered stable.
- A future numeric library may add complex numbers, arbitrary precision, and domain-specific math without requiring them in the minimal compiler.

### Memory and ownership

- Values have clear ownership. Borrowing allows temporary access without copying or transferring ownership.
- The compiler rejects dangling references and conflicting mutable access in safe code.
- Allocation is explicit or visible through APIs such as vectors, arenas, and custom allocators.
- `unsafe` blocks allow carefully controlled low-level operations; they do not disable checks outside the block.
- No implicit garbage collection and no hidden allocation in basic operations.

### Performance and parallelism

- Optimized builds should support inlining, dead-code removal, link-time optimization, and target-specific CPU instructions where available.
- Treat performance comparable to optimized C++ on representative workloads as a goal to measure with published, reproducible benchmarks; do not promise universal parity or superiority.
- The language should make data layout controllable for cache-sensitive and FFI-heavy code.
- Parallelism should be explicit and composable, with a standard path for threads and parallel loops.
- SIMD/vector operations should be available through portable abstractions, with an escape hatch for target-specific intrinsics.
- GPU compute is a planned capability, not a promise of the first compiler release. It will need a defined device model, memory-transfer rules, and supported backend(s).

### Errors and interfaces

- Prefer explicit result values for recoverable errors; do not silently discard failures.
- Provide a documented C ABI / foreign-function interface for integrating existing native libraries.
- Define module and package behavior before stabilizing the public language surface.

## 5. Domain priorities

- **Large-scale data analysis and AI:** Efficient contiguous arrays, numeric kernels, parallel execution, and interoperability with existing C/C++/Fortran libraries. Higher-level tensor and model libraries can be built on these foundations; accelerator support is a planned capability.
- **Scientific and rocket/aerospace calculations:** Fixed-width and floating-point types, predictable execution, explicit error handling, and careful numerical testing. The language itself does not certify software as safe for flight or other safety-critical use.
- **High-graphics, compute-intensive games:** Native builds, control over memory layout, low-overhead abstractions, threading, SIMD, and eventual graphics/GPU ecosystem support.
- **General high-performance software:** Fast startup, small runtime footprint, profiling-friendly builds, and reliable native library interoperability.

## 6. Initial implementation roadmap

1. **Specify the core:** Finalize syntax, types, integer overflow/conversion rules, ownership rules, and module boundaries.
2. **Build the learning prototype:** Interpret page text, classes, variables, and basic `f64` math; display output in a standalone window, support optional HTML export, and test diagnostics.
3. **Build the native compiler:** Parse and type-check functions, structs, basic control flow, and calls; emit native code through a selected backend.
4. **Add memory safety:** Implement ownership/borrowing checks and test both accepted and rejected programs.
5. **Create the standard library:** Add strings, collections, file I/O, errors, and basic threading without hiding costly behavior.
6. **Measure and optimize:** Establish benchmarks for numeric loops, allocation, startup, and FFI before claiming performance.
7. **Expand domains:** Add SIMD and parallel numeric primitives, then explore GPU support and higher-level data/AI and graphics libraries.

## 7. Decisions still open

- Final syntax rules, including indentation, mutability, and page/class behavior; the current proposal omits semicolons and braces.
- Compiler implementation language and backend.
- Integer overflow defaults and checked-arithmetic syntax.
- Exact arithmetic operator rules, including the proposed `**` exponentiation operator, and math-library precision guarantees.
- The details of ownership, lifetimes, and allocator APIs.
- Supported operating systems and CPU architectures for the first release.
- Which GPU APIs or graphics libraries to target, and when.
- Package manager, build system, debugger, formatter, and test tooling.

These choices should be made deliberately and validated with small programs and benchmarks rather than assumed from the language name.
