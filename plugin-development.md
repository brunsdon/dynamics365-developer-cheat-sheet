# Plugin Development

Plugins are used to enforce server-side business logic in Dynamics 365 and Dataverse.

## When Plugins Are Useful

Plugins are commonly used when you need:

- synchronous server-side validation
- automatic field updates
- creation of related records
- enforcement of business rules
- integration triggers
- logic that should not rely on client-side behaviour

## Common Pipeline Stages

Typical stages include:

- PreValidation
- PreOperation
- PostOperation

Choose the stage based on what the logic must do and when it must happen.

## Good Plugin Practices

- keep plugin logic focused
- avoid overly large "god classes"
- trace meaningful diagnostic information
- validate required input early
- handle recursion carefully
- minimise unnecessary service calls
- extract reusable logic into separate classes where appropriate

## Things to Watch

- recursive updates
- performance issues
- transaction impact
- unexpected triggering from imports or integrations
- multiple plugins interacting on the same message

## Registration Considerations

Think about:

- message
- primary table
- stage
- filtering attributes
- execution mode
- deployment approach

## Typical Use Cases

- calculate derived values on create or update
- enforce consistency rules
- create or update related records
- call downstream services through controlled integration patterns
- validate status transitions

## Avoid When Possible

A plugin may not be the best choice when:

- simple no-code configuration is enough
- process belongs in Power Automate
- logic is long-running
- integration requires resilient async processing better suited to Azure or queue-based patterns