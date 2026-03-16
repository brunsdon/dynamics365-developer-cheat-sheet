# XrmToolBox — Favourite Plugins

[XrmToolBox](https://www.xrmtoolbox.com/) is a free Windows application that hosts a large library of community-built tools for Dynamics 365 and Dataverse administration, development, and diagnostics.

## Getting Started

Download from [xrmtoolbox.com](https://www.xrmtoolbox.com/) and connect using an existing Dynamics 365 / Dataverse connection string or interactive login.

```
Connection options:
  - Microsoft Login (OAuth, MFA supported)
  - Connection String
  - Client ID / Secret (service principal)
```

---

## Schema & Data Tools

### FetchXML Builder
**By:** Jonas Rapp

Build and test FetchXML queries visually. Supports converting OData queries and exporting results. Essential for writing queries used in plugins, SSRS reports, and integrations.

```xml
<!-- Example FetchXML output from the builder -->
<fetch top="50">
  <entity name="contact">
    <attribute name="fullname" />
    <attribute name="emailaddress1" />
    <filter>
      <condition attribute="statecode" operator="eq" value="0" />
    </filter>
    <order attribute="fullname" ascending="true" />
  </entity>
</fetch>
```

---

### Metadata Document Generator
**By:** DamSim

Generates Word or Excel documentation of your Dataverse schema — tables, columns, relationships, and option sets. Useful for onboarding and as-built documentation.

---

### Entity Relation Diagram Creator
**By:** DamSim

Generates visual ER diagrams of selected tables and their relationships. Export to image or Visio. Good for solution design reviews.

---

### Attribute Manager
**By:** MscrmTools (Tanguy Touzard)

Bulk-manage columns across multiple tables — rename display names, change requirement levels, manage audit settings at scale instead of one field at a time.

---

## Security & Access Tools

### Security Role Manager
**By:** MscrmTools

Visual editor for security roles. Compare roles side by side, copy privileges between roles, and bulk-edit table-level privileges without clicking through hundreds of rows in the UI.

---

### Access Checker
**By:** MscrmTools

Test exactly what a specific user can and cannot do — retrieve, create, update, delete — against any table. Invaluable for diagnosing access issues without impersonating the user.

---

### User Settings Utility
**By:** MscrmTools

Bulk-update user personal settings (language, timezone, UI preferences) across multiple users in one operation.

---

## Deployment & Configuration Tools

### Solution Component Mover
**By:** Rui Romão

Move or copy components between solutions within the same or different environments. Useful when solutions need restructuring without manual re-adding.

---

### Environment Variables Manager
**By:** P. Nowak

View, edit, and manage environment variable values across environments directly — faster than navigating the maker portal for bulk updates.

---

### Connection References Manager
**By:** P. Nowak

Manage and update connection references across solutions. Useful post-deployment when connections need updating in target environments.

---

### Portal Records Mover
**By:** DamSim

Move Power Pages / Portal configuration records between environments — pages, web templates, content snippets, site settings.

---

## Data Tools

### Data8 Data Migration Assistant / Bulk Data Updater
**By:** Data8 / community

Perform bulk record updates directly from Excel or CSV without writing custom code. Good for data migrations, corrections, and mass updates.

---

### RCMRD Bulk Record Mover
**By:** community

Reassign or move records in bulk — change ownership, business unit, or team assignment across large record sets.

---

## Diagnostics & Profiling

### Plugin Trace Viewer
**By:** MscrmTools

Browse and search plugin trace logs directly — filter by plugin name, correlation ID, or time range. Much faster than the default UI for diagnosing plugin failures.

---

### Async Jobs Manager
**By:** MscrmTools

View, filter, and bulk-cancel system jobs. Essential for cleaning up stuck or failed async plugin jobs and bulk delete operations.

---

### Organisation Comparer
**By:** MscrmTools

Compare schema and customisations between two environments and highlight differences. Useful for drift detection between dev, test, and production.

---

### Governance Manager
**By:** community

Audit environment customisations — find unmanaged layers, orphaned components, and unused assets.

---

## Tips for Using XrmToolBox

- always test destructive operations against a non-production environment first
- keep the tool updated — plugins are maintained independently and update frequently via the Plugin Store
- check the **Plugin Store** tab inside XrmToolBox to discover and install new tools
- use the **Connection Pool** to keep multiple environment connections ready
- some tools require System Administrator or System Customizer roles — confirm access before use in production
