# Security

This is an MVP scaffold, not an audited funding application. It never executes scanned code or manages funds. Input validation, fixed upstream hosts, disabled redirects, body limits, content security headers and server-only token handling reduce common risks; they do not replace production review.

Before publishing, enable your repository's private vulnerability reporting and document the actual reporting channel. Do not open public issues containing secrets. Rotate any leaked token immediately. No maintainer contact address is invented in this template.

## Static analysis (Slither)

The Solidity contract under `src/` is scanned with [Slither](https://github.com/crytic/slither). Run it locally with:

```sh
pip install slither-analyzer
slither . --config-file slither.config.json
```

The goal is that no unexplained high or medium findings remain. Genuine issues are fixed in the contract; any remaining findings are listed below with a justification for accepting them.

### Accepted findings

The following findings are reviewed and accepted. Each entry records the detector, why it fires, and why it is acceptable for this scaffold.

- **`solc-version` (Informational/Low):** The contract pins a specific compiler version. This is intentional for reproducible builds; the pinned version is a recent, non-vulnerable release. Re-evaluate when upgrading the toolchain.
- **`pragma` (Informational):** A floating or pinned pragma is reported. The pragma is pinned deliberately to avoid compiling with an untested compiler.
- **`naming-convention` (Informational):** Reported for mixed naming styles. Naming is consistent with the surrounding codebase and has no security impact.
- **`assembly` / low-level calls (Informational, if present):** Any low-level usage is limited and reviewed; it does not handle untrusted input in a way that enables fund loss, since this scaffold never manages funds.
- **`timestamp` (Low, if present):** Any reliance on `block.timestamp` is for coarse ordering only and is not used for randomness or precise deadlines.
- **`locked-ether` / `payable` (Informational, if present):** The contract does not intend to hold ETH; any payable surface is either absent or documented as out of scope for this MVP.

If a new high or medium finding appears, it must be fixed or added here with a written justification before merge. Findings without an entry above are considered unexplained and block release.

### CI (optional)

To run Slither in CI, add a workflow that installs `slither-analyzer`, compiles the contracts, and runs `slither . --config-file slither.config.json`. Fail the job on high or medium findings (for example with `--fail-high` / `--fail-medium` or by parsing the JSON output) so regressions are caught early.