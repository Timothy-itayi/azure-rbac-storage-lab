# CLI evidence

These are the data-plane proofs. The [README](../README.md) is the brief. The [walkthrough](walkthrough.md) has the commands.

Tests used `--auth-mode login` as service principal `sp-rbac-storage-lab-a669e7`. Scope was container `lab-data` on `strbaclab669e7`.

## Storage was private

HTTPS-only. Public blob access off. Container seeded by the admin identity, not the test principal.

![Storage account: HTTPS only, public blob access disabled](../evidence/screenshots/01-storage-account.png)

![Private container created and seed.txt uploaded](../evidence/screenshots/02-private-container-seed.png)

## Separate identity

Created with no role. IDs confirmed; secret not printed.

![Temporary service principal created without printing the secret](../evidence/screenshots/service-principle-AzureCLI.png)

## Reader scoped to one container

`Storage Blob Data Reader` on `lab-data` only.

![Storage Blob Data Reader assigned at container scope](../evidence/screenshots/04-reader-role-creation-assignment.png)

## Read succeeded

`seed.txt` downloaded as the service principal. Contents matched.

![Reader identity downloaded seed.txt](../evidence/screenshots/05-reader-read-success.png)

## Write was denied

Same identity, same container, same auth mode. Azure asked for Contributor or Owner.

![Reader upload denied](../evidence/screenshots/06-reader-write-denied.png)

## Contributor changed the result

Reader removed. `Storage Blob Data Contributor` assigned at the same scope. Upload of `contributor-write-test.txt` succeeded.

![Storage Blob Data Contributor assigned at the same container scope](../evidence/screenshots/07-contributer-role-assignment.png)

![Contributor upload succeeded](../evidence/screenshots/08-contributor-write-success.png)

```text
Reader      ->  read yes / write no
Contributor ->  read yes / write yes
```

## Cleanup

Service principal deleted. Resource group deleted. `az group exists` returned `false`.

![Service principal and resource group deleted](../evidence/screenshots/09-cleanup-complete.png)
