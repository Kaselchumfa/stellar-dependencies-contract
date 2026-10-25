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

## Project structure

- `src/`: contract source.
- `test/`: self-contained Solidity tests.
- `docs/adr/`: Architecture Decision Records.
- `foundry.toml`: pinned compiler/build settings.

## Access control model

The `ShortlistRegistry` uses a single-owner model. The owner is set at
deployment time (the deployer) and can be transferred by the current owner.

### Roles

| Role | How assigned | Permissions |
| --- | --- | --- |
| **Owner** | Set to `msg.sender` at deployment; changed via `transferOwnership` | Create, update, and remove entries; transfer ownership |
| **Anyone (unauthenticated)** | N/A | Read-only: query entries and the owner |

### Permissions by operation

| Operation | Who may call | Notes |
| --- | --- | --- |
| `addEntry(...)` | Owner only | Reverts for any other caller |
| `updateEntry(...)` | Owner only | Reverts for any other caller |
| `removeEntry(...)` | Owner only | Reverts for any other caller |
| `transferOwnership(...)` | Owner only | Reverts for any other caller; zero address rejected |
| `getEntry(...)` / `owner()` | Anyone | View functions, no state change |

All state-changing functions are guarded by an `onlyOwner` check. Unauthorized
callers are rejected with the custom error `NotOwner()`. The test suite
(`test/`) includes cases proving that non-owner callers are rejected for every
state-changing operation.

## Input validation and custom errors

The contract validates inputs and reverts with descriptive custom errors
rather than generic require strings:

- `NotOwner()` — caller is not the owner.
- `ZeroAddress()` — an address argument (e.g. new owner) is the zero address.
- `EmptyName()` — an entry name is empty.
- `EntryExists()` — attempting to add an entry that already exists.
- `EntryNotFound()` — attempting to update or remove a missing entry.
- `NameTooLong()` — an entry name exceeds the maximum allowed length.

These limits and errors are enforced in code and covered by tests.

## Architecture decisions

Design decisions are recorded as ADRs under `docs/adr/`. In particular, the
immutable-versus-upgradeable strategy is documented in
[`docs/adr/0001-immutable-vs-upgradeable.md`](docs/adr/0001-immutable-vs-upgradeable.md).
The contract is deployed as an immutable (non-upgradeable) contract, matching
that decision.