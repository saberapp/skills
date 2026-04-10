---
name: persona-research
description: >
  Build a deep profile of a buying persona — motivations, challenges, buying behaviour,
  language they use, and where to find them. Helps reps understand who they're selling
  to before they reach out.
---

# Persona Research

Use this skill to build a rich profile of a target buyer persona. The output gives reps
the context they need to engage the right person with the right message — regardless of
what tools you use for prospecting.

No CLI or special tools required. Works from web research alone.

## Input

Ask the user for:
- **Job title / persona** — e.g. "Head of RevOps", "VP of Sales", "CFO at a Series B SaaS"
- **Company profile** — the type of company this persona works at (size, industry, stage)
- **Context** _(optional)_ — any known information about this persona from deals or calls

If ICP context is already available from `signal-discovery` or `extract-icp`, use the buying
committee from there and skip asking.

## Step 1 — Research the persona

Use web search and any available tools to answer these questions:

**Role and context**
- What does this person own? What are they measured on?
- Who do they report to? Who do they manage?
- What does success in this role look like at the end of the year?

**Day-to-day reality**
- What does a typical week look like for them?
- What meetings do they run or attend?
- What decisions are they responsible for?

**Challenges and frustrations**
- What slows them down?
- What problems keep coming up that they haven't solved?
- What did the job posting say about the challenges this role will tackle?

**Buying behaviour**
- Do they initiate purchases or respond to internal requests?
- How do they evaluate new tools? (trials, demos, peer recommendations, analyst reports)
- Who else is involved in the decision? Do they control budget?
- How long do decisions typically take at their company stage?

**Language and content**
- What words and phrases do they use to describe their problems? (pull from reviews, LinkedIn posts, conference talks)
- What content do they consume? (newsletters, podcasts, communities, conferences)
- What topics do they post about?

**Best sources:**
- LinkedIn job postings for this title — the "you will" and "you have" sections describe the person
- G2 / Capterra reviews written by this persona — the "pros/cons" language is gold
- LinkedIn posts from people in this role
- Conference session titles and speaker bios
- Substack / newsletter bylines

## Step 2 — If Saber CLI is available (optional enrichment)

Run contact-level signals on specific people in this role:
```bash
saber signal --profile <linkedin-url> --question "Is this person posting about [relevant challenge] recently?"
saber signal --profile <linkedin-url> --question "Has this person been in their current role for less than 12 months?"
```

## Step 3 — Output the persona profile

```
PERSONA — [Title] at [Company Type]

ROLE CONTEXT
  Reports to:    [who they typically report to]
  Manages:       [team / function / budget they own]
  Key metrics:   [what they're measured on — e.g. pipeline generated, quota attainment, CAC]

DAY-TO-DAY
  [2–3 sentences describing what they actually spend their time on]

GOALS
  Short-term:  [what they're trying to accomplish this quarter]
  Long-term:   [what career or company outcomes they're working toward]

CHALLENGES
  1. [Specific, observable challenge — use their language]
  2. [...]
  3. [...]

BUYING BEHAVIOUR
  Initiator:       [Do they start the search or respond to one?]
  Evaluation:      [How do they assess vendors — trials, demos, peer refs, G2?]
  Decision power:  [Do they own budget? Who else signs off?]
  Timeline:        [How long does a decision typically take at their stage?]

LANGUAGE THEY USE
  [Exact phrases pulled from reviews, posts, and job descriptions — not paraphrased]
  - "[quote from a G2 review or LinkedIn post]"
  - "[another phrase they use]"

WHERE TO FIND THEM
  Online:       [communities, Slack groups, subreddits, LinkedIn groups]
  Content:      [newsletters, podcasts, blogs they read]
  Events:       [conferences or meetups they attend]

WHAT TO AVOID
  - [Common mistake reps make with this persona]
  - [Messaging that lands poorly — e.g. features they don't care about]
  - [Tone or approach that feels off]
```

## Step 4 — Suggest next steps

- Use this profile to guide messaging in `write-outreach` or `build-sequence`
- Use the "language they use" section when writing signal questions in `generate-signals`
- Share with the team as a reference doc before a campaign kicks off
