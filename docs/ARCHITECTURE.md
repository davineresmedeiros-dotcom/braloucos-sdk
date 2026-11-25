# Braloucos SDK Architecture

## Purpose

The Braloucos SDK defines the interfaces and development tools used to create mods for the Braloucos modding ecosystem. It is separate from the Android launcher and does not own launcher screens, Android activities, or installation UX.

This document describes the intended SDK design. It does not claim that the interfaces or runtime are already implemented.

## Main parts

### Public mod API

The public API is the supported surface available to mod authors. It should expose stable abstractions for:
- Mod lifecycle and initialization.
- Logging and diagnostics.
- Access to declared mod data.
- Capability discovery.
- Registration of supported hooks or extension points.
- Inter-mod communication, if explicitly enabled by the runtime.

The API must avoid exposing internal loader implementation details unless they are deliberately part of a versioned contract.

### Mod manifest

Every mod package uses a manifest describing its identity and requirements.

Recommended fields:
- `formatVersion`: package format version.
- `id`: stable, unique mod identifier.
- `name` and `version`: human-readable name and mod version.
- `entry`: declared entry point.
- `sdk`: compatible SDK range.
- `game`: supported game versions or version range.
- `dependencies`: required or optional mod identifiers and version constraints.
- `capabilities`: runtime capabilities requested by the mod.
- `description` and `authors`: package metadata.

The manifest is data, not executable code. The loader validates it before loading the mod.

### Package format

The official mod file extension is `.braloucos`.

The extension identifies a Braloucos package but does not prescribe a particular compression algorithm by itself. The package format should use a documented container structure with:
- A manifest at a fixed, discoverable location.
- Mod payload files.
- Optional assets and data.
- Optional integrity metadata.

The container format and manifest format must be independently versioned. A future format change must be detectable before extracting or executing payloads.

### Native SDK

The native SDK is designed around C++ for native mods and the supported Android ABI(s). Public headers define the API; implementation details remain inside the loader.

The SDK should provide:
- A stable set of headers.
- Explicit ABI and SDK version identifiers.
- Consistent symbol visibility and naming.
- Clear ownership and lifetime rules.
- Error/status conventions that work across the JNI boundary.
- Examples that build against the published SDK.

Avoid requiring mod authors to include private loader headers or link against undocumented internal symbols.

### Runtime contract

The loader discovers packages, validates metadata, resolves dependencies, and initializes mods in a deterministic order.

A mod lifecycle should distinguish at least:
- Discovery.
- Validation.
- Dependency resolution.
- Loading.
- Initialization.
- Active.
- Shutdown or failure.

A failure in one mod should be reported with that mod's identifier and phase. The runtime should apply a documented policy for dependent mods rather than continuing unpredictably.

### Hooks and game integration

Hooks are exposed through deliberate extension points rather than allowing the public SDK to assume a particular game's internal layout.

Game-specific compatibility code belongs in the loader's integration layer. The SDK API should remain as stable as possible across game updates; compatibility-specific functionality must be clearly marked and versioned.

### Build and distribution

SDK releases should include:
- Public headers and required metadata.
- Supported compiler/ABI information.
- Versioned documentation.
- Minimal examples.
- License and attribution information.

The SDK version, package format version, and loader version are separate values. They must not be conflated.

## Security and trust

Native mods execute code inside the game process and can affect the process and its data. The package extension, manifest, or signature does not make a mod inherently safe.

The loader should validate package structure and metadata, restrict file extraction to approved directories, and clearly communicate that users should install mods from sources they trust.

## Compatibility policy

- Reject unsupported package format versions.
- Validate SDK compatibility before initialization.
- Check declared game compatibility.
- Keep public API changes versioned.
- Prefer a clear load error over undefined behavior.

## Relationship to other components

The SDK defines the mod-author-facing contract. The native loader implements that contract. The Android launcher prepares the environment and coordinates startup. The launcher must not become a dependency of SDK headers or native mod source code.
