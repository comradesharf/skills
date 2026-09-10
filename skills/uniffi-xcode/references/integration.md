# Generic integration decisions

Read this reference before creating a new UniFFI-to-Xcode integration.

## Choose targets from repository evidence

Derive Apple platforms from Xcode build settings, package platforms, schemes, and CI destinations. Map each destination to an installed Rust target rather than assuming a universal target list. Typical mappings include macOS arm64/x86_64, iOS device arm64, and iOS Simulator arm64/x86_64, but installed SDKs and Rust target names are authoritative.

Build each platform variant separately. A fat library may combine architectures for one platform variant; an XCFramework should carry device, simulator, and macOS variants as separate elements.

## Choose the package layout

For a new integration, prefer a structure equivalent to:

```text
Rust crate/workspace
Apple bridge package/
|-- Package.swift
|-- Sources/<SwiftModule>/        generated Swift bindings or a thin wrapper
|-- Sources/<CModule>/            generated header and module.modulemap when needed
`-- Frameworks/<Native>.xcframework
```

Adapt names and paths to the repository. The Swift target depends on the binary target, and the package product exposes the Swift target to the Xcode app. Some UniFFI generator/package layouts place the headers in the XCFramework rather than a separate C target; preserve whichever coherent model the installed UniFFI version and repository already use.

## Generate reproducibly

Use the UniFFI CLI available to the project and inspect its `--help` before writing commands, because flags and output layouts vary by version. Prefer a workspace-owned binding-generator binary or a lockfile-pinned tool over an unrelated globally installed version.

The regeneration command should derive its repository root from the script location, quote paths, fail on errors, and avoid machine-specific absolute paths. It should create parent directories, replace only owned generated outputs, and leave unrelated files untouched. A build must not accidentally package a stale host library after a cross-compilation failure.

If the project uses release artifacts in Xcode, build and package release consistently. If both debug iteration and release distribution are required, make configuration an explicit script input and keep Xcode linked to a predictable output path.

## Modify Xcode carefully

Prefer adding a local Swift package through a repository-supported project-generation mechanism when one exists. If the `.pbxproj` is source-controlled and no generator is used, make the smallest coherent edit and validate it with `xcodebuild -list` and an actual build. Do not rely on DerivedData or a developer-specific absolute package reference.

For a dynamic framework, embed and sign rules matter. For a static library inside an XCFramework, link it but do not treat it as a runtime-embedded dynamic framework.

## Define completion

Generation alone is incomplete. The result is integrated when the Apple consumer resolves the package or framework, compiles generated Swift, links the correct native slice, and executes at least one exported Rust call or test where the environment permits.
