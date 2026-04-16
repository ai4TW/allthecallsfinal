# AllTheCalls — Master Knowledge Base

> Last updated: April 16, 2026
> Purpose: Single source of truth for everything AllTheCalls. Any future Claude session starts here.

---

## TL;DR

AllTheCalls.ai is a 24/7 AI phone receptionist SaaS built on **Trillet AI**. Businesses sign up, get an AI receptionist configured to their industry, and never miss a call again. The product is white-labeled Trillet under the AllTheCalls brand.

**As of April 16, 2026: We are migrating the entire stack into Go High Level (GHL).** The old Vercel/Supabase/Resend stack is being replaced. GHL handles website, CRM, email, automations, funnels, and client portal — all for ~$100/mo. Only Stripe (payments) and Trillet (voice AI) remain as external services.

---

## Owner

- **Name:** Brayden Myers
- **Email:** brayden@nextlevelacq.com (personal), brayden@allthecalls.com (AllTheCalls), hello@allthecalls.ai (business)
- **GitHub:** github.com/realbraydenm (org: ai4TW)
- **Working style:** Not a coder. Wants autonomous systems. Prefers GUI management (GHL dashboard). Trusts Claude to make decisions and execute. Don't ask permission — build and present results. Fix things silently; only surface issues that require his input (credentials, business decisions).

---

## The Product

**What we sell:** AI phone receptionists for any business — real estate, legal, medical, home services, etc.

**How it works:**
1. Business finds us (outreach, ads, referrals)
2. They pick a plan and pay via Stripe (14-day free trial)
3. Self-serve wizard configures their AI (business name, services, hours, greeting style)
4. We create their Trillet Call Flow + Call Agent + Knowledge Base automatically
5. Brayden assigns a phone number in Trillet dashboard (only manual step — no API for this)
6. Client gets a welcome email with their portal login
7. Client can see all calls, recordings, transcripts, and summaries in their portal

**Pricing (Stripe LIVE):**
| Plan | Price/mo | Target |
|------|----------|--------|
| Solo | $199 | Single location |
| Pro | $349 | Multi-location / busy |
| Agency | $599 | Agencies managing multiple clients |

**Demo line — Gia:**
- Phone: +1 (316) 232-4777
- Trillet Agent ID: `69d1997a9491b9a74426c02f`
- Trillet Flow ID (pathway): `69d1997a9491b9a74426c040`
- LLM: gpt-4.1, Voice: ElevenLabs Sarah, STT: Deepgram Flux

---

## Architecture — NEW (GHL Migration)

### What's changing

| Was (old stack) | Becomes (GHL) | Status |
|-----------------|---------------|--------|
| Vercel-hosted Next.js marketing site | GHL Funnel/Website | TODO |
| Supabase database | GHL CRM + Custom Fields | TODO |
| Resend email | GHL Email (SMTP built-in) | TODO |
| Custom signup wizard (Next.js) | GHL Form + Workflow | TODO |
| Custom client dashboard (/my) | GHL Client Portal or Membership Area | TODO |
| Portal at app.allthecalls.ai (Next.js) | GHL Client Portal or keep as-is | TBD |
| Stripe embedded checkout | Stripe via GHL integration | TODO |
| Manual prospect outreach | GHL Workflows + Prospecting | TODO |

### What stays the same

| Service | Why it stays |
|---------|-------------|
| **Trillet AI** | The actual voice AI product — no replacement |
| **Stripe** | Payment processing — GHL integrates natively |
| **Domain: allthecalls.ai** | Point DNS to GHL instead of Vercel |

### New simplified stack

```
GHL ($100/mo)
  ├── Website / Funnel (replaces Vercel site)
  ├── CRM (replaces Supabase)
  ├── Email + SMS (replaces Resend)
  ├── Workflows / Automations (replaces nothing — this is NEW capability)
  │   ├── Lead gen sequences (cold email, SMS, follow-up)
  │   ├── Onboarding automation (post-payment → create Trillet agent → welcome email)
  │   └── Client nurture (check-ins, upsells, review requests)
  ├── Pipeline (visual deal tracking)
  ├── Calendar (booking calls with prospects)
  └── Client Portal (call history — may keep app.allthecalls.ai or rebuild in GHL)

Trillet AI (Agency Plus plan)
  └── Voice AI engine — Call Flows, Call Agents, Knowledge Bases, Recordings

Stripe (payment processor)
  └── Subscriptions, trials, invoicing
```

### The automated business machine (target state)

```
LEAD GENERATION
  ├── GHL Prospecting → find businesses without AI receptionists
  ├── Cold email sequence (5-7 touches over 14 days)
  ├── SMS follow-up for engaged leads
  ├── Facebook/Instagram ads → GHL funnel
  └── All leads land in GHL CRM pipeline

SALES
  ├── Lead books a demo call via GHL Calendar
  ├── OR: lead signs up self-serve via funnel
  ├── Stripe processes payment (14-day trial)
  └── Deal moves through pipeline automatically

ONBOARDING (fully automated)
  ├── Payment triggers GHL workflow
  ├── Workflow fires webhook to create Trillet Call Flow + Agent + KB
  ├── Welcome email sent via GHL with portal login
  ├── Brayden assigns phone number (only manual step)
  └── Follow-up sequence: day 1, day 3, day 7 check-ins

CLIENT MANAGEMENT
  ├── Client portal shows call history, recordings, transcripts
  ├── Monthly check-in emails (automated)
  ├── Usage alerts (high call volume = upsell opportunity)
  └── Review request sequence after 30 days
```

---

## GHL Setup Details

### GHL Account

- **Location:** All The Calls AI
- **GHL Location ID:** `PeMkLPdDHTeQ4OWJXrGC`
- **GHL API Key (PIT):** `pit-5edab7ad-d4c1-4423-aa05-bae23d28658a`
- **Company ID:** `A9ofIK7JOtpdIzATw90r`
- **Timezone:** America/Chicago
- **Date created:** April 9, 2026

### GHL Custom Field IDs (created April 16, 2026)

| Field | GHL ID | Field Key |
|-------|--------|-----------|
| Business Name | WAhvLYJEo8CKL4GPFMs3 | contact.business_name |
| Business Phone | YCO4Q3Puf71skyYagGQN | contact.business_phone |
| Business Email | JNELrvarBWv9d89EKeUr | contact.business_email |
| Industry | 8o7fwZxbTN1INIFDXVrg | contact.industry |
| Services Offered | UmYTQjqOIQR5XHsnLZOU | contact.services_offered |
| Business Hours | MXxAHNvhFYRhKqQjzRA0 | contact.business_hours |
| Greeting Style | Pp5uXJK5XHtgNP0fh3eO | contact.greeting_style |
| Plan | pJoVKc74KTW0v2kjOfq0 | contact.plan |
| Trillet Flow ID | GW83RmeAKGh3jAG5pyds | contact.trillet_flow_id |
| Trillet Agent ID | FwN2YsED7waCFTAS1bdh | contact.trillet_agent_id |
| Portal Access URL | agnp6UfqDAlmmF6V2d7o | contact.portal_access_url |
| Signup Date | NtRjCcuUkmkJdFMgDpml | contact.signup_date |
| Trial End Date | nkA0rZU4SXvk5X4qtXrQ | contact.trial_end_date |
| Phone Number Assigned | 9SerJVybWXJipkd7bLQo | contact.phone_number_assigned |
| Monthly Revenue | uzOEf3GhZfv4zT2PP1KD | contact.monthly_revenue |
| Last Call Date | xVD0x9dNRSGt9Cu2CF2F | contact.last_call_date |
| Total Calls | IzO9kKceKDd7HJGnw3bE | contact.total_calls |
| Stripe Customer ID | tLGaTrYCZ9xjOHR63P99 | contact.stripe_customer_id |
| Onboarding Status | 3TDbe3zY0zR5yLGZlqhE | contact.onboarding_status |

### GHL Contacts (imported April 16, 2026)

| Contact | GHL ID | Company | Trillet Agent |
|---------|--------|---------|---------------|
| Kenny Wright | Y1PDg65t5EHVZWNeMmf8 | Lonestar Key Properties | 69d87b5adbfdce87a176a564 |
| Chris Waipa | 0uk2Lb8WcVCLqntvN7J2 | Mortgage Punk | 69d411962b714e68863865d1 |
| Gia (Demo) | vqSRmF3auTuOAMMZchbl | All The Calls AI | 69d1997a9491b9a74426c02f |
| Brayden (NLA) | dkvffJxALh7tVPMQOgx1 | Next Level ACQ | 69d9230d5b99a5d6bb454701 |

### Workflows to build

1. **New Lead Capture** — Form submission → add to CRM → start nurture sequence
2. **Demo Booking** — Calendar trigger → send confirmation → reminder sequence
3. **Self-Serve Signup** — Stripe payment → create Trillet agent (webhook) → welcome email → onboard sequence
4. **Onboarding Drip** — Day 1/3/7/14/30 automated emails with tips, setup help, check-ins
5. **Churn Prevention** — No calls in 14 days → "need help?" email → offer setup call
6. **Review & Referral** — 30 days active → ask for Google review → referral incentive
7. **Lead Gen Outreach** — Prospect list → cold email sequence → follow-up → pipeline

### Funnel pages to build

1. **Homepage** — Hero, benefits, social proof, demo CTA, pricing
2. **Pricing page** — 3 plans, FAQ, Stripe checkout integration
3. **Demo booking page** — Calendar embed, Gia demo number
4. **Thank you / Welcome** — Post-payment, next steps
5. **Setup wizard** — Multi-step form collecting business info for Trillet config

### Trillet integration via GHL webhook

When a client pays, GHL workflow fires a webhook that:
1. Creates a Trillet Call Flow with the client's business details
2. Creates a Trillet Call Agent (voice + STT + TTS config)
3. Creates a Knowledge Base with their services, hours, FAQ
4. Stores the flow ID + agent ID back in GHL custom fields on the contact
5. Sends welcome email with portal access link

This webhook handler needs to live somewhere that can run code. Options:
- **GHL Custom Webhook action** (limited — can't run complex logic)
- **A single serverless function** (one small API endpoint on Vercel or similar) that receives the webhook and talks to Trillet API
- **Make.com / n8n** bridge (receives webhook, calls Trillet API, returns IDs to GHL)

The Trillet agent creation is the one piece that needs code. Everything else is native GHL.

---

## Trillet Reference (unchanged)

| Thing | Value |
|---|---|
| Dashboard | https://app.trillet.ai |
| Plan | Agency Plus |
| Workspace ID | `69cf4b468d2a8a6a8e39b684` |
| Account email | `brayden@allthecalls.com` |
| API base | `https://api.trillet.ai` |

### Two-entity model (critical — don't confuse these)

Each client gets TWO Trillet entities:
- **Call Flow** (`flowId`) — holds Knowledge Base, pathway, SMS config, prompt, welcome message
- **Call Agent** (`agentId`) — voice settings, STT, TTS

The flow's `agent._id` is the agent ID. Portal filters by agent ID.

### API endpoints that work

| Endpoint | Method | Works? | Notes |
|----------|--------|--------|-------|
| v1 `/api/call-flows` | GET | YES | Lists all flows — used by admin page |
| v1 `/api/call-history` | GET | BROKEN | Don't use — returns undefined error |
| v2 `/api/call-history/export-csv` | POST | YES | Source of truth for call data. Filter by `agentId`. Returns CSV. |
| v2 `/api/recordings/{recordId}` | GET | YES | Streams audio/ogg |
| v1 `/api/call-flows` | POST | YES | Creates new flow (used by signup wizard) |
| v1 `/api/agents` | POST | YES | Creates new agent |
| v1 `/api/knowledge-bases` | POST | YES | Creates new KB |

### Phone number assignment

**Manual only.** No API. Brayden must assign in the Trillet dashboard after each new signup. This is the one bottleneck we can't automate until Trillet adds an API.

---

## Existing Clients (as of April 2026)

| Client | Trillet Agent ID | Trillet Flow ID | Status |
|--------|-----------------|-----------------|--------|
| Gia, All The Calls (demo) | `69d1997a9491b9a74426c02f` | `69d1997a9491b9a74426c040` | Active |
| Gia, Next Level ACQ | `69d9230d5b99a5d6bb454701` | `69d9230d5b99a5d6bb454711` | Active |
| Ria, Lonestar Key Properties (Kenny) | `69d87b5adbfdce87a176a564` | `69d87b5adbfdce87a176a574` | Active |
| Roxy, Chris Waipa (Mortgage Punk) | `69d411962b714e68863865d1` | `69d411962b714e68863865e1` | Active |

4 orphan Ria agents exist in Trillet workspace — need manual deletion from Trillet dashboard (API returns 500 on delete).

---

## Stripe

| Thing | Value |
|---|---|
| Account | `acct_1TIGs7IgJN5lMiut` |
| Mode | LIVE |
| Plans | Solo $199/mo, Pro $349/mo, Agency $599/mo |
| Trial | 14 days |
| Keys location | GHL custom values (migrating from Vercel env vars) |

---

## Old Stack (being retired)

These are being replaced by GHL. Keep them running until GHL is live, then sunset.

| Service | What it was | Retire when |
|---------|-------------|-------------|
| Vercel (`allthecallsai` team) | Hosted marketing site + portal | GHL site is live |
| Supabase (`amvaplgwteeoxyutcegk`) | Database for portal_users, clients | GHL CRM has all contacts |
| Resend (`allthecalls.ai` domain) | Transactional email | GHL email is configured |
| Claude-build repo (`ai4TW/Claude-build`) | Next.js marketing site + wizard | GHL funnel replaces it |
| allthecalls-portal repo (`ai4TW/allthecalls-portal`) | Client call history portal | TBD — may keep or rebuild in GHL |

### Old Vercel env vars (for reference during migration)

**allthecalls-ai project:**
TRILLET_API_KEY, TRILLET_WORKSPACE_ID, SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, STRIPE_SECRET_KEY, NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY, STRIPE_WEBHOOK_SECRET, RESEND_API_KEY, SESSION_SECRET, GHL_LOCATION_ID, GHL_API_KEY, GHL_PIPELINE_ID

**allthecalls-portal project:**
TRILLET_API_KEY, TRILLET_WORKSPACE_ID, SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY, RESEND_API_KEY, SESSION_SECRET, ADMIN_PASSWORD

### Gotcha: Vercel env var trailing newline corruption
Values pasted into Vercel dashboard get saved with trailing `\n`. This breaks strict APIs like Resend. Was cleaned on April 14, 2026. Won't matter once we're on GHL.

---

## Domains

| Domain | Currently points to | Should point to |
|--------|-------------------|-----------------|
| allthecalls.ai | Vercel | GHL (when ready) |
| www.allthecalls.ai | Vercel | GHL (when ready) |
| app.allthecalls.ai | Vercel (portal) | TBD — keep or move to GHL |
| getallthecalls.com | Vercel (Rhonda site) | No change |

**Registrar:** GoDaddy (Brayden's account)

**GoDaddy gotcha:** "Forwarding / Parking" overrides DNS records. If A records point to GoDaddy parking IPs (13.248.243.5 / 216.150.1.1 / 76.223.105.230), disable Forwarding in Domain Settings first.

---

## Other Brayden Projects (not AllTheCalls)

### Nexus Intelligence (Paperclip AI)

Autonomous AI company on Paperclip platform. AI tools for small businesses niche. Has 8 agents (Director, Research, Market Intel, Data, Writer, Publisher, Biz Dev, Client Success). Runs at localhost:3100. Newsletter via Beehiiv, email via Resend from news@oropath.ai. Separate from AllTheCalls.

### Rhonda Avera Website

Client site at getallthecalls.com. Hosted on Vercel under allthecallsai team. Unrelated to ATC product. `/api/contact` endpoint is just console.log — not wired up yet.

---

## Session Restart Checklist

1. Read THIS file first (`~/Documents/allthecallsfinal/README.md`)
2. Check memory files at `~/.claude/projects/-Users-bmyer/memory/`
3. Don't explore codebases on startup — ask Brayden what needs done
4. For GHL work: log into GHL dashboard, check current state of funnels/workflows
5. For Trillet work: API key is in GHL custom values (or Vercel env vars during migration)
6. Be autonomous. Build and present results. Don't ask unnecessary questions.

---

## Build Queue (April 16, 2026)

### Phase 1: GHL Foundation
- [ ] Set up AllTheCalls location in GHL (if not already done)
- [ ] Configure GHL email sending domain (allthecalls.ai)
- [ ] Connect Stripe to GHL
- [ ] Build landing page funnel in GHL
- [ ] Build pricing page with Stripe checkout
- [ ] Set up GHL Pipeline (Lead → Demo → Trial → Active → Churned)
- [ ] Import existing clients into GHL CRM

### Phase 2: Automations
- [ ] Build self-serve signup workflow (payment → Trillet agent creation → welcome email)
- [ ] Build Trillet webhook handler (serverless function or Make.com bridge)
- [ ] Build onboarding drip sequence (day 1/3/7/14/30)
- [ ] Build lead capture form + nurture sequence
- [ ] Build demo booking flow with calendar

### Phase 3: Lead Gen Machine
- [ ] Build cold outreach sequence (email + SMS, 5-7 touches)
- [ ] Load 500-prospect list into GHL
- [ ] Set up prospecting automation for finding new businesses
- [ ] Connect Facebook/Instagram ads to GHL funnel
- [ ] Build referral program workflow

### Phase 4: Sunset Old Stack
- [ ] Verify all GHL flows work end-to-end
- [ ] Migrate portal functionality (call history) — keep app.allthecalls.ai or rebuild
- [ ] Point allthecalls.ai DNS to GHL
- [ ] Decommission Vercel projects
- [ ] Cancel Supabase (free tier, but clean up)
- [ ] Cancel Resend

---

*This file is the single source of truth for AllTheCalls. Updated by Claude at Brayden's direction. When in doubt, this doc wins.*
