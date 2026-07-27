---
name: event-prospecting
description: >-
  Build a meeting plan for a conference or event you're attending. Turn an attendee
  list into a tiered plan — buyers, peers, and collaborators — with per-person
  outreach drawn from what each attendee actually posted, and ship one self-contained
  plan you walk in with. Works from any attendee source (Slack export, event app,
  registration CSV, or a pasted list); uses the Saber CLI for email and contact
  signals when available, with a manual fallback. Triggers on "prep for [conference]",
  "who should I meet at [event]", "research attendees for [event]", "build a meeting
  plan for [event]", "find buyers at [event]", "I'm going to [conference] — who's there".
---

# Event Prospecting

This skill plans the event itself rather than flattening it into a lead list: it sorts attendees into who can buy, who's a peer, and who can open doors, writes outreach from what each one actually posted, and produces one plan you walk into the event with.

## Goal

Produce a single, self-contained meeting plan for an event you are attending: every attendee tiered (buyer / peer / collaborator), graded for whether they are actually attending, and paired with copy-ready, personalised outreach — so you arrive knowing exactly who to meet, why, and what to say.

## When to use

- "Prep for SaaStr — who should I meet?"
- "Build a meeting plan for [conference]."
- "Research the attendees for [event] and tell me who the buyers are."
- "I'm going to [conference] next week — who's there worth a conversation?"

Do **not** use this for building a generic prospect list with no event attached (use `build-contact-list`), or for a deep dossier on one named company (use `research-account`).

## Step 1 — Scope the event

Confirm three things before gathering anyone. Ask if they are not already clear:

1. **The event** — name, dates, and city.
2. **The attendee source** — where the list of people comes from (see Step 3). Anything works: an event-app or community export, a registration or RSVP CSV, a sponsor/speaker page, or a pasted list of names.
3. **Who counts as a buyer** — the ICP for the sell-list (e.g. "RevOps and GTM-ops leaders at B2B companies"). Everyone who is not a buyer is a peer or a collaborator, not a target.

If the event or the source is ambiguous, ask one clarifying question rather than guessing.

## Step 2 — Recon the event

Establish the facts before touching the list. Pull from the registration email and the event website:

- Dates, venue, kickoff time, badge pickup.
- The official **agenda, speaker list, and sponsor list**. Speakers and sponsors are useful two ways: speakers are confirmed attendees by definition (Step 5), and sponsor staff are peers.
- Any side events, dinners, or community meetups — these become the at-event playbook.

## Step 3 — Get the attendee roster (the roster is the membership, not just who posted)

The whole list of people is the roster — not only the handful who post or RSVP publicly. Most attendees are silent. Gather the fullest membership you can from whatever source the user has:

- An **event-app or community export** (many events have a Slack/Discord/Luma community — export or copy its member list).
- A **registration / RSVP / attendee CSV**.
- The **speaker and sponsor lists** from the event site.
- People publicly **posting that they're attending** (high-intent; capture them).
- A **pasted list** of names the user already has.

Profile data from these sources is usually sparse (often just a name). Names are the spine; titles, companies, and contact details come from enrichment in Step 4.

If the attendee source is a community whose messages you can read, mine the messages for context — side-event invites, badge-pickup timing, "build vs buy" questions, "I'm looking for X at this event" intent. These become quotes and timing cues later. Treat messages as context; treat membership as the roster.

## Step 4 — Enrich each attendee

For each person, gather the following with whatever enrichment tools are available — a LinkedIn MCP, Apollo, Clay, or manual lookup. Use everything you have; where a field can't be read cleanly, leave it empty rather than guessing (an empty field is correct; a wrong one is not).

- **Connection degree** (1st / 2nd / 3rd) — a 1st-degree connection is warm; you can message directly.
- **Headline and location** (verbatim headline).
- **Recent posts, with their dates.** The age of a post ("2d", "3w", "1mo") decides the outreach tactic (Step 7). Capture the verbatim text and the age. While reading, note any **attendance signal or counter-signal** (Step 5): a post about attending *this* event/city is confirming; a post about a *different* event/city is a non-attendance red flag.
- **Follower count — from the person's profile page, not the activity feed.** The follower number shown on an activity feed is often the *reposted* company's count, not the person's, and can be off by orders of magnitude. Read it from the profile directly.

**On public-only runs, connection degree and follower count are often unavailable** (they usually need an authenticated, logged-in session). Leaving them blank is the expected default, not a failure — the plan renders "no data" and the **warm-connector tier simply stays empty** rather than being forced. Grade what you can see; never invent a degree or a follower number to fill the field.

**Emails.** If the Saber CLI is available, it is the fast path — but resolve the person's **current employer first** (read the current-role entry on their profile; the first company listed is not reliably the current one), then:

```bash
saber contact find-email --full-name "First Last" --domain currentcompany.com --json -y
```

It returns a verification state (deliverable / risky / not-found) and a score. If it returns `risky` or `not-found`, re-check the company — do not ship a guessed address. **Without the Saber CLI**, use any email-finding tool you have, or fall back to a LinkedIn/Slack DM and leave the email empty. Never invent a domain.

**Confidence filtering.** Only treat a fact as confirmed when it is solid (a clear profile match, a primary source). Low-confidence name-matches go in a "verify in person" list, never on the live outreach list.

## Step 5 — Grade attendance confidence (do this before anyone reaches the outreach list)

**Being on the list is not the same as attending.** People join an event's community, or appear on a scraped roster, without ever showing up. Grade every person before they reach the outreach list:

- **CONFIRMED** — explicit evidence: a logistics message (badges, the dinner, hotels, "excited for this"), a post saying they're attending *this specific event/city*, or a slot on the **official speaker/sponsor agenda** (presenters attend their own sessions, so they are confirmed by definition).
- **LIKELY** — a supporting signal (local to the host city, sponsor staff) but no explicit personal confirmation. Keep them on the list, but plan to confirm in the first message.
- **UNCONFIRMED / RISK** — membership or roster presence only, no confirmation. Keep these **off** the "message now" list.

The RISK grade matters most when your source is a **community, registration, or membership roster**, where being on the list does not prove someone will show. If your *only* source is the **official agenda / speaker / sponsor list**, then by definition everyone on it is CONFIRMED and there is no natural RISK population — that is expected, not a gap. In that case, still apply the counter-signal check below, and put any genuinely unverifiable identity-matches into the "verify in person" list rather than forcing a RISK badge.

**The counter-signal check — do not bury it.** If a person's event-related posts are about a *different* city or event than the one you're prospecting, that is a red flag for non-attendance. Surface it in the evidence; do not write it off as "old posts." On-topic content is not attendance.

> A worked example, anonymised: a member sat in a London event's community channel, so an early draft listed them as attending. Every event post of theirs, though, was about a different city entirely — they were not attending London at all, and confirmed as much later. The counter-signal was sitting in plain sight. Conversely, listed speakers were initially under-graded: anyone on the official agenda is confirmed by definition and should never be parked in "verify."

Record the grade and the one-sentence evidence on each person. The deliverable renders this as a coloured badge (Confirmed / Likely / Unconfirmed), and risk-graded people are visually separated and kept out of the live send sequence.

## Step 6 — Tier the roster

Separate three groups explicitly. Conflating them is the cardinal error of this skill:

- **(a) Buyers** — your practitioner ICP, the people who would actually use the product. This is the sell-list: meet, qualify, follow up.
- **(b) Peers / partners** — sponsor staff, competitors, consultants in the same space. This is ecosystem and co-sell, **not** a sale.
- **(c) Collaborators / amplifiers** — high-reach creators, community owners, podcast hosts. This is partnership and co-content (guesting, intros, amplification), **not** a sale.

Never put a peer or a collaborator on the sell-list.

Then rank the **buyers** by intent × warmth × reachability:

- **Tier A** — best fit and reachable now: a live in-community ask, a 1st-degree connection, a pre-qualified "here's what I want at this event" post, or local + senior. A buyer with a **live, dated buying signal** ("anyone doing build-vs-buy on X? beer on me", two days ago) is rank 1 regardless of seniority — stated pain beats title.
- **Tier B** — good fit, slightly less reach or seniority: connect and DM at the event.
- **Tier C** — nurture, adjacent, or early-stage: low priority.

Attendance gates the order (Step 5): only CONFIRMED and LIKELY people belong in the live send sequence. A confirmed listed speaker outranks an unconfirmed roster-only name of the same fit, because one is provably in the room.

## Step 7 — Write the outreach (per person, psychology-first)

Every message has **one job: a yes to ~15 minutes in person.** The pitch happens when you meet, not in the message.

- **Ask for their expertise; don't pitch.** "I'd value your take on X" beats "let me show you my tool." Show up as a peer.
- **Drive the angle from what they actually posted** — not from their title. If there's no usable post, write a clean peer opener; never fabricate a hook.
- **Quote them, don't paraphrase.** Reference the exact line they wrote, verbatim.
- **Mind the timing.** Only suggest *commenting* on a post if it is ≤2–3 days old. Most captured posts are older, so the play is a **DM that quotes the post**, not a public comment. Always note the post's date so the choice is obvious.
- **List every channel; don't rank one as "lowest friction".** Surface every way to reach a person (community DM, LinkedIn, in-person, email where verified) — you can't predict who answers where. Note: LinkedIn connection-request notes often go through blank, so connect without a note and DM after connecting rather than relying on the note.
- **Keep it low-risk with an easy out** — short, a specific time, "no worries if you're slammed."

Produce a couple of copy-ready drafts per person (e.g. a community DM and a LinkedIn DM, or an in-person opener), labelled by channel.

## Step 8 — Build the plan (the deliverable)

Produce **one self-contained HTML file** — the plan you walk in with. Keep everything inline so it opens from a single file with no server. Build it from a single data object so the structure is consistent. The plan has:

- An **event header**: the name, dates, venue, kickoff, a "today / N days out" line, and the attendee source.
- An **action board** (top): the ordered send sequence — who to message, in what order, with checkboxes and a jump-to-card link for each. Risk-graded names are visibly separated or excluded.
- **Tiered sections**: Buyers Tier A, the warm connector(s), Buyers Tier B, Collaborators, Buyers Tier C — each a set of person cards.
- **Person cards** (collapsible), each showing: name, attendance badge (Confirmed / Likely / Unconfirmed) with its evidence, role and company, location, connection degree, follower count, verbatim headline, reachable channels, the outreach angle, their verbatim dated quotes (with a "fresh — can comment" marker only when ≤2–3 days old), and the copy-ready drafts with copy buttons.
- A **side-events** section (verbatim invites), an **at-event day-by-day playbook**, a **peers/sponsors** section (kept off the sell-list), and a **"verify in person"** table for low-confidence name-matches.

Keep the layout clean and legible: a clear visual separation between the three groups, and one consistent colour per attendance grade (for example green for Confirmed, amber for Likely, red for Unconfirmed). The exact styling is yours to choose — the structure and the data below are what must stay consistent.

A compact shape for the single data object that drives the file:

```
{
  event:  { brandName, h1, sub, facts:[{label,value}], foot },
  seq:    [ [orderedActionRows] ],            // the action board
  tierA:  [ person ], warm: [ person ], tierB: [ person ],
  collab: [ person ], tierC: [ person ],      // collab = amplifiers, NOT buyers
  events: [ {when,title,body} ],              // side events, verbatim
  playbook:[ {title, items:[]} ],             // at-event, day by day
  partners:[ {title, body} ],                 // peers/sponsors, NOT the sell-list
  verify: [ {source, matched, issue} ]        // low-confidence matches
}

person = {
  name, att:[level, evidence],                // level: "conf" | "likely" | "risk" — REQUIRED on everyone
  role, loc, deg, foll, headline,
  channels,                                   // ALL channels; never rank one "lowest friction"
  timing:[mode, text],                        // mode "now" (comment window open) | "dm" (quote the post)
  angle,
  quotes:[ [src, date, fresh, text] ],        // text VERBATIM; fresh=true only if ≤2–3 days old
  drafts:[ [label, text] ],                   // copy-ready, labelled by channel
  email, emailConfidence                      // "" if unresolved — never a guess
}
```

## Step 9 — Verify before you call it done

Do not declare the plan finished until you have actually rendered it and checked it:

- **Open the HTML** in a browser (or render it however you can) and confirm there are no errors and every section is populated. If no browser is available in your environment, validate the data object instead — confirm every list is populated and every person carries the required fields — and confirm the file opens.
- **Audit the data against the source**: every quote is verbatim, every email matches what was resolved, every follower count came from a profile page, every post date is real.
- **Check the tiering**: no buyer mis-filed as a collaborator, no peer on the sell-list.
- **Check attendance**: every person carries a grade, the badges render, and no unconfirmed/risk person sits in the live "send now" sequence.

## Hand off

After the plan is built:

- Use `research-account` to go deeper on any company a buyer works at before the meeting.
- Use `write-outreach` to expand a card's drafts into a fuller sequence if a meeting doesn't land at the event.
- Use `enrich-contacts` to resolve missing emails or phone numbers for the people you most want to reach.
