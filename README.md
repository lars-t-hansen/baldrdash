# baldrdash
Some patches for hooking Cretonne into spidermonkey.

The file `aldrdash.patch` should apply to a mozilla-inbound checkout (currently mercurial rev 367478:7a401b1e5f98).  It provides:

- New files `js/src/wasm/WasmCretonneCompile.{cpp,h}` which define the function `js::wasm::CretonneCompileFunction`. This has the same API as `js::wasm::IonCompileFunction` and `js::wasm::BaselineCompileFunction`. This new function is the entry point for the Rust backend based on Cretonne.  It sets up a compilation context and calls into rust to compile a single Wasm function.
- A command line switch to the JS shell, `--wasm-always-cretonne`, that forces the Wasm compiler infrastructure to call `js::wasm::CretonneCompileFunction` for all Wasm compilation
- A new subdirectory js/src/baldrdash, which currently contains C bindings for Wasm data structures and Rust wrappers around those bindings, but which could in principle contain a Wasm function compiler written in Rust.
- Implementations of the C bindings on the Rust side in `js/src/baldrdash/src/baldrdash.rs` and on the C side in `js/src/wasm/WasmCretonneCompile.cpp`.  These are not quite done yet.
- Hooks for the mozilla build system to link with the Rust code in js/src/baldrdash.  This is work in progress, not really working yet.

Take a look at `js/src/baldrdash/src/lib.rs`, for an example of the `wasm_compile_function` function that compiles a single wasm function into machine code, and how it uses these APIs.  (What's there is only a stub, Cretonne is needed for serious work.)
