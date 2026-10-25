# Security

This is an MVP scaffold, not an audited funding application. It never executes scanned code or manages funds. Input validation, fixed upstream hosts, disabled redirects, body limits, content security headers and server-only token handling reduce common risks; they do not replace production review.

Before publishing, enable your repository's private vulnerability reporting and document the actual reporting channel. Do not open public issues containing secrets. Rotate any leaked token immediately. No maintainer contact address is invented in this template.

## Access control model

The application distinguishes three roles. Access rules are enforced in code and covered by tests that prove unauthorized callers are rejected.

| Role      | Create entries | Change entries | Remove entries | Read entries |
|-----------|----------------|----------------|----------------|--------------|
| anonymous | no             | no             | no             | yes          |
| editor    | yes            | yes            | no             | yes          |
| admin     | yes            | yes            | yes            | yes          |

- **anonymous** callers may only read entries. Any attempt to create, change or remove an entry is rejected.
- **editor** callers may create and change entries but may not remove them.
- **admin** callers may create, change and remove entries.
- Roles are assigned server-side; callers cannot elevate their own role via request input.
- Every mutating request is authorized before any state change occurs, and rejected requests produce no side effects.

The corresponding tests assert that anonymous and under-privileged callers are rejected for each mutating operation.

## Input validation limits

- Request bodies are size-limited; oversized payloads are rejected.
- Upstream hosts are fixed and redirects are disabled.
- Scanned code is never executed.
- Tokens are handled server-side only and never exposed to the client.

## Design decision: immutable versus upgradeable

The project deliberately adopts an **immutable** design: deployed contracts/artifacts are not upgradeable in place. Changes ship as new deployments, and the trust and risk trade-offs of this choice are recorded in the architecture decision record at [`docs/adr/0001-immutable-vs-upgradeable.md`](docs/adr/0001-immutable-vs-upgradeable.md). The code matches that decision; any move to an upgradeable design requires a superseding ADR.