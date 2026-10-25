# DependencyLens Contract

Optional Solidity `ShortlistRegistry` for DependencyLens. Standalone and not yet wired to the app.

Version 0.2.0. Independent of Drips; no affiliation or funding guarantee.

This repository is one of three:

- **stellar-dependencies-contract** (this repo): Solidity contract
- **stellar-dependencies-backend**: API and GitHub/npm scanner
- **stellar-dependencies-frontend**: web UI

## Build and test

Requires [Foundry](https://book.getfoundry.sh/). Compiler and build settings are pinned in `foundry.toml`.

```sh
forge build
forge test
```

## ABI export

After `forge build`, run the export script to produce the contract ABI as JSON:

```sh
forge build
./scripts/export-abi.sh
```

This writes `abi/ShortlistRegistry.json`. That file is the stable, versioned interface consumed by the backend and frontend repositories, so treat it as the canonical contract interface rather than reading artifacts directly.

Each release attaches the ABI JSON as a release asset, so downstream consumers can pin to a specific released ABI.

## Project structure

- `src/`: contract source.
- `test/`: self-contained Solidity tests.
- `scripts/`: tooling, including `export-abi.sh` for ABI export.
- `abi/`: generated ABI artifacts (e.g. `ShortlistRegistry.json`).
- `foundry.toml`: pinned compiler/build settings.