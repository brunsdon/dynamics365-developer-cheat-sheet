# Solution Management

Solution management is central to delivering Dynamics 365 changes safely across environments.

## Key Concepts

- unmanaged solutions
- managed solutions
- publishers
- patches
- versioning
- environment variables
- connection references

## Practical Guidance

- use consistent publisher strategy
- keep solution boundaries intentional
- avoid unmanaged drift in shared environments
- version changes clearly
- document deployment prerequisites
- understand dependencies before release

## Good Habits

- separate experimental work from releasable assets
- include related components deliberately
- validate app, flow, and connection dependencies
- use environment variables for environment-specific values
- keep release notes for each deployment

## Common Problems

- missing dependencies
- unmanaged customisations in target environments
- connection references not set correctly
- incorrect layering assumptions
- unclear rollback plan
- solution import succeeds but runtime configuration is incomplete

## Questions Before Release

- what exactly changed?
- what depends on it?
- is post-deployment configuration required?
- are flows turned on?
- are connection references valid?
- are security roles and access impacts understood?