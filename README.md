# MajorKey Horizon Ops — the Entra admin cockpit

**Sit above your Entra tenant. Make Entra dramatically easier to administer.**

Horizon Ops is a Microsoft Graph–based operator surface that turns the daily Entra admin experience from blade-bouncing into a 9-workspace cockpit. Your tenant remains the system of record. Conditional Access still evaluates in Entra. PIM still activates in Entra. Identities still live in Entra. Horizon Ops is the cockpit on top — same surfaces (users, groups, devices, CA, identity protection, app regs, governance, sign-ins, audit, lifecycle, provisioning, Entra Connect, plus all the things Entra never built), dramatically better operator experience.

## Why the cockpit pattern

- **No migration.** Your data never leaves Entra. Revoke Horizon Ops's app registration consent anytime — your tenant is unchanged.
- **No retraining of Entra.** Operators learn Horizon Ops; everything they configure still flows through Graph into the same Entra tenant.
- **No vendor lock-in.** Microsoft remains the system of record. Horizon Ops is the workspace.
- **Things Entra will never build.** Identity graph, automation hub, mobile triage, persona view-as, before/after diffs on every change, ⌘K palette, AI Copilot grounded in your tenant, scheduled Copilot digests, native Slack/Teams/PagerDuty webhooks.

## What it gives you over the bare Entra admin portal

| | Entra admin | Horizon Ops |
|---|---|---|
| Top-level nav items | 50+ blades | 9 workspaces |
| Click-through to drill | 3-5 clicks | 1 tab swap inside workspace |
| Keyboard shortcuts | None | ⌘K palette · ⌘/ Copilot · `g+letter` jump · `?` overlay |
| User profile context | Spread across 4-6 blades | One drawer · 6 tabs · timeline |
| Automation visibility | Implicit · scattered | First-class workspace · 482 auto-actions/24h · ROI dashboard |
| Logs centralization | 7 destinations | One Activity workspace · 8 sibling tabs |
| Before/after diffs | None | On every change · role · entitlement · attribute · sync |
| AI Copilot | Separate add-on | Built-in · grounded in tenant data |
| Identity graph | None | Visual node/edge view |
| Mobile triage | Desktop-shrunk | Phone-optimized layout |
| Persona view-as | None | Admin / Helpdesk / Auditor / Approver |
| Webhooks out | LAW/Sentinel/Storage/EH | Plus Slack · Teams · ServiceNow · PagerDuty · custom |
| Saved views + scheduled digests | None | 14 saved views · 5 cadences · Copilot-generated |
| Dark mode + PWA install | No | Yes |
| Print stylesheet | Inconsistent | Cmd+P from any workspace → clean PDF |

Themed for a SLED tenant ("Cascadia State · cascadia.gov") out of the box. Open `index.html` directly in a browser to demo with sample data, or wire to MSAL.js + Microsoft Graph (instructions below) to run live against any real Entra tenant.

## Demo flow suggestions

1. **Dashboard** — open the show. Point out KPIs (joiners/movers/leavers/failures), 24h sign-in volume, provisioning donut, system health.
2. **Identities → click any user** — show the 6-tab drawer; the **Role changes** tab on Maya Patel-Singh shows a live before/after diff produced by the Mover workflow.
3. **Lifecycle** — switch between Joiner / Mover / Leaver canvases; the featured before/after panel and triggers table tell the source-of-truth story (Workday → Horizon).
4. **Provisioning** — show the inbound pipeline DAG, then the Outbound connector table, then the **Attribute mappings** subtab to surface Workday → Entra transforms.
5. **Entra Connect** — agent + configuration health, plus on-prem AD ↔ Entra before/after for a single object.
6. **Data Factory** — visualize the `hr-to-entra-inbound` pipeline that drives JML.
7. **Governance** — access review campaigns table, plus the before/after panel for the Q1 Treasury Package review (3 users removed, 1 app removed, cycle changed annually → quarterly).
8. **Audit & Logs → Entitlement changes tab** — the SOX/CJIS evidence story: every entitlement change with before/after and net delta.
9. **Sign-in Logs** — risky sign-in row (Toronto from Olympia user) and a blocked attempt from Lagos.

## Files

- `index.html` — the entire app (HTML + CSS + JS, ~5,500 lines)
- `CHANGELOG.md` — release notes
- `README.md` — this file

## Connecting to a real Entra tenant (live mode)

The portal runs against sample data out of the box. To attach it to any production Entra tenant:

### 1. Register an Entra app

In your tenant's Microsoft Entra admin center → App registrations → New registration:
- Name: `Horizon Ops Portal`
- Supported account types: Single tenant (or multi-tenant if needed)
- Redirect URI: `Single-page application` → `http://localhost:8080` (or your hosting URL)

After creation, copy the **Application (client) ID** and **Directory (tenant) ID**.

### 2. Grant Microsoft Graph permissions

In the app registration → API permissions → Add permission → Microsoft Graph → Application permissions, add:

**Read-only (recommended starting point):**
- `User.Read.All`, `Group.Read.All`, `Directory.Read.All`
- `AuditLog.Read.All`, `Policy.Read.All`
- `IdentityRiskEvent.Read.All`, `Application.Read.All`
- `EntitlementManagement.Read.All`

**Optional write (only if using portal write actions):**
- `User.ReadWrite.All`, `Group.ReadWrite.All`

Then click **Grant admin consent for &lt;tenant&gt;**.

### 3. Wire up MSAL.js + Graph client

Add to the bottom of `index.html` (before `</script>`):

```js
import { PublicClientApplication } from 'https://cdn.jsdelivr.net/npm/@azure/msal-browser/+esm';
import { Client } from 'https://cdn.jsdelivr.net/npm/@microsoft/microsoft-graph-client/+esm';

const msal = new PublicClientApplication({
  auth: {
    clientId: '<your-app-registration-id>',
    authority: 'https://login.microsoftonline.com/<your-tenant-id>',
    redirectUri: window.location.origin
  }
});
const result = await msal.loginPopup({
  scopes: ['User.Read.All', 'AuditLog.Read.All', 'Policy.Read.All']
});
const graph = Client.init({
  authProvider: async (done) => {
    const t = await msal.acquireTokenSilent({ scopes: ['.default'], account: result.account });
    done(null, t.accessToken);
  }
});

// Replace sample arrays with live Graph queries
window.USERS  = (await graph.api('/users').select('displayName,mail,jobTitle,department,officeLocation').top(999).get()).value;
window.SIGNINS = (await graph.api('/auditLogs/signIns').top(50).get()).value;
window.AUDIT  = (await graph.api('/auditLogs/directoryAudits').top(50).get()).value;
window.GROUPS = (await graph.api('/groups').top(100).get()).value;
window.CA_POLICIES = (await graph.api('/identity/conditionalAccess/policies').get()).value;
window.RISKY_USERS = (await graph.api('/identityProtection/riskyUsers').top(50).get()).value;
window.ENT_APPS = (await graph.api('/servicePrincipals').top(100).get()).value;
window.APP_REGS = (await graph.api('/applications').top(100).get()).value;
window.WORKLOADS = (await graph.api('/servicePrincipals').filter('servicePrincipalType eq \\'Application\\'').top(100).get()).value;
window.DEVICES = (await graph.api('/devices').top(100).get()).value;
window.GUESTS = (await graph.api(`/users?$filter=userType eq 'Guest'`).top(50).get()).value;

// Re-render current route to pick up live data
navigate(currentRoute);
```

The `Connect tenant` workspace inside the app (`#connect`) has the full UI walkthrough plus a copy-pasteable code block.

### 4. CORS / hosting

For production, host behind any static-file server (S3, Azure Static Web Apps, Netlify, GitHub Pages) and add the URL as an additional Redirect URI in your Entra app registration.

## Branding to swap for a different tenant

Search `index.html` for:
- `Cascadia State` → tenant display name
- `cascadia.gov` → primary domain
- `--navy` / `--mint` CSS variables → brand colors
- `USERS`, `CONNECTORS`, `ENTITLEMENTS`, `REVIEWS`, `SIGNINS`, `AUDIT`, `ROLE_HISTORY` arrays → demo data
