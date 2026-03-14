# DevOps

Dynamics 365 delivery benefits from disciplined DevOps practices even when some assets are low-code.

## Core Areas

- source control
- solution export and unpack
- build validation
- environment promotion
- release governance
- deployment documentation

## Practical Guidance

- keep solution assets in source control
- automate exports and packaging where possible
- document deployment order
- validate environment variables and connection references
- include smoke-test steps after deployment
- track solution versions properly

## Useful Pipeline Concerns

Think about:

- build agent requirements
- authentication to environments
- managed versus unmanaged build outputs
- release approvals
- deployment sequencing
- rollback or remediation steps

## Good Delivery Practice

A strong Dynamics delivery process should include:

- source-controlled solution assets
- repeatable build steps
- repeatable deployment steps
- release notes
- post-deployment validation
- operational ownership

## Common Delivery Failures

- deployment succeeds but app is unusable
- flows are inactive after import
- connection references point nowhere
- environment variables are missing
- undocumented manual steps break repeatability
- solution layering causes unexpected behaviour