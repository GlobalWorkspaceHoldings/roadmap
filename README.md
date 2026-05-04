# sgf.work Feature Inventory

A scannable index of every feature in the platform, grouped by surface area. One line per feature.

This is an internal reference for onboarding, scope conversations, and cleanup audits. Last reviewed against the codebase on 2026-05-04. Not part of the published docs build (`docs/_build.mjs` only renders `member-portal/` and `admin/` subdirs) — read this file directly on GitHub.

**Status column** is blank for normal/live features. Otherwise:
- `incomplete` — UI exists but the wiring or backend is partial
- `deprecated` — superseded but not yet removed
- `external` — handled outside this app, listed here so the boundary is explicit
- `flag` — worth a closer look (residual code, ambiguous ownership, etc.)

Anything flagged is also collected in [Flagged for follow-up](#flagged-for-follow-up) at the bottom.

---

## 1. Public site (`sgf.work`)

The marketing site. No login required. All pages live at the repo root.

| Feature | Route | What it does | Status |
|---|---|---|---|
| Homepage | `/` | Hero, member spotlight, primary CTAs | |
| Why sgf.work | `/why.html` | Pitch page; embeds the StatusPage incident widget | |
| Pricing | `/pricing.html` | Membership tier pricing and comparison | |
| Day passes | `/day-passes.html` | Buy a day pass via Stripe Checkout (upfront, not slot-based) | |
| Book a tour | `/book-a-tour.html` | Lead-capture form for prospective members | |
| Tour confirmed | `/tour-confirmed.html` | Post-submit thank-you page for tour requests | |
| Partnership | `/partnership.html` | Corporate / bulk-account inquiries | |
| Climate | `/climate.html` | Sustainability story | |
| Credits | `/credits.html` | Third-party attribution | |
| Terms | `/terms.html` | Terms of Service | |
| Privacy | `/privacy.html` | Privacy Policy | |
| Blog index | `/blog/` | Listing with tag filters and RSS link | |
| Blog post | `/blog/post.html?slug=…` | Individual post view | |
| Blog RSS | `/blog/feed.xml` | RSS feed | |
| Classifieds board | `/board/` | Public read-only listing of community classifieds | |
| Classified detail | `/board/post.html?id=…` | Individual ad page | |
| 404 | `/404.html` | Custom not-found page | |
| Offline | `/offline.html` | PWA offline fallback | |
| Accept invite | `/accept-invite.html` | Landing for staff/member invite emails | |
| Sign agreement | `/sign-agreement.html` | Standalone agreement-signing surface | |
| Checkout confirmed | `/checkout-confirmed.html` | Post-Stripe-Checkout landing | |
| Return | `/return.html` | Stripe-Checkout return-URL handler | |

---

## 2. Onboarding flow (`/onboarding/`)

Post-signup wizard that collects profile info, agreements, and a tour booking before the member lands in the portal.

| Step | Route | What it does | Status |
|---|---|---|---|
| Index | `/onboarding/` | Entry point, routes to the next unfinished step | |
| Welcome | `/onboarding/welcome.html` | Intro + plan confirmation | |
| Profile | `/onboarding/profile.html` | Name, photo, company, bio | |
| Preferences | `/onboarding/preferences.html` | Communication and visibility preferences | |
| Sign | `/onboarding/sign.html` | Member agreement signature | |
| Tour | `/onboarding/tour.html` | Schedule the in-person tour | |
| Complete | `/onboarding/complete.html` | Final hand-off to the dashboard | |

---

## 3. Member portal (`members.sgf.work`)

Authenticated member surface. Lives in `/members/`.

### Auth & account

| Feature | Route | What it does | Status |
|---|---|---|---|
| Login / signup | `/members/login.html` | Email + password or Google OAuth; signup tab on the same page | |
| Password reset | `/members/reset-password.html` | Supabase magic-link reset flow | |
| Profile | `/members/profile.html` | Name, email, phone, company, bio, avatar, social links | |
| Plans | `/members/plans.html` | View available tiers, current plan badge, upgrade path | |
| Invoices & billing | `/members/invoices.html` | Payment history; manage card via Stripe Billing Portal | |

### Dashboard & space

| Feature | Route | What it does | Status |
|---|---|---|---|
| Dashboard | `/members/index.html` | Greeting, day-pass widget, announcements, ID-card pickup banner, rearrangeable cards | |
| Booking | `/members/booking.html` | Calendar booking for hot desks, dedicated desks, meeting rooms, phone booths, private offices, compute desks, podcast studio, makerspace | |
| Space calendar | `/members/space-calendar.html` | Read-only availability view; companion to booking | |
| Calendar subscription | `/members/calendar.html` | iCal/CalDAV subscription instructions | |
| Day passes (member) | `/members/day-passes.html` | Buy day passes (upfront, not slot-based); list active/upcoming | |

### Community

| Feature | Route | What it does | Status |
|---|---|---|---|
| Member directory | `/members/directory.html` | Browse other members; searchable | |
| Community / classifieds | `/members/community.html` | Post classifieds ($5/post, 60-day run, moderated); manage own posts | |
| Bulletin board | `/members/bulletin-board.html` | One flyer per member on the shared cork-board | |
| Events | `/members/events.html` | Upcoming events with RSVP/ticket buttons | |
| Event ticket | `/members/ticket.html` | QR code + event details for a purchased ticket | |

### Cafe & rewards

| Feature | Route | What it does | Status |
|---|---|---|---|
| Cafe menu | `/members/cafe.html` | Browse menu, add to cart, checkout | |
| Cafe order detail | `/members/cafe-order.html` | Single-order status + pickup instructions | |
| Cafe order history | `/members/cafe-orders.html` | List of past orders | |
| Loyalty (stars) | `/members/loyalty.html` | Stars balance, milestones, redemption rules; earned via cafe | |

### Tools

| Feature | Route | What it does | Status |
|---|---|---|---|
| LinkedIn translator | `/members/linkedin-translator.html` | Anthropic-powered LinkedIn-profile-to-bio rewriter | |

---

## 4. Admin console (`/admin/`)

Staff-facing surface. Reachable at `https://sgf.work/admin/`. Role gating is enforced in client JS, in `netlify/functions/_lib/rbac.mjs`, and in SQL (RLS + `is_super_admin()`).

**Roles:** `owner`, `admin`, `manager`, `community`, `billing`, `front_desk` — all can reach `/admin/*`. The super-admin allowlist (only `sean@gwh.co` and `seanblowers13@gmail.com`) gates role mutation and integration management.

### Members & access

| Feature | Route | What it does | Required role |
|---|---|---|---|
| Admin home | `/admin/index.html` | Landing dashboard | any staff |
| Members list | `/admin/members.html` | Search, filter, view all members | any staff |
| Member detail | `/admin/member-detail.html` | Profile, history, actions, offboarding checklist (incl. UniFi step pointers) | any staff |
| Member statement (PDF) | from member detail | Generate a statement PDF for a member | billing, admin, owner |
| Member actions | API only | Pause/resume subscription, refunds, credits, one-off charges | owner, admin |
| Visitors | `/admin/visitors.html` | Guest check-in / checkout | front_desk and up |
| Agreements | `/admin/agreements.html` | View signed member agreements; resend requests | any staff |
| Agreement templates | `/admin/agreement-templates.html` | Manage T&C templates | admin, owner |
| Tier agreements | `/admin/tier-agreements.html` | Map plans to required agreements | admin, owner |
| Staff & roles | `/admin/staff.html` | Grant/revoke staff roles | super-admin only |

### Bookings & spaces

| Feature | Route | What it does | Required role |
|---|---|---|---|
| All bookings | `/admin/bookings.html` | List, filter, manually book/override | any staff |
| Space calendar | `/admin/space-calendar.html` | Calendar view of all rooms | any staff |
| Day passes (admin) | `/admin/day-passes.html` | Manage day-pass purchases | any staff |
| Spaces | `/admin/spaces.html` | Configure rooms/areas and availability | any staff |
| Blackouts | `/admin/blackouts.html` | Block dates/times when spaces are unavailable | any staff |
| Plan settings | `/admin/plan-settings.html` | Configure tiers and pricing | admin, owner |
| Packages | `/admin/packages.html` | Define service/product packages | admin, owner |

### Billing & accounting

| Feature | Route | What it does | Required role |
|---|---|---|---|
| Accounting | `/admin/accounting.html` | Revenue overview | billing, admin, owner |
| Invoices | `/admin/invoices.html` | View/manage Stripe invoices; regenerate or email | billing, admin, owner |

### Community moderation

| Feature | Route | What it does | Required role |
|---|---|---|---|
| Board moderation | `/admin/board-moderation.html` | Approve/reject pending classifieds; resolve reports | owner, admin, manager, community |
| Incidents | `/admin/incidents.html` | Log/track member incidents; export PDF | any staff |
| Flyer board | `/admin/flyerboard.html` | Manage community flyers | any staff |

### Communications & content

| Feature | Route | What it does | Required role |
|---|---|---|---|
| Campaigns | `/admin/campaigns.html` | Broadcast email to members | any staff |
| Announcements | `/admin/announcements.html` | Site-wide announcements shown in the dashboard | any staff |
| Email templates | `/admin/email-templates.html` | Manage reusable templates | any staff |
| Contacts | `/admin/contacts.html` | External outreach contact list | any staff |
| CRM & tours | `/admin/crm.html` | Tour leads pipeline; CRM workflow | any staff |
| Blog list | `/admin/blog.html` | Publish/unpublish posts | any staff |
| Blog editor | `/admin/blog-edit.html` | Compose with optional Anthropic AI draft | any staff |
| Events admin | `/admin/events.html` | Create/manage events calendar | any staff |
| Tasks | `/admin/tasks.html` | Internal task assignment | any staff |

### Cafe operations

| Feature | Route | What it does | Required role |
|---|---|---|---|
| Cafe products | `/admin/cafe-products.html` | Menu items, pricing, inventory | any staff |
| Cafe orders | `/admin/cafe-orders.html` | Barista/queue view of incoming orders | any staff |
| Cafe loyalty | `/admin/cafe-loyalty.html` | Manage loyalty/rewards rules | any staff |

### Platform config

| Feature | Route | What it does | Required role |
|---|---|---|---|
| Settings | `/admin/settings.html` | Global platform configuration | admin, owner |
| Integrations | `/admin/integrations.html` | Manage API keys and 3rd-party connections | super-admin only |
| Webhooks | `/admin/webhooks.html` | Configure outbound webhooks | super-admin only |
| Slack admin | `/admin/slack.html` | Slack workspace integration controls | super-admin only |
| Audit log | `/admin/audit.html` | All staff actions, searchable | any staff |
| Reports | `/admin/reports.html` | Revenue, occupancy, and member analytics | any staff |

---

## 5. Kiosk

On-site touch surfaces. Lives in `/kiosk/`. Has its own auth and a relaxed CSP (camera access).

| Feature | Route | What it does | Status |
|---|---|---|---|
| Member check-in | `/kiosk/checkin.html` | Self-serve check-in at the front door | |
| TV display | `/kiosk/tv.html` | Lobby TV: announcements, who's in, upcoming events | |

Supporting code: `kiosk/app/checkin.js`, `kiosk/app/tv.js`, `kiosk/lib/{api,auth,config,realtime,ui}.js`.

---

## 6. Integrations

Third-party services with real wiring in this repo (verified by file presence).

| Service | Purpose | Entry points |
|---|---|---|
| **Stripe** | Payments, subscriptions, customer billing portal. Checkout at `secure.sgf.work`. **No webhooks** (account banned — sync is poll-based). | `netlify/functions/create-checkout.js`, `billing-portal.mjs`, `subscription-status.mjs`, `dunning-poll.mjs` |
| **Supabase** | Postgres, auth (email + Google OAuth), realtime, storage. Live project: `vxjvaimuhikwaabzfsdd`. | `js/supabase-client.js`, `js/auth.js`, `migrations.sql` |
| **Resend** | Transactional + broadcast email. From: `@send.sgf.work`. Reply-to: `hello@sgf.work`. | `netlify/functions/send-email.mjs`, `send-broadcast.mjs` |
| **Twilio** | Outbound SMS (booking confirmations, reminders). REST API directly, no SDK; no-ops gracefully if env unset. | `netlify/functions/_lib/sms.mjs` |
| **Intercom** | In-app support chat in the member portal. HMAC user-hash for identity verification. | `js/intercom.js`, `netlify/functions/intercom-hash.mjs` |
| **Anthropic Claude** | LinkedIn translator + AI blog draft/tag. | `netlify/functions/linkedin-translator.mjs`, `blog-ai-draft.mjs`, `blog-ai-tag.mjs` |
| **Apple Wallet / PassKit** | Signed `.pkpass` membership cards, day-pass passes, event tickets. APNs push for pass updates. | `netlify/functions/_pass-builder.mjs`, `_daypass-pass-builder.mjs`, `_event-pass-builder.mjs`, `_lib/wallet-push.mjs`, `wallet.mjs`, `generate-pass.mjs` |
| **Google Analytics 4 + Google Ads** | Marketing analytics + conversion tracking. GA4 `G-Q58FRVCFHZ`, Ads `AW-17968652697`. | `assets/js/marketing.js` |
| **Slack** | Workspace integration; admin surface at `/admin/slack.html`. | `/admin/slack.html`, related Netlify functions |
| **Netlify** | Hosting, Functions, Edge Functions, image CDN. | `netlify.toml`, `netlify/functions/*`, `netlify/edge-functions/*` |

### External (handled outside this app)

| Service | What it owns | Why it's listed |
|---|---|---|
| **UniFi Access** | All physical door access AND printing/print quotas | external — referenced in admin offboarding checklist (`/admin/member-detail.html`); do **not** build printing features in this portal |

---

## 7. Platform & infrastructure

Capabilities that aren't user-facing pages but are real features.

| Capability | Files | Notes |
|---|---|---|
| **PWA / service worker** | `sw.js`, `manifest.json`, `offline.html` | Cache-first for assets, network-first for admin. `CACHE_VERSION` must be bumped on every cached-asset edit. |
| **RBAC** | `netlify/functions/_lib/rbac.mjs` | Server-side role checks; super-admin allowlist (email-based) for sensitive mutations |
| **Row-level security** | `migrations.sql` | RLS policies on every member-touched table; service role bypasses for admin functions |
| **Scheduled jobs** | `netlify/functions/board-expire-cron.mjs`, `dunning-poll.mjs`, `dunning-suspend.mjs`, `tour-reminders.mjs` | Cron-driven: expire classifieds, poll Stripe for subscription state, enforce dunning, send tour reminders |
| **Public API + OpenAPI** | `netlify/functions/_api/router.mjs`, `_api/schemas/*.mjs`, `_api/routes/openapi.mjs` | Zod-validated `/api/v1/*` endpoints; OpenAPI spec is generated |
| **Edge functions** | `netlify/edge-functions/geo-block.ts` | Geo-blocking at the edge |
| **Web push** | `netlify/functions/push-subscribe.mjs`, `_lib/wallet-push.mjs` | PWA push subscriptions; APNs push for Apple Wallet pass updates |
| **Apple Wallet pass updates** | `netlify/functions/wallet.mjs`, `_pass-builder.mjs` | PassKit web service for live card refreshes |
| **CSP & security headers** | `netlify.toml` headers section | Stricter defaults; relaxed for kiosk (camera) and admin |
| **Image optimization** | `netlify.toml` | Netlify Images at `/.netlify/images?url=…` |
| **URL shortener** | `netlify/functions/shorten.mjs` | `/s/:code` redirects |
| **Outbound webhooks** | `netlify/functions/_lib/webhooks.mjs`, `webhook-deliver.mjs` | Configurable in `/admin/webhooks.html` |
| **PDF generation** | `netlify/functions/generate-agreement-pdf.mjs`, `js/admin-member-statement-pdf.js`, `js/admin-incident-pdf.js`, `js/ticket-pdf.js` | `pdf-lib` for agreements; client-side renders for statements, incidents, tickets |
| **Rate limiting** | `netlify/functions/_lib/rate-limit.mjs`, `_api/rate-limit.mjs` | IP/user throttling on public + internal endpoints |
| **Idempotency** | `netlify/functions/_api/idempotency.mjs` | Idempotency-key dedupe on mutating API calls |
| **Audit logging** | `netlify/functions/_lib/audit.mjs` | Surface in `/admin/audit.html` |
| **Notification bell** | `js/notifications-bell.js` | Topbar bell + popover for in-app notifications |
| **Theme toggle** | `js/theme.js` | Light/dark/auto with `prefers-color-scheme`; persisted in `localStorage` |

---

## Flagged for follow-up

No features are flagged at this time. This section exists so future status updates have a single place to land.

When something gets flagged inline above (`incomplete` / `deprecated` / `flag`), copy the row here so cleanup work has one list to scan.
