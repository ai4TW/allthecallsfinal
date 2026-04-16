# AllTheCalls.ai — Go High Level Automation Workflows

> Last updated: April 16, 2026
> Purpose: Complete GHL workflow blueprints with exact triggers, actions, conditions, wait times, and all email/SMS copy. Build these in GHL Workflows tab exactly as documented.

---

## Table of Contents

1. [Master Reference Lists](#master-reference-lists)
2. [Workflow 1: New Lead Capture](#workflow-1-new-lead-capture)
3. [Workflow 2: Demo Booking](#workflow-2-demo-booking)
4. [Workflow 3: Self-Serve Signup](#workflow-3-self-serve-signup)
5. [Workflow 4: Churn Prevention](#workflow-4-churn-prevention)
6. [Workflow 5: Cold Outreach Sequence](#workflow-5-cold-outreach-sequence)
7. [Workflow 6: Review & Referral](#workflow-6-review--referral)

---

## Master Reference Lists

### All Custom Fields (create these in GHL Settings > Custom Fields FIRST)

| Field Name | Field Key | Type | Used In |
|---|---|---|---|
| Business Name | `business_name` | Text | All workflows |
| Business Type | `business_type` | Text | Signup, Outreach |
| Plan Name | `plan_name` | Dropdown (Solo/Pro/Agency) | Signup |
| Plan Price | `plan_price` | Number | Signup |
| Stripe Customer ID | `stripe_customer_id` | Text | Signup |
| Stripe Subscription ID | `stripe_subscription_id` | Text | Signup |
| Trillet Flow ID | `trillet_flow_id` | Text | Signup |
| Trillet Agent ID | `trillet_agent_id` | Text | Signup |
| Trial Start Date | `trial_start_date` | Date | Signup |
| Trial End Date | `trial_end_date` | Date | Signup |
| Signup Date | `signup_date` | Date | Signup, Review |
| Phone Number Assigned | `phone_number_assigned` | Checkbox | Signup |
| Portal Access URL | `portal_access_url` | Text | Signup |
| Lead Source | `lead_source` | Dropdown (Funnel/Cold Outreach/Referral/Organic) | All |
| Last Call Date | `last_call_date` | Date | Churn |
| Total Calls | `total_calls` | Number | Churn, Review |
| Demo Date | `demo_date` | Date | Demo Booking |
| Demo Attended | `demo_attended` | Checkbox | Demo Booking |
| Referral Code | `referral_code` | Text | Referral |
| Referred By | `referred_by` | Text | Referral |

### All Tags

| Tag | Purpose | Added By |
|---|---|---|
| `new-lead` | Just submitted funnel form | Workflow 1 |
| `nurture-active` | Currently in nurture sequence | Workflow 1 |
| `nurture-complete` | Finished nurture sequence | Workflow 1 |
| `demo-booked` | Has a scheduled demo | Workflow 2 |
| `demo-attended` | Attended the demo | Workflow 2 |
| `demo-no-show` | Missed the demo | Workflow 2 |
| `trial-active` | Currently on 14-day free trial | Workflow 3 |
| `trial-ending` | Trial ending within 2 days | Workflow 3 |
| `paying-customer` | Active paid subscription | Workflow 3 |
| `inactive-14-days` | No calls in 14 days | Workflow 4 |
| `at-risk` | Churn prevention sequence active | Workflow 4 |
| `churned` | Cancelled subscription | Workflow 4 |
| `prospect` | Cold outreach target | Workflow 5 |
| `outreach-active` | Currently in cold sequence | Workflow 5 |
| `engaged` | Replied to outreach | Workflow 5 |
| `active` | Using the product regularly | Workflow 6 |
| `review-requested` | Google review email sent | Workflow 6 |
| `referral-asked` | Referral program email sent | Workflow 6 |
| `do-not-contact` | Opted out / requested stop | Global |

### All Pipeline Stages

**Pipeline Name: AllTheCalls Sales Pipeline**

| Stage | Order | Description |
|---|---|---|
| New Lead | 1 | Just captured, entering nurture |
| Nurture | 2 | In email/SMS drip sequence |
| Demo Booked | 3 | Appointment scheduled |
| Demo Complete | 4 | Attended demo, pricing sent |
| Trial Active | 5 | Signed up, 14-day trial running |
| Paying Customer | 6 | Converted to paid |
| At Risk | 7 | Churn warning triggered |
| Churned | 8 | Cancelled |
| Engaged | 9 | Replied to cold outreach (pre-demo) |

### All Email Templates (Summary)

| Template Name | Used In |
|---|---|
| Lead Welcome | Workflow 1 |
| Nurture 1-5 | Workflow 1 |
| Demo Confirmation | Workflow 2 |
| Demo Reminder | Workflow 2 |
| Demo No-Show Follow-Up | Workflow 2 |
| Demo Attended — Pricing | Workflow 2 |
| Signup Welcome | Workflow 3 |
| Onboarding Day 1 | Workflow 3 |
| Onboarding Day 3 | Workflow 3 |
| Onboarding Day 7 | Workflow 3 |
| Onboarding Day 12 | Workflow 3 |
| Onboarding Day 30 | Workflow 3 |
| Onboarding Day 37 | Workflow 3 |
| Churn — Inactive Notice | Workflow 4 |
| Churn — Free Setup Offer | Workflow 4 |
| Cold Outreach 1-7 | Workflow 5 |
| Review Request | Workflow 6 |
| Referral Ask | Workflow 6 |

---

## Workflow 1: New Lead Capture

**Workflow Name:** `WF-01 New Lead Capture + Nurture`
**Trigger:** Form Submitted (any funnel form on allthecalls.ai)

### Step-by-Step Flow

```
TRIGGER: Form Submitted
│
├─ ACTION: Create/Update Contact
│  Map form fields → First Name, Last Name, Email, Phone, business_name, business_type
│
├─ ACTION: Add Tag → "new-lead"
├─ ACTION: Add Tag → "nurture-active"
│
├─ ACTION: Move to Pipeline Stage → "New Lead"
│  Pipeline: AllTheCalls Sales Pipeline
│  Stage: New Lead
│
├─ ACTION: Send Email → "Lead Welcome"
│  (copy below)
│
├─ ACTION: Internal Notification → Email to brayden@nextlevelacq.com
│  Subject: "New Lead: {{contact.first_name}} {{contact.last_name}} — {{contact.business_name}}"
│  Body: "New lead just submitted the funnel form.\n\nName: {{contact.first_name}} {{contact.last_name}}\nEmail: {{contact.email}}\nPhone: {{contact.phone}}\nBusiness: {{contact.business_name}}\nBusiness Type: {{contact.business_type}}\n\nThey're entering the nurture sequence now."
│
├─ ACTION: Move to Pipeline Stage → "Nurture"
│
├─ WAIT: 1 day
├─ ACTION: Send Email → "Nurture Email 1"
│
├─ WAIT: 2 days
├─ ACTION: Send Email → "Nurture Email 2"
├─ ACTION: Send SMS → "Nurture SMS 1"
│
├─ WAIT: 2 days
├─ ACTION: Send Email → "Nurture Email 3"
│
├─ WAIT: 2 days
├─ ACTION: Send Email → "Nurture Email 4"
├─ ACTION: Send SMS → "Nurture SMS 2"
│
├─ WAIT: 2 days
├─ ACTION: Send Email → "Nurture Email 5"
│
├─ ACTION: Remove Tag → "nurture-active"
├─ ACTION: Add Tag → "nurture-complete"
```

### Email & SMS Copy

---

**LEAD WELCOME EMAIL**

From: AllTheCalls.ai <hello@allthecalls.ai>
Subject: You're in — here's what happens next

Body:
```
Hi {{contact.first_name}},

Thanks for checking out AllTheCalls. You just took the first step toward never missing a business call again.

Here's the short version of what we do: we give your business an AI phone receptionist that answers every call 24/7, books appointments, answers FAQs, and sends you a summary after each conversation. It sounds exactly like a real person — because it's trained on YOUR business.

Want to hear it in action? Call our demo line right now:

(316) 232-4777

That's Gia, our demo receptionist. She'll show you exactly what your callers will experience.

Over the next few days, I'll send you a few short emails showing how businesses like yours are using this to capture more leads and stop losing calls. No fluff, just real examples.

Talk soon,
Brayden Myers
Founder, AllTheCalls.ai
hello@allthecalls.ai
```

---

**NURTURE EMAIL 1 — Day 1**

Subject: The call you missed last week? It was worth $3,000.

Body:
```
Hi {{contact.first_name}},

The average missed business call is worth $1,200 in lifetime revenue. Most businesses miss 3-5 calls per week.

That's $3,600 to $6,000 walking out the door every single week — to your competitors who picked up.

AllTheCalls fixes this permanently. Your AI receptionist:
- Answers every call within 2 rings, 24/7/365
- Knows your business, your services, your pricing
- Books appointments directly into your calendar
- Sends you a full summary and recording of every call

No hold music. No voicemail. No "sorry, we're closed."

You can hear exactly how it works by calling our demo line: (316) 232-4777

Talk soon,
Brayden
AllTheCalls.ai
```

---

**NURTURE EMAIL 2 — Day 3**

Subject: "I thought it was a real person"

Body:
```
Hi {{contact.first_name}},

That's what our clients' callers say after talking to their AI receptionist.

The AI doesn't sound robotic. It doesn't follow a rigid script. It actually listens, responds naturally, and handles the conversation the way a great receptionist would — except it never calls in sick, never puts anyone on hold, and never forgets your pricing.

Here's what's included when you sign up:
- Your AI is custom-trained on YOUR business (we handle the setup)
- It answers calls, books appointments, and captures lead info
- You get a private portal with every call recording, transcript, and summary
- Plans start at $199/mo — less than one missed lead is worth

Want to see the portal? Want to hear a live demo? Just reply to this email or call (316) 232-4777.

Brayden
AllTheCalls.ai
```

---

**NURTURE SMS 1 — Day 3 (sent same day as Email 2)**

```
Hey {{contact.first_name}}, it's Brayden from AllTheCalls. Quick question — how many calls does your business miss per week? Our AI receptionist answers every one, 24/7. Call our demo: (316) 232-4777. Reply STOP to opt out.
```

---

**NURTURE EMAIL 3 — Day 5**

Subject: Your competitors answer at 11 PM. Do you?

Body:
```
Hi {{contact.first_name}},

42% of callers who reach voicemail hang up and call the next business on Google. They don't leave a message. They don't call back.

After hours, weekends, lunch breaks, staff meetings — these are the moments you're bleeding leads.

AllTheCalls gives you a receptionist that works every hour of every day. It answers in your brand voice, with your business knowledge, and captures every opportunity.

Setup takes about 24 hours. We train the AI on your business, assign you a dedicated phone number, and you start getting call summaries immediately.

Plans:
- Solo: $199/mo (1 AI receptionist, perfect for solo operators)
- Pro: $349/mo (priority support + advanced features)
- Agency: $599/mo (multiple locations or client-facing)

All plans include a 14-day free trial. No contracts. Cancel anytime.

Ready to stop missing calls? Sign up at allthecalls.ai or reply to this email and I'll walk you through it.

Brayden
AllTheCalls.ai
```

---

**NURTURE EMAIL 4 — Day 7**

Subject: What would you do with 10 extra hours a week?

Body:
```
Hi {{contact.first_name}},

That's how much time the average small business owner spends answering, returning, and managing phone calls every week.

When your AI receptionist handles the phones, you get that time back — to close deals, serve clients, or just leave the office before 7 PM.

Here's what our clients tell us they love most:
1. They stop worrying about missed calls
2. Their callers get instant, professional answers
3. They can review every conversation from their phone
4. Setup took less than a day

AllTheCalls isn't another answering service with hold music and scripts. It's AI that actually knows your business and sounds like part of your team.

Hear it for yourself: (316) 232-4777

Brayden
AllTheCalls.ai
```

---

**NURTURE SMS 2 — Day 7 (sent same day as Email 4)**

```
{{contact.first_name}} — just wanted to make sure you saw this. AllTheCalls gives you a 24/7 AI receptionist trained on your business. 14-day free trial, $199/mo after. Try the demo: (316) 232-4777. Reply STOP to opt out.
```

---

**NURTURE EMAIL 5 — Day 10**

Subject: Last thing — then I'll leave you alone

Body:
```
Hi {{contact.first_name}},

I've sent you a few emails about AllTheCalls and I don't want to overstay my welcome. This is the last one in this series.

Here's my honest pitch: if your business gets phone calls — from customers, leads, vendors, anyone — an AI receptionist will make your life dramatically easier and stop you from losing money to missed calls.

We offer a 14-day free trial with every plan. No credit card games, no surprise charges, no contracts. If it doesn't work for you, you cancel with one click.

Three ways to move forward:
1. Sign up now at allthecalls.ai (takes 5 minutes)
2. Call our demo line to hear the AI: (316) 232-4777
3. Reply to this email and I'll personally answer any questions

Whatever you decide, I appreciate your time.

Brayden Myers
Founder, AllTheCalls.ai
hello@allthecalls.ai
```

---

## Workflow 2: Demo Booking

**Workflow Name:** `WF-02 Demo Booking + Follow-Up`
**Trigger:** Appointment Status Changed → "Booked" (GHL Calendar)

### Step-by-Step Flow

```
TRIGGER: Calendar Appointment Booked
│
├─ ACTION: Add Tag → "demo-booked"
├─ ACTION: Remove Tag → "nurture-active" (stop nurture if still running)
│
├─ ACTION: Move to Pipeline Stage → "Demo Booked"
│
├─ ACTION: Update Custom Field → demo_date = {{appointment.start_time}}
│
├─ ACTION: Send Email → "Demo Confirmation"
│  (copy below)
│
├─ ACTION: Internal Notification → Email to brayden@nextlevelacq.com
│  Subject: "Demo Booked: {{contact.first_name}} {{contact.last_name}} — {{appointment.start_time}}"
│
├─ WAIT: Until 1 hour before appointment start time
│  (Use "Wait until event start time" with -60 min offset)
│
├─ ACTION: Send Email → "Demo Reminder"
├─ ACTION: Send SMS → "Demo Reminder SMS"
│
├─ WAIT: Until appointment end time + 30 minutes
│
├─ IF/ELSE CONDITION: Check tag "demo-attended"
│  (Brayden manually adds this tag after the call, OR use calendar status "showed")
│
│  ├─ IF "demo-attended" tag exists:
│  │  ├─ ACTION: Move to Pipeline Stage → "Demo Complete"
│  │  ├─ ACTION: Send Email → "Demo Attended — Pricing"
│  │  └─ END (this branch)
│  │
│  └─ ELSE (no-show):
│     ├─ ACTION: Add Tag → "demo-no-show"
│     ├─ WAIT: 24 hours
│     ├─ ACTION: Send Email → "Demo No-Show Follow-Up"
│     ├─ ACTION: Send SMS → "Demo No-Show SMS"
│     └─ END
```

### Email & SMS Copy

---

**DEMO CONFIRMATION EMAIL**

From: AllTheCalls.ai <hello@allthecalls.ai>
Subject: You're booked — here's what to expect

Body:
```
Hi {{contact.first_name}},

Your AllTheCalls demo is confirmed for {{appointment.start_date_time}}.

Here's what we'll cover in about 15 minutes:
- Live demo of an AI receptionist handling a real call
- How we'd train the AI specifically for {{contact.business_name}}
- Your portal where you'll see every call, transcript, and recording
- Pricing and how the 14-day free trial works

Before the call, I'd recommend calling our demo line so you can hear the AI firsthand:
(316) 232-4777 — ask it anything.

Talk to you soon,
Brayden Myers
Founder, AllTheCalls.ai
```

---

**DEMO REMINDER EMAIL — 1 hour before**

Subject: We're on in 1 hour

Body:
```
Hi {{contact.first_name}},

Just a quick heads up — our call is in about an hour at {{appointment.start_date_time}}.

If anything came up and you need to reschedule, just reply to this email. No worries at all.

Otherwise, I'll talk to you soon.

Brayden
```

---

**DEMO REMINDER SMS — 1 hour before**

```
Hey {{contact.first_name}}, just a reminder — we're on for your AllTheCalls demo in about an hour. Looking forward to it! - Brayden
```

---

**DEMO NO-SHOW FOLLOW-UP EMAIL — 24 hours after no-show**

Subject: Missed you yesterday — want to reschedule?

Body:
```
Hi {{contact.first_name}},

Looks like we weren't able to connect for your demo yesterday. No worries — things come up.

I'd still love to show you how an AI receptionist could work for {{contact.business_name}}. Here are a couple of options:

1. Book a new time that works: [INSERT GHL CALENDAR LINK]
2. Skip the demo and just try it — sign up for a free 14-day trial at allthecalls.ai
3. Call our demo line anytime to hear the AI: (316) 232-4777

Just let me know what works best.

Brayden
AllTheCalls.ai
```

---

**DEMO NO-SHOW SMS — 24 hours after no-show**

```
Hey {{contact.first_name}}, we missed each other yesterday for the AllTheCalls demo. Want to reschedule? Just reply with a day/time that works, or try the demo line anytime: (316) 232-4777. - Brayden
```

---

**DEMO ATTENDED — PRICING EMAIL**

Subject: Here's everything we talked about

Body:
```
Hi {{contact.first_name}},

Great talking with you today. As promised, here's a quick recap and your next steps.

What you're getting with AllTheCalls:
- A custom AI receptionist trained specifically on {{contact.business_name}}
- 24/7 call answering — nights, weekends, holidays, lunch breaks
- Appointment booking directly into your calendar
- A private portal with call recordings, transcripts, and summaries
- Setup handled by our team within 24 hours

Pricing:

Solo — $199/mo
Best for: Solo operators and small teams
Includes: 1 AI receptionist, portal access, call summaries

Pro — $349/mo
Best for: Growing businesses that want priority support
Includes: Everything in Solo + priority setup and support

Agency — $599/mo
Best for: Agencies, multi-location businesses, or resellers
Includes: Everything in Pro + multi-agent support

Every plan includes a 14-day free trial. No contracts. Cancel anytime with one click.

Ready to get started? Sign up here: https://allthecalls.ai

Or just reply "let's go" and I'll send you a direct signup link for the plan we discussed.

Brayden Myers
Founder, AllTheCalls.ai
hello@allthecalls.ai
```

---

## Workflow 3: Self-Serve Signup

**Workflow Name:** `WF-03 Self-Serve Signup + Onboarding`
**Trigger:** Webhook received (Stripe payment/subscription webhook)

### Webhook Configuration

**Inbound Webhook URL:** Set up a GHL inbound webhook trigger. The Stripe webhook (or your serverless middleware) should POST to this URL with the following payload:

```json
{
  "email": "client@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "phone": "+15551234567",
  "business_name": "Doe Plumbing",
  "business_type": "Plumbing",
  "plan_name": "Solo",
  "plan_price": 199,
  "stripe_customer_id": "cus_xxxxxxxxxxxx",
  "stripe_subscription_id": "sub_xxxxxxxxxxxx",
  "trial_start_date": "2026-04-16",
  "trial_end_date": "2026-04-30"
}
```

**Outbound Webhook to Trillet:** After creating/updating the contact, fire an outbound webhook (HTTP POST) to your serverless Trillet creation endpoint.

**Trillet Creation Endpoint Payload:**
```json
{
  "email": "{{contact.email}}",
  "businessName": "{{contact.business_name}}",
  "businessType": "{{contact.business_type}}",
  "planName": "{{contact.plan_name}}",
  "firstName": "{{contact.first_name}}",
  "lastName": "{{contact.last_name}}",
  "phone": "{{contact.phone}}",
  "workspaceId": "69cf4b468d2a8a6a8e39b684"
}
```

The endpoint returns:
```json
{
  "flowId": "69d...",
  "agentId": "69d...",
  "portalAccessUrl": "https://app.allthecalls.ai/access/xxxxxxxx"
}
```

### Step-by-Step Flow

```
TRIGGER: Inbound Webhook (Stripe payment data)
│
├─ ACTION: Create/Update Contact
│  Map webhook fields → contact fields + custom fields
│  Set: business_name, business_type, plan_name, plan_price,
│       stripe_customer_id, stripe_subscription_id,
│       trial_start_date, trial_end_date, signup_date = today
│
├─ ACTION: Add Tag → "trial-active"
├─ ACTION: Remove Tags → "new-lead", "nurture-active", "nurture-complete", "prospect", "outreach-active"
│
├─ ACTION: Move to Pipeline Stage → "Trial Active"
│
├─ ACTION: Outbound Webhook (POST) → Trillet creation endpoint
│  URL: https://allthecalls.ai/api/create-agent (or your serverless function URL)
│  Payload: (documented above)
│  Method: POST
│  Headers: Authorization: Bearer {{TRILLET_API_KEY}}
│
├─ ACTION: Update Custom Fields from webhook response
│  trillet_flow_id = {{webhook.response.flowId}}
│  trillet_agent_id = {{webhook.response.agentId}}
│  portal_access_url = {{webhook.response.portalAccessUrl}}
│
│  NOTE: If GHL can't natively capture webhook responses inline,
│  have the Trillet endpoint POST back to a second GHL inbound webhook
│  with the contact email + flowId + agentId + portalAccessUrl.
│  Use a "Wait for webhook" step or a second workflow triggered by
│  that callback to update the custom fields.
│
├─ ACTION: Send Email → "Signup Welcome"
│  (copy below)
│
├─ ACTION: Internal Notification → Email to brayden@nextlevelacq.com
│  Subject: "NEW SIGNUP — Assign phone number for {{contact.business_name}}"
│  Body:
│  "New client just signed up!\n\n
│   Name: {{contact.first_name}} {{contact.last_name}}\n
│   Business: {{contact.business_name}}\n
│   Plan: {{contact.plan_name}} (${{contact.plan_price}}/mo)\n
│   Email: {{contact.email}}\n
│   Phone: {{contact.phone}}\n\n
│   Trillet Flow ID: {{contact.trillet_flow_id}}\n
│   Trillet Agent ID: {{contact.trillet_agent_id}}\n\n
│   ACTION REQUIRED: Go to Trillet dashboard and assign a phone number to this agent.\n
│   Then mark the 'Phone Number Assigned' custom field in GHL."
│
├─ ACTION: Send SMS → "Signup Welcome SMS"
│
├─ WAIT: 1 day
├─ ACTION: Send Email → "Onboarding Day 1"
│
├─ WAIT: 2 days (Day 3 total)
├─ ACTION: Send Email → "Onboarding Day 3"
│
├─ WAIT: 4 days (Day 7 total)
├─ ACTION: Send Email → "Onboarding Day 7"
│
├─ WAIT: 5 days (Day 12 total)
├─ ACTION: Add Tag → "trial-ending"
├─ ACTION: Send Email → "Onboarding Day 12"
│
├─ WAIT: 18 days (Day 30 total)
├─ IF/ELSE: Contact has tag "active"
│  ├─ IF yes:
│  │  ├─ ACTION: Send Email → "Onboarding Day 30 — Review Request"
│  │  └─ WAIT: 7 days (Day 37 total)
│  │  └─ ACTION: Send Email → "Onboarding Day 37 — Referral Ask"
│  └─ ELSE: END (skip review/referral if not active)
```

### Email & SMS Copy

---

**SIGNUP WELCOME EMAIL**

From: Brayden at AllTheCalls <hello@allthecalls.ai>
Subject: Welcome to AllTheCalls — your AI receptionist is being built

Body:
```
Hi {{contact.first_name}},

Welcome to AllTheCalls. Your signup just went through and your AI receptionist for {{contact.business_name}} is being set up right now.

Here's what happens next:

1. We're building your AI right now — It's being trained on your business info. This usually takes less than 24 hours.

2. You'll get a dedicated phone number — I'll personally assign one and email you when it's ready. You can forward your existing business line to it, or give it out directly.

3. Your call portal is ready — Once your AI starts taking calls, you'll be able to see every call recording, transcript, and summary in your private portal. I'll send you access shortly.

Your plan: {{contact.plan_name}} (${{contact.plan_price}}/mo)
Your 14-day free trial started today and runs through {{contact.trial_end_date}}.

If you have any questions at all, just reply to this email. I read every one.

Welcome aboard,
Brayden Myers
Founder, AllTheCalls.ai
hello@allthecalls.ai
```

---

**SIGNUP WELCOME SMS**

```
Welcome to AllTheCalls, {{contact.first_name}}! Your AI receptionist for {{contact.business_name}} is being set up now. I'll reach out within 24 hours with your phone number. - Brayden
```

---

**ONBOARDING DAY 1 — Welcome + What Your AI Can Do**

Subject: Your AI receptionist is live — here's what it can do

Body:
```
Hi {{contact.first_name}},

Your AI receptionist for {{contact.business_name}} is set up and ready to take calls.

Here's what it can do right out of the box:

Answer every call instantly — No rings, no hold music, no voicemail. Your AI picks up within 2 seconds, 24 hours a day.

Sound like part of your team — It knows your business name, your services, your hours, and your pricing. Callers won't know it's AI.

Book appointments — If you connect your calendar, the AI can schedule appointments in real time during the call.

Capture lead information — Names, phone numbers, what they need, when they need it. All saved and sent to you.

Send you call summaries — After every call, you get a summary of what was discussed. You can also listen to the full recording or read the transcript in your portal.

If I've already sent you your phone number, try calling it now from a different phone. You'll hear your AI in action.

If you haven't gotten your number yet, it's coming within 24 hours — I assign these personally to make sure everything's right.

Brayden
AllTheCalls.ai
```

---

**ONBOARDING DAY 3 — Check-in**

Subject: How's your AI receptionist working?

Body:
```
Hi {{contact.first_name}},

Just checking in — you've had your AI receptionist for a few days now. How's it going?

A few things to keep in mind:

- If you haven't forwarded your business line yet, that's the single biggest step to getting value. Once calls route to your AI, everything runs on autopilot.

- Your call portal shows every call your AI has handled — recordings, transcripts, and summaries. Check it anytime.

- If the AI is saying something you want to change — like how it describes a service or your hours — just let me know and I'll update it same day.

The most successful clients set up call forwarding in the first week. If you need help with that, reply to this email with your phone carrier and I'll send you the exact steps.

Brayden
AllTheCalls.ai
```

---

**ONBOARDING DAY 7 — Tips to Get More From Your AI**

Subject: 3 ways to get more from your AI receptionist

Body:
```
Hi {{contact.first_name}},

You're one week in with AllTheCalls. Here are three things our most successful clients do:

1. Set up after-hours forwarding at minimum
If you're not ready to forward all calls, start with after-hours only. Your AI handles the nights and weekends so you never miss an opportunity while you're off the clock.

2. Check your portal weekly
Your call portal has recordings, transcripts, and summaries of every call. It takes 5 minutes to review and you'll spot trends — common questions, popular services, peak call times.

3. Tell us what to improve
Heard a call where the AI could have done better? Just forward the details and we'll refine it. The AI gets smarter over time, and your feedback accelerates that.

You're still in your free trial ({{contact.trial_end_date}} is the last day), so now's the perfect time to test everything and make sure it's dialed in before your subscription starts.

Any questions? Just reply.

Brayden
AllTheCalls.ai
```

---

**ONBOARDING DAY 12 — Trial Ending Reminder**

Subject: Your free trial ends in 2 days

Body:
```
Hi {{contact.first_name}},

Heads up — your AllTheCalls free trial ends on {{contact.trial_end_date}}.

After that, your {{contact.plan_name}} plan (${{contact.plan_price}}/mo) will begin billing automatically. Your AI receptionist will continue working with zero interruption.

If you want to make any changes:
- Switch plans → just reply to this email
- Cancel → you can cancel anytime from your account, no questions asked
- Keep going → you don't need to do anything

Quick question: has your AI handled any calls yet? If not, now's the time to set up call forwarding so you can see the full value before your trial ends.

Need help with anything? Just reply.

Brayden
AllTheCalls.ai
```

---

**ONBOARDING DAY 30 — Review Request**

Subject: Quick favor? (takes 30 seconds)

Body:
```
Hi {{contact.first_name}},

You've been using AllTheCalls for about a month now, and I'd genuinely love to hear what you think.

If your AI receptionist has been helpful for {{contact.business_name}}, would you mind leaving us a quick Google review? It takes about 30 seconds and means the world to a small team like ours.

Leave a review here: [INSERT GOOGLE REVIEW LINK]

Even a one-sentence review makes a huge difference. And if there's anything that ISN'T working well, I'd rather hear it from you directly — just reply to this email and I'll fix it personally.

Thank you,
Brayden Myers
Founder, AllTheCalls.ai
```

---

**ONBOARDING DAY 37 — Referral Ask**

Subject: Know someone who's missing calls?

Body:
```
Hi {{contact.first_name}},

Since you've been using AllTheCalls for over a month, I wanted to ask — do you know another business owner who's missing phone calls?

Maybe a colleague, a friend, someone in your network who's complained about voicemails piling up or leads slipping through the cracks.

If you send them our way, here's what I'll do:
- Give them a 14-day free trial (same as yours)
- Set up their AI receptionist personally
- Give YOU a $50 credit on your next invoice for every referral who signs up

Just have them mention your name when they sign up, or reply to this email with their contact info and I'll reach out.

Thanks for being a client, {{contact.first_name}}. I appreciate it.

Brayden
AllTheCalls.ai
```

---

## Workflow 4: Churn Prevention

**Workflow Name:** `WF-04 Churn Prevention — Inactive Client`
**Trigger:** Tag Added → "inactive-14-days"

> Note: The "inactive-14-days" tag can be added manually by Brayden, or via a separate integration/Zapier that monitors call activity via the Trillet API and tags contacts who haven't had calls in 14 days.

### Step-by-Step Flow

```
TRIGGER: Tag "inactive-14-days" added
│
├─ ACTION: Add Tag → "at-risk"
├─ ACTION: Move to Pipeline Stage → "At Risk"
│
├─ ACTION: Send Email → "Churn — Inactive Notice"
│  (copy below)
│
├─ WAIT: 3 days
│
├─ IF/ELSE: Contact replied? (check "Last Reply" date within last 3 days)
│  ├─ IF replied: → END (they engaged, let Brayden handle manually)
│  │
│  └─ ELSE (no reply):
│     ├─ ACTION: Send SMS → "Churn SMS"
│     │
│     ├─ WAIT: 3 days
│     │
│     ├─ IF/ELSE: Contact replied?
│     │  ├─ IF replied: → END
│     │  │
│     │  └─ ELSE (still no reply):
│     │     ├─ ACTION: Send Email → "Churn — Free Setup Offer"
│     │     │
│     │     ├─ ACTION: Internal Notification → Email to brayden@nextlevelacq.com
│     │     │  Subject: "AT-RISK CLIENT: {{contact.business_name}} — no response to churn sequence"
│     │     │  Body:
│     │     │  "{{contact.first_name}} {{contact.last_name}} ({{contact.business_name}}) has been inactive for 14+ days and hasn't responded to the churn prevention emails or SMS.\n\n
│     │     │   Email: {{contact.email}}\n
│     │     │   Phone: {{contact.phone}}\n
│     │     │   Plan: {{contact.plan_name}}\n\n
│     │     │   Consider calling them directly."
│     │     └─ END
```

### Email & SMS Copy

---

**CHURN — INACTIVE NOTICE EMAIL**

Subject: Your AI receptionist is waiting for calls

Body:
```
Hi {{contact.first_name}},

I noticed that your AI receptionist for {{contact.business_name}} hasn't been getting many calls lately.

I wanted to reach out because I want to make sure you're getting value from AllTheCalls. A few common reasons this happens:

1. Call forwarding isn't set up yet — If your business line isn't forwarding to your AI number, calls won't reach it. I can walk you through the setup in under 5 minutes.

2. The AI needs fine-tuning — Maybe it said something that wasn't quite right and you haven't had a chance to tell us. Just reply with what needs to change and I'll fix it same-day.

3. You're not sure how to get the most from it — Totally fair. Reply and I'll personally help you get everything dialed in.

Your AI is live and ready to work 24/7. Let's make sure it's actually getting the chance to.

Just reply to this email and I'll help.

Brayden
AllTheCalls.ai
```

---

**CHURN SMS — Day 3 after inactive email**

```
Hey {{contact.first_name}}, it's Brayden from AllTheCalls. I noticed your AI hasn't had many calls lately. Need help setting up call forwarding or making changes? Happy to help — just reply here. Reply STOP to opt out.
```

---

**CHURN — FREE SETUP OFFER EMAIL — Day 6 after inactive email**

Subject: Free setup call — let me fix this for you

Body:
```
Hi {{contact.first_name}},

I've reached out a couple of times and I want to be straight with you — I don't want you paying for something you're not using.

So here's my offer: let me hop on a quick 15-minute call with you and I'll personally make sure everything is set up perfectly. Call forwarding, AI training, portal access, the whole thing.

No sales pitch. Just making sure {{contact.business_name}} is getting what you're paying for.

Book a free setup call here: [INSERT GHL CALENDAR LINK]

Or if AllTheCalls just isn't the right fit, no hard feelings at all. You can cancel anytime from your account. But I'd love the chance to make it work before you go.

Brayden Myers
Founder, AllTheCalls.ai
hello@allthecalls.ai
```

---

## Workflow 5: Cold Outreach Sequence

**Workflow Name:** `WF-05 Cold Outreach — 7 Email + 2 SMS`
**Trigger:** Tag Added → "prospect"

### Step-by-Step Flow

```
TRIGGER: Tag "prospect" added
│
├─ ACTION: Add Tag → "outreach-active"
│
├─ CONDITION: Check tag "do-not-contact"
│  ├─ IF has "do-not-contact": → END immediately
│  └─ ELSE: continue
│
├─ ACTION: Send Email → "Cold Email 1"
│
├─ WAIT: 3 days
├─ CONDITION: Check if reply received (Message Reply event)
│  ├─ IF replied: → GOTO [ENGAGED BRANCH]
│  └─ ELSE: continue
│
├─ ACTION: Send Email → "Cold Email 2"
│
├─ WAIT: 3 days
├─ CONDITION: Reply check
│  ├─ IF replied: → GOTO [ENGAGED BRANCH]
│  └─ ELSE: continue
│
├─ ACTION: Send Email → "Cold Email 3"
├─ ACTION: Send SMS → "Cold SMS 1" (after Email 3)
│
├─ WAIT: 3 days
├─ CONDITION: Reply check
│  ├─ IF replied: → GOTO [ENGAGED BRANCH]
│  └─ ELSE: continue
│
├─ ACTION: Send Email → "Cold Email 4"
│
├─ WAIT: 3 days
├─ CONDITION: Reply check
│  ├─ IF replied: → GOTO [ENGAGED BRANCH]
│  └─ ELSE: continue
│
├─ ACTION: Send Email → "Cold Email 5"
├─ ACTION: Send SMS → "Cold SMS 2" (after Email 5)
│
├─ WAIT: 3 days
├─ CONDITION: Reply check
│  ├─ IF replied: → GOTO [ENGAGED BRANCH]
│  └─ ELSE: continue
│
├─ ACTION: Send Email → "Cold Email 6"
│
├─ WAIT: 3 days
├─ CONDITION: Reply check
│  ├─ IF replied: → GOTO [ENGAGED BRANCH]
│  └─ ELSE: continue
│
├─ ACTION: Send Email → "Cold Email 7"
│
├─ ACTION: Remove Tag → "outreach-active"
├─ END
│
[ENGAGED BRANCH]:
├─ ACTION: Remove Tag → "outreach-active"
├─ ACTION: Remove Tag → "prospect"
├─ ACTION: Add Tag → "engaged"
├─ ACTION: Move to Pipeline Stage → "Engaged"
├─ ACTION: Internal Notification → brayden@nextlevelacq.com
│  Subject: "REPLY: {{contact.first_name}} {{contact.last_name}} replied to cold outreach"
│  Body: "{{contact.business_name}} just replied to the cold outreach sequence.\n
│         Email: {{contact.email}}\n
│         Phone: {{contact.phone}}\n
│         Check GHL conversations for their reply."
├─ END
```

> **GHL Implementation Note:** In GHL Workflows, you cannot natively check "did this contact reply?" as an inline condition between steps. Instead, create a **separate workflow** with the trigger "Customer Reply" (email reply event). That workflow should:
> 1. Check if contact has tag "outreach-active"
> 2. If yes: remove from the cold outreach workflow, remove "outreach-active" tag, add "engaged" tag, move pipeline stage, notify Brayden
>
> The reply-check conditions in the flow above are conceptual. In practice, the reply-detection workflow handles the exit.

### Email & SMS Copy

---

**COLD EMAIL 1 — Day 1**

Subject: Who answers your phones at 9 PM?

Body:
```
Hi {{contact.first_name}},

Quick question — when a potential customer calls {{contact.business_name}} after hours, what happens?

If the answer is voicemail, you're losing leads. 42% of callers who hit voicemail hang up and call the next business on Google.

We built AllTheCalls to fix that. It's an AI phone receptionist that answers every call for your business, 24/7. It knows your services, books appointments, and sounds like a real person on your team.

You can hear it right now — call (316) 232-4777 and talk to our demo receptionist, Gia.

Worth 30 seconds of your time if you're tired of missing calls.

Brayden Myers
AllTheCalls.ai

Reply STOP to unsubscribe.
```

---

**COLD EMAIL 2 — Day 4**

Subject: $1,200 per missed call

Body:
```
Hi {{contact.first_name}},

The average missed business call represents $1,200 in lifetime customer value. If you're missing just 3 calls a week, that's over $180,000 a year walking away.

AllTheCalls gives your business an AI receptionist that:
- Answers every call in under 2 seconds
- Knows your business, services, and hours
- Books appointments in real time
- Costs less than one missed lead per month ($199/mo)

No hold music. No voicemail. No missed opportunities.

Try the demo: (316) 232-4777

Brayden
AllTheCalls.ai

Reply STOP to unsubscribe.
```

---

**COLD EMAIL 3 — Day 7**

Subject: Not another answering service

Body:
```
Hi {{contact.first_name}},

I know what you might be thinking — "another answering service pitch."

AllTheCalls isn't that. There's no call center. No scripts. No hold music.

It's an AI that actually learns your business — your services, your pricing, your FAQs — and answers calls as if it's been working at {{contact.business_name}} for years.

Our clients' callers consistently say: "I thought I was talking to a real person."

14-day free trial. No contracts. Cancel anytime.

Hear it yourself: (316) 232-4777

Brayden
AllTheCalls.ai

Reply STOP to unsubscribe.
```

---

**COLD SMS 1 — Day 7 (after Email 3)**

```
Hi {{contact.first_name}}, it's Brayden from AllTheCalls. We make AI phone receptionists that answer every call for your business 24/7. Hear a live demo: (316) 232-4777. Reply STOP to opt out.
```

---

**COLD EMAIL 4 — Day 10**

Subject: What your receptionist can't do

Body:
```
Hi {{contact.first_name}},

Even the best receptionist can't:
- Work 24/7/365 without breaks
- Answer every call within 2 seconds
- Remember every detail of every conversation perfectly
- Cost less than $200/month

Your AI receptionist from AllTheCalls can do all four.

It doesn't replace your team — it catches every call your team can't. After hours, during meetings, on weekends, over holidays.

Every call is recorded, transcribed, and summarized in your private portal so you never miss a detail.

Plans start at $199/mo with a 14-day free trial.

Interested? Just reply to this email.

Brayden
AllTheCalls.ai

Reply STOP to unsubscribe.
```

---

**COLD EMAIL 5 — Day 13**

Subject: How {{contact.business_name}} could use this

Body:
```
Hi {{contact.first_name}},

I've been thinking about how AllTheCalls could specifically help {{contact.business_name}}.

Here's what I'd set up for you:
- An AI trained on your exact services and pricing
- 24/7 call answering so you never miss a lead
- Appointment booking synced to your calendar
- Call recordings and transcripts you can review from your phone

The setup takes less than 24 hours. We handle everything — you just start getting answered calls.

If you want to see what it's like from the caller's perspective, try our demo line: (316) 232-4777. Ask it anything.

No obligation. Just reply if you're curious.

Brayden
AllTheCalls.ai

Reply STOP to unsubscribe.
```

---

**COLD SMS 2 — Day 13 (after Email 5)**

```
{{contact.first_name}} — ever lost a customer because no one answered the phone? AllTheCalls fixes that with a 24/7 AI receptionist. Try 14 days free: allthecalls.ai. Reply STOP to opt out.
```

---

**COLD EMAIL 6 — Day 16**

Subject: The setup takes 24 hours

Body:
```
Hi {{contact.first_name}},

One thing I hear a lot: "This sounds great but I don't have time to set up something new."

Fair. That's why we do it all for you.

Here's what the process looks like:
1. You sign up (takes 5 minutes)
2. We train your AI on your business (we handle this)
3. Within 24 hours, your AI is live and answering calls
4. You review calls from your portal whenever you want

There's nothing for you to configure, manage, or maintain. You just stop missing calls.

14-day free trial. $199/mo after. No contracts.

allthecalls.ai

Brayden
AllTheCalls.ai

Reply STOP to unsubscribe.
```

---

**COLD EMAIL 7 — Day 19**

Subject: Last note from me

Body:
```
Hi {{contact.first_name}},

This is my last email about AllTheCalls. I don't believe in pestering people.

Here's the bottom line: if {{contact.business_name}} gets phone calls from customers or leads, an AI receptionist will save you time, capture more business, and make your callers' experience better.

If the timing isn't right, I completely understand. But if you ever want to revisit:

- Demo line (call anytime): (316) 232-4777
- Website: allthecalls.ai
- My email: hello@allthecalls.ai

I appreciate your time either way, {{contact.first_name}}.

Brayden Myers
Founder, AllTheCalls.ai

Reply STOP to unsubscribe.
```

---

## Workflow 6: Review & Referral

**Workflow Name:** `WF-06 Review + Referral Request`
**Trigger:** Two conditions must BOTH be true:
1. Tag "active" is present
2. Custom field `signup_date` is 30+ days ago

> **GHL Implementation:** Use a Date-based trigger or a separate workflow that runs daily, checks contacts with tag "active" where signup_date is 30 days ago, and who do NOT have tag "review-requested". Alternatively, this is handled inline in Workflow 3's Day 30/37 steps. This standalone workflow is for clients who signed up before the onboarding drip was implemented, or as a backup.

### Step-by-Step Flow

```
TRIGGER: Contact matches conditions (active + 30 days since signup)
│
├─ CONDITION: Does contact have tag "review-requested"?
│  ├─ IF yes: → END (already asked)
│  └─ ELSE: continue
│
├─ ACTION: Add Tag → "review-requested"
├─ ACTION: Send Email → "Review Request"
│
├─ WAIT: 7 days
│
├─ CONDITION: Does contact have tag "referral-asked"?
│  ├─ IF yes: → END
│  └─ ELSE: continue
│
├─ ACTION: Add Tag → "referral-asked"
├─ ACTION: Send Email → "Referral Ask"
│
├─ END
```

### Email Copy

> The Review Request and Referral Ask emails are identical to the Onboarding Day 30 and Day 37 emails documented in Workflow 3 above. Use the same templates.

---

## Implementation Notes

### GHL Setup Checklist

1. **Create Custom Fields** — Add all fields from the Master Custom Fields table under Settings > Custom Fields
2. **Create Pipeline** — "AllTheCalls Sales Pipeline" with all 9 stages listed above
3. **Create Tags** — All tags will auto-create when workflows use them, but pre-creating avoids typos
4. **Set Up Calendars** — Create a demo booking calendar for Workflow 2
5. **Configure Inbound Webhooks** — Create webhook URLs for:
   - Stripe payment events (Workflow 3 trigger)
   - Trillet agent creation callback (Workflow 3 custom field update)
6. **Build Workflows** — Create all 6 workflows following the step-by-step flows above
7. **Create a Reply Detection Workflow** — Separate workflow for cold outreach exit (see Workflow 5 note)
8. **Connect Stripe** — Either via native GHL Stripe integration or via serverless middleware that forwards events to GHL webhooks
9. **Set Up Email Domain** — Verify `allthecalls.ai` in GHL for sending (in addition to existing Resend verification)
10. **Configure SMS** — Set up Twilio/GHL phone number for sending SMS messages

### Webhook Architecture for Workflow 3

```
Stripe (payment succeeds)
  │
  ▼
Serverless Function (existing Vercel endpoint)
  │
  ├─── Creates Trillet agent (existing logic)
  │
  ├─── POST to GHL Inbound Webhook #1 (trigger Workflow 3)
  │    Payload: contact info + plan + Stripe IDs + trial dates
  │
  └─── POST to GHL Inbound Webhook #2 (update custom fields)
       Payload: email + trillet_flow_id + trillet_agent_id + portal_access_url
```

This way, the existing serverless function handles Trillet creation, and GHL handles all CRM + communication. Brayden's one manual step remains: assigning phone numbers in the Trillet dashboard.

### Reply STOP Compliance

All SMS messages include "Reply STOP to opt out." When a contact replies STOP:
- GHL natively handles opt-out for its own SMS
- Add tag "do-not-contact" as a safety measure
- All workflows check for this tag before sending

### Testing Order

1. Test Workflow 1 first (simplest — form submission)
2. Test Workflow 5 second (cold outreach — most messages)
3. Test Workflow 2 (requires calendar setup)
4. Test Workflow 3 (requires Stripe + Trillet webhook wiring)
5. Test Workflow 4 (requires manual tag trigger)
6. Test Workflow 6 (requires 30-day wait — test with modified dates)
