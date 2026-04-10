---
name: objection-handler
description: >
  Build or extend an objection library for your ICP — common objections mapped to
  the real concern underneath, with response frameworks your team can use in live deals.
---

# Objection Handler

Use this skill to build a structured objection library tailored to your ICP and product.
Each entry surfaces the real concern behind the objection and gives reps a response
framework — not a script, but a way to think through it.

No CLI or special tools required.

## Input

Ask the user for:
- **ICP and product context** — who you sell to and what you sell (or pull from `extract-icp` if available)
- **Known objections** _(optional)_ — objections the team already hears; if provided, start there
- **Focus area** _(optional)_ — "budget objections", "timing objections", "we're already using X"

If no specific objections are provided, generate a set from first principles based on the ICP.

## Step 1 — Identify the objection set

Common objection categories for B2B sales:

**Budget and cost**
- "It's too expensive" / "We don't have budget right now"
- "We need to see ROI before we can commit"
- "We're in a budget freeze"

**Timing**
- "Not a priority right now" / "Come back in Q3"
- "We're in the middle of [initiative] — bad timing"
- "Let's revisit after [event]"

**Competitive / status quo**
- "We're already using [competitor]"
- "We built something internally"
- "We do this with spreadsheets / manually and it works fine"

**Trust and risk**
- "We've never heard of you" / "You're too small / new"
- "We had a bad experience with a similar tool"
- "What happens to our data?"

**Internal process**
- "I need to get buy-in from [stakeholder]"
- "I'm not the decision maker"
- "IT / Legal / Security needs to review this first"

**Product fit**
- "We're too small / too big for this"
- "It doesn't integrate with [tool we use]"
- "We don't need all of this — it's overkill"

Ask the user which categories to prioritise, or cover all if no preference given.

## Step 2 — Build the objection library

For each objection, produce an entry in this format:

```
OBJECTION — "[Exact words the prospect uses]"

REAL CONCERN
  [What this objection usually means underneath — what fear or constraint is driving it?]
  [Is it a real objection or a brush-off? How to tell the difference.]

RESPONSE FRAMEWORK
  1. Acknowledge — [how to validate the concern without agreeing it's a dealbreaker]
  2. Clarify — [question to ask that gets to the real concern underneath]
  3. Reframe — [how to shift the frame without being defensive]
  4. Evidence — [what proof point, story, or data point is most relevant here]
  5. Next step — [what to ask for at the end of the response]

EXAMPLE RESPONSE
  "[A natural-sounding response that follows the framework — not a script, a model]"

SIGNALS THAT IT'S A REAL OBJECTION VS A BRUSH-OFF
  Real: [what the prospect says or does that means this is a genuine blocker]
  Brush-off: [what signals this isn't the real issue]

WHAT NOT TO SAY
  - [Response that commonly backfires with this persona]
  - [Trap that reps often fall into]
```

## Step 3 — Signal-informed variants (optional)

If signal data is available (from Saber or any source), add variants for specific contexts:

```
SIGNAL-INFORMED VARIANT — "[Objection]"

IF prospect recently raised funding:
  → [How the response changes — funding reframes the budget conversation]

IF prospect hired a new [title] in the last 6 months:
  → [How the response changes — new leadership = open evaluation window]

IF prospect is actively hiring in [relevant role]:
  → [How the response changes — growth signals urgency]
```

## Step 4 — Output the full library

Present the objection library as a structured, shareable reference. Offer to:
- Format it as a Notion table or Google Doc outline
- Export as markdown the team can add to their sales playbook
- Prioritise the top 5 objections for a quick-reference card

## Step 5 — Suggest next steps

- Add the most common objections to `build-sequence` — proactively address them in later touchpoints
- Use the "real concern" framing in `write-outreach` to acknowledge pain without sounding salesy
- Combine with `competitive-intel` battle cards for objections involving competitors
- Re-run this skill when entering a new segment or after a batch of lost deals
