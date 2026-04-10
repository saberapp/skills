---
name: build-sequence
description: >
  Design a multi-step outreach sequence (email + LinkedIn) with messages personalised
  using Saber signal results and timing tied to signal triggers.
---

# Build Sequence

Use this skill to design a full multi-step outreach sequence for a target persona, with each touchpoint personalised using Saber signal data and timing informed by signal triggers.

## Goal

Produce a complete sequence plan: N steps, each with a channel, timing, and a personalised message template that references specific signals.

## Step 1 — Establish context

Confirm from conversation context or ask:
- **Target persona:** job title, seniority, department
- **Target company profile:** industry, size, signals that matter
- **Sender:** name, company, value prop in one sentence
- **Goal of the sequence:** book a call, get a reply, drive a trial sign-up

If a `signal-discovery` run has already happened in this conversation, use those ICP and signal definitions directly.

## Step 2 — Pull signal context from Saber

Signal results personalise each step. Pull what's available before designing the sequence.

**If the Saber CLI is available** (`saber --help` works):

Check for existing subscription results covering the target accounts:
```bash
saber subscription list
saber subscription get <subscriptionId>
```

For contact-level context (if LinkedIn URLs are known):
```bash
saber signal --profile <linkedin-url> --question "Is this person posting about [relevant topic]?"
```

If no results exist yet, ask the user what signals they typically track and design the sequence around those signal hypotheses — placeholders can be filled in once signals run.

**If the Saber CLI is not available:** design the sequence with signal placeholder hooks (e.g. `[IF hiring signal: ...]`) that the user can personalise once they have data.

## Step 3 — Design the sequence structure

Ask the user their preferred sequence length and mix, or recommend based on the persona:

**Recommended structure for cold outreach (B2B SaaS):**

| Step | Day | Channel | Purpose |
|------|-----|---------|---------|
| 1 | 0 | Email | Signal-led first touch |
| 2 | 3 | LinkedIn | Connection request with context |
| 3 | 7 | Email | Value-add follow-up (resource or insight) |
| 4 | 12 | LinkedIn | Engage on their content (comment or message) |
| 5 | 18 | Email | Direct ask / break-up |

Adjust based on the persona (executives = shorter, fewer steps; SDR targets = more touches).

## Step 4 — Write each step

For each step, write a message template. Use the strongest available signal in Step 1, and distribute different angles across the sequence (don't repeat the same hook).

**Step 1 — Signal-led first touch (email)**
Use the highest-confidence signal as the opener. See `write-outreach` for format guidelines.

**Step 2 — LinkedIn connection request**
Reference the email or a different signal angle. 300 chars max. No hard CTA.

**Step 3 — Value-add follow-up (email)**
Don't push the product again. Share something useful: a relevant case study, a data point tied to their signals, a framework for the problem you solve. Soft CTA.

**Step 4 — LinkedIn engagement**
If the contact has posted recently (use contact-level Saber signals or check LinkedIn), comment meaningfully on their post. If not, send a short message referencing the earlier email.

**Step 5 — Direct ask / break-up (email)**
Be direct. "Is this a priority for you right now?" or "Should I stop reaching out?" — honest, low-friction. Often the highest-response step.

## Step 5 — Add signal-triggered variants

For each step, add a conditional variant based on key signals:

```
Step 1 — IF hiring signal positive:
  "I saw [company] is scaling the sales team — most RevOps leaders at that stage are dealing with [problem]..."

Step 1 — IF funding signal positive:
  "Congrats on the recent round — [company] is clearly in growth mode. Teams at this stage often prioritise [problem]..."

Step 1 — IF no strong signals:
  [Fall back to persona-level pain without a specific event hook]
```

## Step 6 — Present the sequence

Output the complete sequence as a structured plan, each step clearly showing:
- Channel and timing
- Full message copy
- Which signal(s) it references
- Conditional variants where applicable

Offer to export as a format the user's sequencing tool (Outreach, Salesloft, Apollo, HubSpot sequences) can import.
