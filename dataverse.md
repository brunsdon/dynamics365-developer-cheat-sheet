# Dataverse

Dataverse is the core data platform underpinning Dynamics 365 and much of the Power Platform.

## Core Concepts

Common building blocks include:

- tables
- columns
- relationships
- business rules
- forms
- views
- choices
- security roles
- business process flows

## Design Principles

When designing Dataverse solutions:

- use clear table ownership and security design
- choose relationships carefully
- avoid unnecessary complexity in data model extensions
- separate configuration data from transactional data
- think about reporting and integration impacts early

## Table Design Tips

- use consistent naming conventions
- document purpose and ownership of custom tables
- avoid creating duplicate concepts across multiple tables
- keep required columns meaningful
- be careful with large text and file storage where not needed

## Relationship Design Tips

Think carefully about:

- 1:N relationships
- N:1 relationships
- N:N relationships
- cascading behaviour
- parent-child data ownership
- integration implications

## Common Mistakes

- overcustomising without documenting rationale
- putting too much process logic into data structure
- poor naming of schema objects
- creating fields that duplicate existing Dynamics functionality
- ignoring downstream reporting and integration needs

## Practical Questions to Ask

- who owns the record?
- who can read it?
- who can modify it?
- does this belong in Dataverse at all?
- will external systems need this data?
- should this logic be implemented in a plugin, flow, or external service?