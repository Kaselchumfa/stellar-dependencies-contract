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
- `test/ShortlistRegistry.invariants.t.sol`: Foundry invariant tests for the registry-wide properties documented below.
- `scripts/`: tooling, including `export-abi.sh` for ABI export.
- `abi/`: generated ABI artifacts (e.g. `ShortlistRegistry.json`).
- `foundry.toml`: pinned compiler/build settings.

## Registry invariants

The `ShortlistRegistry` maintains a set of registry-wide properties that must
hold after any arbitrary sequence of calls (register, unregister, transfer,
etc.). These are checked with Foundry invariant tests in
`test/ShortlistRegistry.invariants.t.sol`, which drive the contract through
randomized handler calls and assert the properties below after every run.

- **Unique registrations**: each dependency identifier is registered at most
  once. A successful registration never creates a duplicate entry, and the
  registry never reports two distinct entries for the same identifier.
- **Consistent ownership**: every registered entry has exactly one owner, and
  that owner is the address recorded for the entry. Ownership is never left
  unset for a live entry, and only the current owner can mutate or transfer an
  entry.
- **Monotonic counters**: any registry counter (e.g. total registrations or
  entry ids) never decreases across calls. Counters only advance, so ids are
  never reused and historical counts remain stable.
- **No orphaned entries**: every entry reachable through the registry's
  bookkeeping (owner index, id index, or enumeration) corresponds to a live
  registration, and every live registration is reachable through that
  bookkeeping. Removing an entry cleans up all associated indexes so no stale
  or dangling references remain.

Run the invariant suite with:

```sh
forge test --match-path test/ShortlistRegistry.invariants.t.sol
