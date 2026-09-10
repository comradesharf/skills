---
name: uniffi-xcode
description: Automatically integrate the current Rust project with Xcode or Swift Package Manager using UniFFI, including Rust exports, Swift bindings, Apple-platform libraries, XCFramework packaging, project wiring, and build verification. Use for new integrations and broken existing bridges.
---

# UniFFI Xcode integration

Implement a working Rust-to-Swift bridge in the current workspace. Inspect first, infer choices from the repository, make the required changes, and verify the result. Do not stop at instructions or generated snippets when the user asked to integrate the project.

## Discover the integration shape

Inspect repository instructions and the current worktree before editing. Locate:

- the Cargo workspace and candidate library crates;
- existing UniFFI dependencies, scaffolding, exports, UDL files, and binding tools;
- `.xcodeproj`, `.xcworkspace`, `Package.swift`, Apple deployment targets, schemes, and app imports;
- existing build scripts, CI, generated-file policy, and artifact directories.

Prefer the Rust crate already referenced by Apple-side code. Otherwise choose the only library crate that exposes the relevant API. If several crates are equally plausible, or no intended Apple platform can be inferred, ask one concise question before making structural changes.

Preserve an existing integration model. For a new integration, prefer a local Swift package containing generated Swift sources plus a binary target backed by an XCFramework; it keeps generated bindings and native linkage out of the app target and works cleanly with Xcode. Do not introduce a separate package when the repository clearly standardizes on direct Xcode integration.

Read [references/integration.md](references/integration.md) before implementing a new integration. Read [references/checklist.md](references/checklist.md) for a full audit or non-localized failure.

## Implement automatically

1. Configure the selected Rust library for UniFFI using the project’s existing dependency-management style. Keep the library and binding generator on compatible UniFFI versions. Preserve the project’s current proc-macro or UDL approach; for a new project, prefer proc-macro exports unless its API or conventions require UDL.
2. Add only the minimal exported API needed to prove the bridge. Do not expose unrelated internals or redesign the public Rust API. Ensure the library produces the native artifact required by the chosen packaging model.
3. Create or update a reproducible build command that:
   - installs or checks required Rust Apple targets without silently changing unrelated toolchains;
   - builds every architecture required by the inferred Xcode destinations;
   - generates Swift sources, C headers, and a module map from the same Rust build and matching UniFFI generator;
   - combines device architectures only within the same Apple platform when needed;
   - creates an XCFramework from separate platform libraries and headers;
   - writes generated files and artifacts to stable project-relative paths.
4. Create or update the Swift package or Xcode linkage. Keep the binary target, generated Swift target, product, module-map module, and Swift import names consistent. Add the package product to the actual app or test target when an Xcode project exists.
5. Add a minimal Swift smoke call or test for one exported Rust API when the repository has an appropriate test target. Avoid adding production UI solely to prove linkage.
6. Document the single project command that regenerates the bridge only if the repository has an established documentation location. The build script and package manifest should otherwise be self-explanatory.

Never hand-edit generated bindings. Generate replacements before removing currently usable artifacts, and preserve unrelated worktree changes. Do not commit large generated binaries unless repository policy or the existing integration indicates they are tracked.

## Verify the result

Verify at the narrowest meaningful layers, then expand:

1. Rust library build and tests.
2. Binding generation with the selected library artifact.
3. XCFramework slices, headers, module map, and exported symbols.
4. `swift build` for a Swift package when supported by its platforms.
5. `xcodebuild` for the discovered scheme and a real inferred destination.
6. A smoke test or call crossing Swift -> UniFFI -> Rust.

Use the repository’s existing commands where possible. If a required compiler target, Xcode SDK, signing identity, network dependency, or permission is unavailable, complete every independent step and report the exact blocker and command. Do not claim Xcode integration is complete from a Rust-only build.

## Diagnose existing integrations

- Missing Swift module or API: check package resolution, target dependencies, generated Swift membership, imports, and stale generated output.
- Header or module-map failure: check relative header paths and agreement among module-map, binary-target, and imported module names.
- Undefined symbols: inspect the selected XCFramework slice and Rust exports; confirm crate type, architecture, platform, and binding/library version match.
- Duplicate symbols or architectures: do not merge device and simulator libraries with `lipo`; keep them as separate XCFramework elements.
- Runtime loading failure: inspect dynamic install names, embedding/signing, selected slice, and initialization.
- Swift/Rust API mismatch: regenerate all binding outputs from the current library; do not patch generated declarations.

Finish by summarizing the chosen crate, supported Apple destinations, generated artifacts, Xcode/Swift package changes, verification commands and results, and any remaining external prerequisite.
