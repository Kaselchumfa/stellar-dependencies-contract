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
- `script/`: Foundry deployment scripts.
- `foundry.toml`: pinned compiler/build settings.

## Deploy to a testnet

The deployment script lives at `script/Deploy.s.sol`. It reads the deployer credentials from your environment or a Foundry keystore account — never from a committed file.

> **Never commit private keys.** `.gitignore` already excludes `.env` files, so keep any local secrets in `.env` (or your shell) and out of version control.

### 1. Provide credentials

Pick **one** of the following approaches.

**Option A — environment variable**

```sh
export PRIVATE_KEY=0x<your-testnet-private-key>
```

**Option B — Foundry keystore (recommended)**

Import the key into an encrypted keystore once, then reference it by name:

```sh
cast wallet import deployer --interactive
```

This stores the key encrypted on disk; you will be prompted for the password when deploying.

### 2. Run the deployment

With the environment variable:

```sh
forge script script/Deploy.s.sol \
  --rpc-url <TESTNET_RPC_URL> \
  --broadcast
```

With a keystore account, add `--account`:

```sh
forge script script/Deploy.s.sol \
  --rpc-url <TESTNET_RPC_URL> \
  --account deployer \
  --broadcast
```

### 3. (Optional) Verify the contract

If the target network has a block explorer, add `--verify` and supply the explorer API key:

```sh
forge script script/Deploy.s.sol \
  --rpc-url <TESTNET_RPC_URL> \
  --account deployer \
  --broadcast \
  --verify \
  --etherscan-api-key <EXPLORER_API_KEY>
```

### 4. Confirm the deployment

The script prints the deployed `ShortlistRegistry` address. Note it down and confirm the contract on the testnet explorer before wiring it into the backend or frontend.