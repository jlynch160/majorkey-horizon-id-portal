# MajorKey Horizon Ops — Changelog

## v0.5.0 — 2026-05-05 — Governance > Configuration tab (operating-model view)

New `Configuration` subtab inside the Governance workspace (set as the default landing tab). Inspired by the Oxford Computer Group / IDXchange Identity Governance Status reference architecture. Two parts:

### Operating-model grid (top)
6 column-cards laid out across the workspace, each rendering an Entra operating-model concern with sub-items pulled from live tenant data:
- **Entra Entities** — identities, accounts, groups, roles, guests
- **Applications** — enterprise apps, app regs, workload identities, inbound provisioners, app roles
- **Connectors** — Workday HRIS, on-prem AD Cloud Sync, SCIM outbound, Data Factory, custom REST/IDXchange
- **Workflows** — LWF count, Joiner/Mover/Leaver auto-counts (30d), custom JML scripts
- **Governance** — active reviews, configured reviews, entitlement packages, SoD policies, certifications
- **Audit** — audit events 24h, provisioning logs, entitlement changes, policy/CA changes, reports library

Each card uses an accent color, an icon, and is fully clickable — clicking any card navigates to the matching workspace.

### Identity Governance status panel (bottom)
Three-column layout matching the reference:
- **Tenant shape** — KPIs with mini bar charts (identities, guests, privileged roles, groups & teams, applications)
- **Centered SVG donut** with gradient stroke + radial wash showing 78% governance posture coverage; legend under it
- **Your ID Governance configurations** — clickable rows for LWFs, access reviews, app-role apps, privileged-role identities, access packages, SoD policies; each row deep-links to the relevant workspace

Includes a `View technical information` toggle that reveals Activity ID / Job ID / Provisioning API endpoint / last sync — same details surfaced by the Microsoft Entra Identity Governance dashboard.

### CSS additions
- `.opmodel-grid` / `.opmodel-card` — responsive 6→3→2 column grid with accent-bar header, hover lift, dashed-line items
- `.ig-status-grid` / `.ig-stat` / `.ig-bar` / `.ig-cfg` — three-column status panel with mini bar charts and clickable config rows
- `.ig-donut` — pure SVG donut (no chart lib) with linear + radial gradients
- Dark-mode adjustments for all of the above

## v0.4.2 — 2026-05-04 — Rebrand to Horizon Ops

Renamed **Horizon ID** → **Horizon Ops** across the portal, README, manifest, and all copy. Sidebar now reads `MajorKey / HORIZON OPS`. Reflects the operator-surface positioning (482 auto-actions/day, automation-first workspace, 9-workspace cockpit) better than the identity-only framing — the portal manages CA, PIM, governance, lifecycle, sign-ins, and audit, not just identity. PWA install name updated. No functional changes.

## v0.4.1 — 2026-05-04 — Rebrand to Horizon ID

Renamed **Horizon One** → **Horizon ID** across the portal, README, manifest, and all copy. Sidebar reads `MajorKey / HORIZON ID`. Folder name (`majorkey-horizon-id-portal`) and PWA install name updated. No functional changes — pure rename to align with SLED-facing product positioning.

## v0.4.0 — 2026-04-30 — Workspace redesign

Pivot away from "Entra-portal lookalike" toward a streamlined, opinionated workspace experience.

### Nav reduced from 32 items to 9
**Top of nav (always visible):**
- Pulse · People · Security · Apps · Connect · Automation · Activity

**Bottom of nav:**
- Approvals · Alerts · Settings · Quick search (⌘K)

All previously top-level routes (29 sub-modules) are now reached via in-page tab bars under their parent workspace. The active nav item highlights the parent workspace regardless of which sub-module you're viewing.

### Hero workspace headers
Each workspace lands on a gradient hero: navy → mid-blue with mint/teal radial glows, "Live" pulse-dot eyebrow, large title, contextual description, and 4 inline KPIs pulled from real data — no more dense top-of-page header rows.

### Module tab bar
A pill-style tab bar replaces the prior nav drill-down. Sits directly under the hero. Each tab is a real route — they navigate, deep-link via hash, and stay highlighted independently of the parent.

### Sidebar refresh
- Soft navy → deep navy vertical gradient with mint glow accent
- Active item: green left rail with subtle glow + inset ring
- Quick search button anchored at the bottom always one click away

### Active highlight + route map
- New `NAV_HIGHLIGHT` map: any sub-route resolves to a primary nav item, so navigation feels like a tab change inside a workspace, not a redirect to an unrelated page.

## v0.3.0 — 2026-04-30 — Automation-first + remaining Entra surfaces

Goal: close the gap to a true Entra-portal replacement by adding the surfaces that an admin reaches for daily, and surface every always-on workflow in one place so the operator can *see* the automation doing its job.

### New modules
- **Automation hub** — top-level surface listing every always-on workflow (JML, ADF inbound, Cloud Sync, outbound SCIM, risk-based remediation, access reviews, license reclaim, secret rotation, stale-guest cleanup, SoD enforcement) with run counts; recent auto-actions feed; suggested "turn manual into auto" candidates with hours-saved estimate.
- **Recommendations** — Identity Secure Score (82/100), 9 open items, auto-remediable badges, one-click auto-fix buttons.
- **Roles & administrators** — directory role catalog with Tier 0/1/2/3 classification, active vs PIM-eligible counts, custom role.
- **Named locations** — IP ranges + country lists, trusted/untrusted, auto-updated lists, CA reference count.
- **Authentication strengths** — built-in + custom strengths catalog with allowed methods and CA usage count.
- **Lifecycle Workflows (Entra-native)** — 8 LWFs separate from custom JML scripts, trigger/scope/tasks/success-rate columns.

### Wiring
- Top-bar bell now navigates to **Alerts**.
- Top-of-nav now leads with the action surfaces: Dashboard → **Automation** (badge: 482) → **Recommendations** (badge: 9) → Approvals → Alerts → Sign-ins → Audit.
- ⌘K palette adds a dedicated "⚡ Auto-actions" section: auto-remediate all recs, auto-rotate secrets, auto-disable inactive accounts, auto-reclaim licenses.
- Security group expanded to 8 items (added Auth strengths, Named locations, Roles & administrators).
- People group adds Lifecycle Workflows alongside JML.

## v0.2.0 — 2026-04-30 — Entra admin portal expansion

Goal: replace the Microsoft Entra admin portal with a streamlined, Horizon-branded experience. Adds 15 new modules covering the full identity-admin surface, with drill-down detail drawers and a global command palette.

### New modules
- **Approvals** — unified queue for PIM activations, access packages, JML approvals, guest invites; click row → drawer with risk signals + before/after impact diff.
- **Alerts** — operational + security alert center with severity, source, recommended actions; bell badge in topbar.
- **Groups** — 12 sample groups (security / M365 / dynamic / role-assignable); drawer shows membership rule, sample members, downstream usage.
- **Devices** — Windows / macOS / iOS / Android inventory with compliance state; drawer surfaces hardware, encryption, patch level, compliance policy results.
- **Guests / B2B** — guest list with sponsor + expiration, cross-tenant access settings.
- **Enterprise apps** — 12 SAML/OIDC apps with users, sign-ins, secret expiry status.
- **App registrations** — secrets/cert/federated credential view with red-amber-mint expiry chips.
- **Workload identities** — service principals + managed identities, including one orphaned/high-risk for the demo.
- **Conditional Access** — 12 policies covering legacy auth block, MFA, geo-block, FIDO2, CJIS-strict, token-protection report-only; drawer shows assignments, controls, 7d impact, JSON.
- **Authentication methods** — tenant policy, method usage chart, per-user methods table, MFA gap callouts.
- **Identity Protection** — risky users + sign-in/user risk policy summary, auto-remediation stats.
- **Password protection** — banned-terms list, smart lockout activity.
- **Token protection (CAE)** — CAE signal subscriptions, recent revocation events with sub-5s propagation.
- **Reports** — license utilization, inactive users, MFA gaps cards + standard reports library.
- **Licenses** — 5-SKU breakdown with utilization bars + annual cost forecast.
- **SSPR** — registration %, method coverage, recent reset events.

### Navigation
- Restructured left nav into **Entra-style groups**: Manage, Applications, Protect, Govern, Sync, Insights, System.
- Added top-level Approvals + Alerts items with live count badges.

### Visual polish
- Streamlined typography: tabular numerals across KPIs and tables, less mono noise, sans-serif body for diff values.
- Tighter table density (52px header), hover affordance for clickable rows.
- Wider drawer (720px) with responsive collapse for diff pairs at narrow widths.
- KPI tiles: consistent height, delta line pinned to bottom for easy scan.

### Interactions
- **Command palette (⌘K)** — fuzzy search across modules, actions, and 15 sample users; keyboard navigation (↑↓/↵/esc); clicking the topbar search opens it.
- Drawer close on Esc; drawer click-out backdrop.

### Reusable detail-drawer pattern
- New `openDetail(opts)` helper used by Approvals, Alerts, Conditional Access, Devices, Groups; identity drawer retained.

## v0.1.0 — 2026-04-30 — initial build

Single-file light-mode SPA modeled as the MajorKey Horizon Ops + Entra Admin Portal experience for a SLED tenant (Cascadia State).

### Modules
- **Dashboard** — KPIs, 24h sign-in chart, provisioning donut, recent activity feed, system health, top apps, risky sign-ins, open access reviews.
- **Identities** — directory of 15 sample users with filters; clickable rows open a 6-tab profile drawer (Overview, Attributes, Access, Role changes, Sign-ins, Audit).
- **Lifecycle (JML)** — three workflow canvases (Joiner / Mover / Leaver) with stepwise state, featured Mover before/after diff, in-flight workflow table, and trigger map.
- **Provisioning** — Inbound (Workday → Entra) pipeline visualization, sample identity before/after, Outbound connector table (11 SaaS), attribute mapping table (14 mappings + transforms), run history.
- **Entra Connect / Cloud Sync** — agent health, configuration scopes, on-prem AD ↔ Entra before/after for sample object, recent delta/full sync events.
- **Connectors** — 14-card gallery covering HRIS, directory, ITSM, SaaS, custom SCIM with health dots and metadata.
- **Data Factory** — pipeline DAG for `hr-to-entra-inbound`, all-pipelines table, recent run history.
- **Governance** — Access Reviews (5 campaigns + before/after for Q1 Treasury Package), Entitlement Packages (6), PIM eligible roles, SoD policies.
- **Sign-in Logs** — Entra-style sign-in viewer with filters and Conditional Access column.
- **Audit & Logs** — 5 tabs: Audit, Provisioning, Lifecycle, Entitlement changes (before/after table), Policy/CA changes.
- **Settings** — tenant info, administrators, branding, API integrations.

### Design
- Light mode, MajorKey navy + mint accent, Inter type, soft shadows, rounded cards, status pills.
- Hash-based routing (`#dashboard`, `#identities`, etc.), keyboard `⌘K` to focus search, `Esc` closes drawer.
- Responsive collapse to icon nav under 900px.
- 0 external dependencies beyond Google Fonts; everything else self-contained in `index.html`.
