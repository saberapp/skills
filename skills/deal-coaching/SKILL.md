---
name: deal-coaching
description: >
  Analyse a deal in progress — assess health, identify risks, map the buying committee,
  and recommend specific next steps to move it forward.
---

# Deal Coaching

Use this skill to get a structured analysis of a specific deal. Bring deal context and
get back: a health assessment, risk flags, stakeholder map, and concrete next steps.

Works without any CLI. If the Saber CLI is available, it can run live signals on the
account to add real-time context.

## Input

Ask the user to describe the deal. Collect:

- **Company** — name, domain, size, industry
- **Contacts engaged** — names, titles, how active they've been
- **Deal stage** — where it sits in the pipeline
- **What's happened so far** — demos done, proposals sent, objections raised
- **Known blockers** — what's slowing things down
- **Timeline / deadline** — is there urgency? A budget cycle? A renewal date?
- **Competitive situation** — are competitors in the deal?

Collect missing context conversationally — don't dump all questions at once.

## Step 1 — Account research (optional enrichment)

If the Saber CLI is available and the domain is known, run a quick signal check:
```bash
saber signal --domain <domain> --question "Has this company hired a new [buyer title] in the last 6 months?" --yes
saber signal --domain <domain> --question "Is this company actively growing its [relevant team]?" --yes
```

If no CLI, use web search to check for recent news: funding, leadership changes, layoffs,
product launches, or any event that changes urgency or risk.

## Step 2 — Deal health assessment

Score the deal across these dimensions:

**Champion** — Is there someone inside who wants this to happen?
- Strong: actively scheduling meetings, sharing internal context, coaching on the process
- Weak: hard to reach, not advocating internally, "I'll try to get others involved"

**Economic buyer** — Have you reached the person who controls budget?
- Strong: engaged in the process, has asked about pricing
- Weak: never spoken with, champion says "they'll rubber-stamp it"

**Pain** — Is the pain real, specific, and urgent?
- Strong: prospect has described a specific problem with a business impact
- Weak: "we'd be interested in seeing what this does"

**Timeline** — Is there a forcing function?
- Strong: renewal date, board commitment, end of budget cycle
- Weak: "sometime this year", "no rush"

**Competition** — What's the alternative if you don't win?
- Strong: you understand what else is being evaluated and why
- Weak: you don't know who else they're talking to

**Process** — Do you understand how they make decisions?
- Strong: you know the steps, who's involved, what happens next
- Weak: every meeting ends with "we'll get back to you"

Rate each dimension: **Strong / Weak / Unknown**

## Step 3 — Risk flags

Identify the top 2–3 risks in this deal:

```
RISK FLAGS

🔴 [Risk name]: [What's happening / not happening that signals this risk]
   → [What to do about it]

🟡 [Risk name]: [Watch area — not a crisis but worth monitoring]
   → [What to do about it]
```

Common risk patterns:
- **No champion** — you're selling to someone who can't say yes
- **Single-threaded** — only one contact, no access to the buying group
- **No urgency** — deal will drag forever without a forcing function
- **Ghosting** — responses are slowing down after an initial positive signal
- **Scope creep** — deal is growing but decision criteria keep shifting
- **Competitor late entry** — a new name just appeared in the last call

## Step 4 — Stakeholder map

Based on known contacts, map the buying committee:

```
STAKEHOLDER MAP

Champion:        [Name] — [title] — [assessment: strong / developing / unclear]
Economic buyer:  [Name or "not yet reached"]
Influencers:     [Names, titles, stance if known]
Blockers:        [Anyone who might slow or kill the deal]
Missing:         [Roles you haven't reached yet that typically need to be involved]
```

## Step 5 — Recommended next steps

Produce 3–5 specific, actionable next steps ranked by priority:

```
NEXT STEPS

1. [Specific action] — [Why this, why now]
   How: [Exact ask or message to send]

2. [...]

3. [...]
```

Good next steps are:
- **Time-bound** — has a proposed date or deadline
- **Specific** — not "follow up" but "send the ROI template to [Name] by [date]"
- **Oriented toward advancing** — not just maintaining contact

## Step 6 — Suggested messaging

For the most important next step, draft the outreach:
- Email subject + body
- Or LinkedIn message
- Reference the deal context and most relevant signal or pain point

Offer to use `write-outreach` for a fuller personalisation pass if signal data is available.
