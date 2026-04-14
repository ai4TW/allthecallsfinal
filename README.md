# AllTheCalls — Final State Snapshot

> Last updated: April 14, 2026
> Purpose: one-page handoff so any future Claude (or human) can pick up the AllTheCalls stack cold. Start here, then dive into the relevant repo.

---

## TL;DR

AllTheCalls.ai is a white-label AI phone receptionist SaaS built on top of Trillet AI. Clients sign up via Stripe, run a 4-step self-serve wizard, and land in a dashboard — no manual onboarding needed. A separate mobile-first **portal** at `app.allthecalls.ai` lets each client see their AI's call history, recordings, and transcripts. Brayden manages portal logins from a master `/admin` page and fires branded welcome emails via Resend.

## Projects

| Thing | Repo | Vercel Project | Domain(s) |
|---|---|---|---|
| **Main marketing site + signup wizard + client dashboard** | `ai4TW/Claude-build` | `allthecallsai/allthecalls-ai` | `allthecalls.ai`, `www.allthecalls.ai` |
| **Client portal** (call history / recordings / transcripts / PWA) | `ai4TW/allthecalls-portal` | `allthecallsai/allthecalls-portal` | `app.allthecalls.ai` |
| **Rhonda Avera website** (separate client site, unrelated to ATC product) | `ai4TW/Rhondawebsite` | `allthecallsai/rhonda-website` | `getallthecalls.com`, `www.getallthecalls.com`, `card.getallthecalls.com` |
| **This snapshot** | `ai4TW/allthecallsfinal` | — | — |

All three ATC projects live under the Vercel team `allthecallsai`.

## Stack

- **Framework:** Next.js 15 (App Router) + React 19 + TypeScript
- **Hosting:** Vercel (auto-deploys on push to `main`)
- **Database:** Supabase — project `amvaplgwteeoxyutcegk` (`https://amvaplgwteeoxyutcegk.supabase.co`)
- **Auth:** Custom HMAC-signed session cookies (not Supabase Auth for the portal; portal uses `/access/[token]` magic URLs)
- **Payments:** Stripe embedded checkout (LIVE keys, 3 plans: Solo $199, Pro $349, Agency $599)
- **Voice AI:** Trillet AI — Agency Plus plan, workspace `69cf4b468d2a8a6a8e39b684`
- **Email:** Resend — sending domain `allthecalls.ai` (verified)
- **Design:** "Midnight Intelligence" dark theme — `#08090f` bg, `#4cd7f6` cyan → `#7c3aed` violet → `#c4b5fd` lilac gradient, Space Grotesk font

---

## The Portal (`app.allthecalls.ai`) — deep dive

This is the newest piece and the most likely reason you're reading this doc.

### Repo

- GitHub: https://github.com/ai4TW/allthecalls-portal
- Local dev path (Brayden's machine): N/A — session scaffolded it in `/tmp/allthecalls-portal`; pull the repo to a permanent location next time

### What it is

A mobile-first Next.js app where each AllTheCalls client logs in once and sees every call their AI receptionist has handled — call history, recordings, summaries, full transcripts. Installable as a PWA on iPhone/Android via home-screen.

### Auth model (important — no passwords)

- Each client has a row in Supabase `portal_users` with a long random `access_token`
- Their login URL is `https://app.allthecalls.ai/access/{access_token}` — permanent, bookmark-able
- Clicking it sets a 30-day HMAC-signed session cookie scoped to their `agent_id`
- Server-side requests to Trillet always filter by `session.agentId` — zero cross-tenant leakage
- Admin page (`/admin`) is gated by `ADMIN_PASSWORD` env var; separate 12-hour admin cookie

### Supabase schema

```sql
-- Already applied in prod. Definition in supabase/migrations/001_portal_users.sql.
create table public.portal_users (
  id uuid primary key default gen_random_uuid(),
  email text not null,
  name text not null,
  agent_id text not null,       -- Trillet voice agent ID
  flow_id text,                 -- Trillet call flow ID
  access_token text not null unique,
  created_at timestamptz not null default now(),
  last_login_at timestamptz,
  email_sent_at timestamptz
);

create unique index portal_users_email_agent_idx
  on public.portal_users (lower(email), agent_id);
```

### Env vars (set in Vercel)

| Key | Purpose |
|---|---|
| `TRILLET_API_KEY` | Trillet REST auth |
| `TRILLET_WORKSPACE_ID` | Trillet workspace scope |
| `SUPABASE_URL` | Supabase project URL |
| `SUPABASE_SERVICE_ROLE_KEY` | Server-side DB writes (portal_users) |
| `RESEND_API_KEY` | Welcome email sender |
| `SESSION_SECRET` | HMAC for session + admin cookies |
| `ADMIN_PASSWORD` | Master admin password (see credentials section) |

### Trillet API quirks that matter

- **v1 `/api/call-history` list/detail is broken** — returns `Cannot read properties of undefined (reading 'length')` for Gia's agent regardless of filter params. **Do NOT use it.**
- **v2 `/api/call-history/export-csv` (POST) is the source of truth.** Filters by `agentId`. Returns a CSV with 20+ columns (call ID, phone numbers, status, duration, summary, recording link column, transcript column if requested, etc). Portal parses this server-side on every page load.
- **v2 `/api/recordings/{recordId}` (GET) streams the audio** as `audio/ogg`. The CSV's "Recording Link" column is empty, so the portal proxies through `/api/recording/[id]` to fetch + stream from Trillet.
- **v1 `/api/call-flows` (GET) works** and is used by the admin page to populate the flow dropdown.
- Recording capture is already enabled on the flow (`settings.enableRecording = true`). New calls have recordings; use `recordId` → v2 recordings endpoint.

### Admin page playbook

1. Go to https://app.allthecalls.ai/admin/login
2. Enter the master password (see credentials section below)
3. **"Add a client login" form:**
   - Type client email + a display name
   - Pick their call flow from the dropdown (auto-populated from Trillet)
   - Click "Generate login URL" → row is created in `portal_users`
4. **"Active logins" list:**
   - Each row has a copyable access URL
   - Click "Send welcome email" → fires a fully branded Resend email with the "Open My Portal" button + iPhone/Android install instructions
   - Click "Delete" → revokes access immediately
5. **"Flows without a login"** — any Trillet flow that doesn't yet have a portal_user row

### Current portal clients (as of this snapshot)

Check `/admin` for the live list. At time of writing, the 4 flows available were:
- Gia, All The Calls — agent `69d1997a9491b9a74426c02f`, flow `69d1997a9491b9a74426c040`
- Gia, Next Level ACQ — agent `69d9230d5b99a5d6bb454701`, flow `69d9230d5b99a5d6bb454711`
- Ria, Lonestar Key Properties (Kenny) — agent `69d87b5adbfdce87a176a564`, flow `69d87b5adbfdce87a176a574`
- Roxy - Chris Waipa (Mortgage Punk) — agent `69d411962b714e68863865d1`, flow `69d411962b714e68863865e1`

---

## Main site (`Claude-build` / `allthecalls.ai`) — brief

See the deeper `project_allthecalls.md` auto-memory file. Key points:

- Local repo: `/Users/bmyer/Documents/Claude-build`
- Self-serve signup wizard at `/welcome` (4 steps: Business → Services → AI → Password)
- Signup creates Trillet flow + agent + KB + Supabase auth user + clients row in one transaction
- Client dashboard at `/my` — call history, settings, AI management
- Stripe embedded checkout (live), 14-day trial
- Gia is the live demo receptionist — phone (316) 232-4777
- The one lingering TODO is Stripe webhook 400 (signing secret) — which was likely the `\n` corruption and should now be fixed

## Trillet reference

| Thing | Value |
|---|---|
| Dashboard | https://app.trillet.ai |
| Plan | Agency Plus |
| Workspace ID | `69cf4b468d2a8a6a8e39b684` |
| Account email | `brayden@allthecalls.com` |
| API base | `https://api.trillet.ai` |
| Two-entity model | Every client has a **Call Flow** (holds KB + pathway + SMS config) AND a **Call Agent** (voice/STT/TTS). The flow's `agent._id` is the agent ID. Portal filters by agent ID. |
| Phone number assignment | Manual only, via dashboard — no API |

## GHL reference

- GHL Location ID, API Key, Pipeline ID are in `Claude-build` env vars
- Brayden doesn't want automation flowing through GHL for portal signups (stated April 14, 2026). Portal sends its own welcome emails via Resend directly. Revisit only if GHL workflows are needed for other flows.

## Known-working (as of this snapshot)

- Marketing site + SEO
- Signup wizard creating Trillet + Supabase resources end-to-end
- Stripe checkout (live)
- Portal login via access token, stats bar, call list, call detail, transcripts, summaries
- Portal recording playback via `/api/recording/[id]` proxy
- Portal CSV export via `/api/export`
- Admin page: list flows, add login, send welcome email, delete user, duplicate detection
- Resend emails from `hello@allthecalls.ai` (domain verified)
- All env vars on both Vercel projects — audited for trailing `\n` corruption April 14, 2026

## Known broken / TODO

- **4 orphan Ria agents** in Trillet workspace — must delete manually from Trillet dashboard (API 500s on delete)
- **Sales pipeline** — no outreach has been sent; 500-prospect list built but not launched to Instantly.ai / FB groups yet
- **Portal multi-device sync** — access URLs work on any device, but there's no "log out other devices" button yet
- **Portal `/api/contact` on rhondawebsite** is just console.log — wire to Resend/CRM when ready

## Gotchas you WILL run into

1. **Trailing `\n` in Vercel env vars.** When values are pasted into the Vercel dashboard, trailing newlines get saved. `vercel env pull` escapes them as literal `\n` in the written .env file, but the Vercel runtime gets the REAL trailing newline. This breaks strict APIs (Resend especially). **Audit regularly:**
   ```bash
   vercel env pull /tmp/env.check --environment=production --yes
   grep -nE '\\n"$' /tmp/env.check
   ```
   If anything matches, that var is corrupted.

2. **Trillet v1 call-history endpoints are unreliable.** Always use the v2 export-csv endpoint + parse CSV server-side.

3. **Trillet has two confusingly-named entities** ("Call Flow" vs "Call Agent"). In Claude-build's code, the variable `agentId` is usually actually the FLOW ID. In the portal, `agentId` always means the VOICE agent ID (used by v2 export-csv).

4. **GoDaddy "Forwarding / Parking"** overrides DNS records. If you add A records at a registrar but the domain still resolves to GoDaddy parking IPs (13.248.243.5 / 216.150.1.1 / 76.223.105.230), disable Forwarding in Domain Settings — that's what's re-injecting them.

5. **Vercel SSO deployment protection** is ON by default on new projects in the `allthecallsai` team. Disable via the Vercel API `PATCH /v9/projects/{name}?teamId=...` with `{"ssoProtection":null}` or new projects will 401 everyone.

## Credentials reference (where to look, not what they are)

- **Portal admin password**: Only Brayden has it. Saved in his password manager.
- **Trillet API key**: Vercel env `TRILLET_API_KEY` on both projects
- **Supabase service role**: Vercel env `SUPABASE_SERVICE_ROLE_KEY`
- **Resend API key**: Vercel env `RESEND_API_KEY` — the one that starts `re_NyYJkV...` — if you see `re_...\n` in the pulled env file, it's corrupted; fix per gotcha #1
- **Stripe LIVE keys**: Vercel env `STRIPE_SECRET_KEY` / `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`
- **Supabase SQL editor**: https://supabase.com/dashboard/project/amvaplgwteeoxyutcegk/sql/new
- **Vercel dashboard**: https://vercel.com/allthecallsai
- **GoDaddy**: Brayden's account

## Session restart checklist

When restarting Claude to pick up work on AllTheCalls:

1. Read this file (`allthecallsfinal/README.md`) first
2. Read `/Users/bmyer/.claude/projects/-Users-bmyer/memory/project_allthecalls.md` — older but has deeper history
3. Pull the relevant repo locally if not already there:
   - `git clone git@github.com:ai4TW/Claude-build.git ~/Documents/Claude-build`
   - `git clone git@github.com:ai4TW/allthecalls-portal.git ~/Documents/allthecalls-portal`
4. Pull env vars: `cd <repo> && vercel env pull .env.local --environment=production --yes`
5. Audit env vars for `\n` corruption (see gotchas)
6. `npm install && npm run dev`
7. Tell Claude: "read the allthecallsfinal README.md and continue where we left off"

## Quick-reference commands

```bash
# Audit all Vercel env vars on a project for \n corruption
vercel env pull /tmp/env.check --environment=production --yes
grep -nE '\\n"$' /tmp/env.check

# List Trillet flows (find agent IDs for new clients)
API="..." WS="69cf4b468d2a8a6a8e39b684"
curl -s "https://api.trillet.ai/v1/api/call-flows" \
  -H "x-api-key: $API" -H "x-workspace-id: $WS" | jq '.[] | {name, id: ._id, agentId: .agent._id}'

# Test Resend key
curl -s "https://api.resend.com/domains" -H "Authorization: Bearer $RESEND_API_KEY"

# Force-issue a Vercel SSL cert (if auto-issue lags)
curl -X POST "https://api.vercel.com/v4/certs?teamId=team_hFImaLSamwUJVzU0LST7woUj" \
  -H "Authorization: Bearer $VERCEL_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"cns":["<domain>"]}'

# Disable SSO protection on a new Vercel project
curl -X PATCH "https://api.vercel.com/v9/projects/<project-name>?teamId=team_hFImaLSamwUJVzU0LST7woUj" \
  -H "Authorization: Bearer $VERCEL_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ssoProtection":null}'
```

---

*This file is maintained by Claude at Brayden's request. Update it when the shape of things changes, not after every little edit. When in doubt, `git log` in the portal and Claude-build repos is the real truth.*
