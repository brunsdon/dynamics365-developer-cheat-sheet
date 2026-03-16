# Power Pages

Power Pages is the Microsoft low-code platform for building externally-facing websites backed by Dataverse. It was formerly known as Power Apps Portals.

## Key Concepts

- **Site** — the top-level container; one site per environment is typical
- **Web Pages** — hierarchical pages that make up the site structure
- **Web Templates** — Liquid-based templates that control page rendering
- **Content Snippets** — reusable blocks of text or HTML managed in Dataverse
- **Site Settings** — key/value configuration entries that control site behaviour
- **Entity Forms / Basic Forms** — single-table forms rendered on a page
- **Entity Lists / Basic Lists** — filtered, paginated views of Dataverse records
- **Advanced Forms (Web Forms)** — multi-step forms with conditional logic
- **Table Permissions** — control what Dataverse data website users can access
- **Web Roles** — group portal users and assign table permissions and page access

## Architecture Overview

```
┌─────────────────────────────────────────────────┐
│                  Browser / User                 │
└────────────────────────┬────────────────────────┘
                         │ HTTPS
┌────────────────────────▼────────────────────────┐
│               Power Pages Site                  │
│   Web Pages │ Web Templates │ Content Snippets  │
├─────────────────────────────────────────────────┤
│            Liquid Template Engine               │
│   fetchxml │ entitylist │ entityform tags        │
├─────────────────────────────────────────────────┤
│                  Dataverse                      │
│   Tables │ Table Permissions │ Web Roles        │
└─────────────────────────────────────────────────┘
```

## Authentication Options

| Method                        | Notes                                          |
|-------------------------------|------------------------------------------------|
| Local (username/password)     | Built-in, not recommended for production       |
| Azure AD (Entra ID)           | Common for internal/staff-facing portals       |
| Azure AD B2C                  | Common for external/public-facing portals      |
| Microsoft/Google/LinkedIn     | Social identity providers via Azure AD B2C     |
| Custom OAuth 2.0              | Configurable for non-standard identity systems |

## Table Permissions

Table permissions are the primary security mechanism. Without an explicit permission, anonymous and authenticated users cannot read or write any Dataverse data.

```
Scope options:
  Global      — applies to all records of the table
  Contact     — applies to records owned by or related to the logged-in contact
  Account     — applies to records owned by or related to the contact's account
  Parent      — inherits scope from a parent table permission
  Self        — contact can only access their own contact record
```

### Typical Permission Setup

```
Web Role: Authenticated Users
  └── Table Permission: Project Requests
        Scope: Contact
        Privileges: Read, Create, Write
        Relationship: prefix_projectrequest_contact (contact lookup)

Web Role: Administrator
  └── Table Permission: Project Requests
        Scope: Global
        Privileges: Read, Create, Write, Delete, Append, AppendTo
```

Always test permissions as an authenticated non-admin user and as an anonymous user.

## Liquid Templates

Liquid is the templating language used in Web Templates and Content Snippets. It allows dynamic rendering of Dataverse data.

### Render a FetchXML Query

```liquid
{% fetchxml my_query %}
<fetch top="10">
  <entity name="prefix_projectrequest">
    <attribute name="prefix_name" />
    <attribute name="prefix_status" />
    <filter>
      <condition attribute="statecode" operator="eq" value="0" />
    </filter>
    <order attribute="createdon" descending="true" />
  </entity>
</fetch>
{% endfetchxml %}

{% for item in my_query.results.entities %}
  <p>{{ item.prefix_name }} — {{ item.prefix_status }}</p>
{% endfor %}
```

### Access the Current User

```liquid
{% if user %}
  <p>Welcome, {{ user.fullname }}</p>
  <p>Email: {{ user.emailaddress1 }}</p>
{% else %}
  <p>You are not signed in.</p>
{% endif %}
```

### Content Snippets

```liquid
{{ snippets["Header/Welcome Message"] }}
```

### Site Settings in Liquid

```liquid
{% assign max_items = settings["prefix/MaxResultsPerPage"] | integer %}
```

## Basic Form Tips

- one Basic Form maps to one Dataverse table
- set **Mode** to Insert (create), Edit (update), or ReadOnly
- tab and section layout follows the Dataverse form definition
- use **Entity Form Metadata** to customise field labels, tooltips, and validation without changing the Dataverse form
- redirect on success using the **On Success** setting (URL or page)

## Advanced Forms (Multi-Step)

```
Step 1: Contact Details        → Basic Form (contact, Insert)
Step 2: Request Information    → Basic Form (prefix_projectrequest, Insert)
Step 3: Review & Submit        → Basic Form (ReadOnly)
Step 4: Confirmation           → Redirect to thank-you page
```

Each step can have condition rules to skip or branch based on previous answers.

## JavaScript on Pages

JavaScript can be added per-page via **Web Page > Custom JavaScript** or via a Web Template. The standard Dataverse form client API is not available — use standard DOM manipulation and the portal's jQuery instance.

```javascript
// Disable a field on a Basic Form
$(document).ready(function () {
  $("#prefix_estimatedvalue").prop("disabled", true);
});

// Show/hide a section based on a field value
$("#prefix_requesttype").on("change", function () {
  var val = $(this).val();
  if (val === "100000001") {
    $("#section_additionaldetails").show();
  } else {
    $("#section_additionaldetails").hide();
  }
});
```

## Site Settings Reference

Common site settings to know:

| Setting Key                                   | Purpose                                      |
|-----------------------------------------------|----------------------------------------------|
| `Authentication/Registration/Enabled`         | Enable/disable new user self-registration    |
| `Authentication/Registration/RequiresApproval`| Require admin approval before access granted |
| `HTTP/supresswwwredirect`                     | Suppress www redirect                        |
| `Webapi/prefix_table/enabled`                 | Enable Web API for a specific table          |
| `Webapi/prefix_table/fields`                  | Comma-separated columns exposed via Web API  |
| `Search/Enabled`                              | Enable/disable portal search                 |
| `Profile/ShowReadOnlyContactForm`             | Show read-only profile page                  |

## Portal Web API

Power Pages exposes a restricted client-side Web API for authenticated users. It must be explicitly enabled per table via site settings.

```javascript
// GET records (client-side, requires table permission)
fetch("/api/data/v9.2/prefix_projectrequests?$select=prefix_name,prefix_status", {
  headers: {
    "__RequestVerificationToken": $('input[name="__RequestVerificationToken"]').val()
  }
})
.then(res => res.json())
.then(data => console.log(data.value));

// POST a new record
fetch("/api/data/v9.2/prefix_projectrequests", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "__RequestVerificationToken": $('input[name="__RequestVerificationToken"]').val()
  },
  body: JSON.stringify({
    "prefix_name": "New Request",
    "prefix_status": 100000000
  })
});
```

Always include the anti-forgery token (`__RequestVerificationToken`) on mutating requests.

## Deployment Considerations

Power Pages configuration records live in Dataverse and are included in solutions like any other component.

```
Solution components to include:
  ✓ Web Pages
  ✓ Web Templates
  ✓ Content Snippets
  ✓ Site Settings
  ✓ Web Roles
  ✓ Table Permissions
  ✓ Entity Forms / Entity Lists
  ✓ Advanced Forms

Post-deployment steps:
  [ ] Verify site settings are correct for target environment
  [ ] Update any environment-specific site settings (URLs, keys)
  [ ] Confirm identity provider configuration (Azure AD app registrations differ per env)
  [ ] Test anonymous and authenticated user access
  [ ] Confirm table permissions are active
```

## Common Problems

- table permissions not configured — users see no data or get access denied errors
- web role not assigned to authenticated users web role — authenticated users get anonymous experience
- site settings referencing wrong environment URLs after promotion
- Azure AD B2C app registration not updated for new environment URL
- Liquid errors silently returning blank content — enable diagnostic mode in development
- Basic Form not saving — check table permissions include Create/Write for the web role
- CAPTCHA not displaying — check reCAPTCHA site settings are present and valid

## Useful Tips

- use the **Power Pages Design Studio** for low-code layout editing
- use **Visual Studio Code with the Power Platform extension** for editing Web Templates and Liquid directly
- enable **portal diagnostics** in development by appending `?diagnostic=true` to the URL (requires admin web role)
- clear the portal cache after deploying configuration changes: navigate to `/_services/about` and select **Clear Cache** (requires admin)
- avoid storing secrets in Content Snippets or Site Settings — use Azure Key Vault references where supported
