# baldrdash
Some initial patches for hooking Cretonne into spidermonkey.

The file `baldrdash.patch` should apply to a mozilla-inbound checkout (currently mercurial rev 368041:9f61b0a0397e).  It provides:


- New files `js/src/wasm/WasmCretonneCompile.{cpp,h}` which define the function `js::wasm::CretonneCompileFunction`. This has the same API as `js::wasm::IonCompileFunction` and `js::wasm::BaselineCompileFunction`. This new function is the entry point for the Rust backend based on Cretonne.  It sets up a compilation context and calls into rust to compile a single Wasm function.
- A command line switch to the JS shell, `--wasm-always-cretonne`, that forces the Wasm compiler infrastructure to call `js::wasm::CretonneCompileFunction` for all Wasm compilation
- A new subdirectory `js/src/baldrdash`, which currently contains C bindings for Wasm data structures and Rust wrappers around those bindings, but which could in principle contain a Wasm function compiler written in Rust.
- Implementations of the C bindings on the Rust side in `js/src/baldrdash/src/baldrdash.rs` and on the C side in `js/src/wasm/WasmCretonneCompile.cpp`.
- A simple Rust wrapper for the compiler in `js/src/baldrdash/src/lib.rs`
- Hooks for the mozilla build system to build and link with the Rust code in `js/src/baldrdash`.
- A very dumb example compiler in `js/src/baldrdash/src/cretin.rs`.  It produces code that does run.  Comments in the file tell you how to test.

Note that if the Rust-to-C API definitions in `js/src/baldrdash/baldrapi.h` are changed, then `bindgen` must be re-run manually, see the `Makefile` in that directory.
