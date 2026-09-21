# Security and production notes

## What the lab enforced

- Public blob access disabled; HTTPS only; TLS 1.2 minimum.
- Test data was a one-line text file. No personal or customer data.
- A **separate** service principal ran the access tests so admin rights could not fake the result.
- Data roles were assigned at **container** scope (`lab-data`), not subscription or account.
- Blob operations used `--auth-mode login` (Microsoft Entra + RBAC), not the storage account key.
- The client secret was never printed, screenshotted, or committed.
- The service principal and the whole resource group were deleted after evidence was captured.

## What this is not

This is a same-day disposable lab. It is not a production identity design.

A real workload would usually:

- Prefer a **managed identity** or **workload identity** over a long-lived client secret.
- Have an owner for the role assignment, plus an access-review cycle.
- Log data-plane and control-plane activity and alert on unexpected writes.
- Treat role upgrades (Reader → Contributor) as a change that needs approval, not a casual CLI step.
- Keep break-glass access documented and rare.

The lab still demonstrates the part that interviews actually probe: **scope, effective permissions, and a negative test**.

## Decision trade-offs

**Separate service principal vs admin identity.**  
Admin tests prove nothing. The admin can already write. A dedicated principal makes the denial meaningful.

**Container scope vs account/subscription scope.**  
A subscription-wide data role would have made the lab easier and the least-privilege claim weaker.

**Azure CLI + `--auth-mode login` vs Portal clicks or account keys.**  
Keys bypass the RBAC story. Portal screenshots are easy to fake-read. CLI output shows the operation, the identity mode, and the exact deny/allow result.

**Standard LRS, deleted the same day.**  
The objective was RBAC, not durability or DR. Leaving the account up would only add cost.

## Publishing rules used here

Not committed:

- service-principal passwords
- storage account keys
- access tokens
- `.env` / `.azure` cache files
- downloaded blob copies (`downloaded-*.txt`)

The Azure Portal app-registration screenshot is in `evidence/screenshots/` as extra identity proof. The README uses the CLI check instead because it does not display account email.
