# Braloucos SDK

The **Braloucos SDK** defines the mod-author-facing interfaces and package conventions for the Braloucos Geometry Dash modding ecosystem.

It is intended to provide a stable contract for mod lifecycle, metadata, capabilities, diagnostics, and supported extension points.

## Architecture

See [Architecture](docs/ARCHITECTURE.md) for the SDK components, manifest model, `.braloucos` package format, native API boundaries, runtime contract, and compatibility principles.

## Mod package extension

Braloucos mods use the `.braloucos` extension. Package format and manifest versions are independent of the SDK and loader versions.

## Project boundaries

The SDK contains public modding interfaces and development-facing materials. It does not depend on Android launcher UI or private launcher implementation details.

## License

See [LICENSE](LICENSE).
