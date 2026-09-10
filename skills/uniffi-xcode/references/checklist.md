# UniFFI-to-Xcode audit checklist

Use for a full integration audit or when a failure is not localized.

## Discovery and design

- [ ] Repository instructions and existing worktree changes were inspected.
- [ ] The selected Cargo library and Apple consumer are unambiguous.
- [ ] Apple platforms, architectures, deployment targets, and schemes come from project evidence.
- [ ] The packaging model preserves repository conventions or uses a local Swift package plus XCFramework for a new integration.

## Rust and bindings

- [ ] UniFFI scaffolding and intended exports exist.
- [ ] Library crate type supports the selected native packaging.
- [ ] Library and binding generator use compatible UniFFI versions.
- [ ] Every required Rust Apple target is built with the intended configuration.
- [ ] Swift sources, C headers, module map, and native library come from matching inputs.
- [ ] Generated output has a stable location and is not hand-edited.

## XCFramework, package, and Xcode

- [ ] XCFramework elements represent distinct Apple platforms or variants.
- [ ] Each element contains the required architectures and exported symbols.
- [ ] Binary-target paths are correct relative to `Package.swift`.
- [ ] Target dependencies, package product references, and target membership are present.
- [ ] Rust library, header, module-map, binary-target, Swift target, product, and import names agree where required.
- [ ] Xcode destination and deployment target are compatible with the artifact.

## Verification

- [ ] Rust tests and native builds pass.
- [ ] Binding generation completes successfully.
- [ ] Swift package build passes when applicable.
- [ ] Xcode scheme builds for an inferred real destination.
- [ ] A smoke test or call crosses the FFI boundary.
- [ ] Any skipped verification has an exact external blocker, not an inferred success.
