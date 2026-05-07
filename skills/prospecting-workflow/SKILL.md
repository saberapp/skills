---
name: prospecting-workflow
description: >
  End-to-end guided workflow — build a Saber company list, run research signals across
  every company in parallel, find named contacts, and export a combined CSV to the desktop.
  Walks the user through each stage with confirmations and a credit-discipline guard. Use
  when you want one signed-off CSV instead of running build-account-list,
  create-company-signals, and build-contact-list separately.
---

# Prospecting Workflow

A guided workflow for:
1. Building a filtered company list with a live count preview
2. Running research signals against every company in the list
3. Searching for contacts at those companies
4. Exporting a combined CSV (companies + signals + contacts) to `~/Desktop/`

Uses the Saber CLI (`/opt/homebrew/bin/saber`). Auth is handled automatically via stored credentials — no API key needed.

---

## Trigger Discipline — Read This First

`saber subscription trigger` has **no idempotency and no cache**. Every call spawns a fresh batch of signal runs across every company in the list and charges credits for every one — including companies already answered by a prior trigger. Re-triggering is how credit budgets get destroyed.

**Rule:** Never call `subscription trigger` on a subscription whose `lastRunAt` is non-null, or that already has signals in `saber signal list --subscription-id <id>`. Use the pre-trigger guard in Stage 3. No exceptions — not for stalled signals, not for "just to be safe", not for retries.

**A second, equally important rule: never call `saber subscription start` in this workflow.** Subscriptions are created with `--run-once`, which is what keeps them as one-off jobs (no Temporal schedule, cron field dormant). Calling `start` materializes the Temporal schedule from the cron expression and converts every "one-off" into a recurring monthly job that will fire on its own at the cron's next tick. There is no DELETE endpoint for company signal subscriptions, so a started subscription must be `stop`ped to disarm — and any drift to `status=active` is the failure mode that drains credits in bulk.

**Safe vs guarded commands:**

| Command | Type | Can you call it freely? |
|---|---|---|
| `saber signal list --subscription-id <id>` | read-only | Yes — poll as often as you like |
| `saber signal get <signalId>` | read-only | Yes |
| `saber subscription get <id>` | read-only | Yes |
| `saber subscription list` | read-only | Yes |
| `saber list company companies <listId>` | read-only | Yes |
| `saber list company count-preview` | read-only | Yes |
| `saber subscription stop <id>` | safe write | Yes — disarms a runaway active subscription |
| `saber subscription trigger <id>` | **guarded write** | **Only via the pre-trigger guard — aborts if already run** |
| `saber subscription start <id>` | **forbidden in this workflow** | **Never. Converts one-off subs into recurring monthly jobs.** |

Progress polling during a running batch uses only read-only commands. It never interacts with `trigger` and never causes re-runs. Poll freely.

**Session resumption:** If you pick up a conversation that already has a subscription ID (from prior context, a passed-in ID, or anything other than a subscription you just created in the current session), assume it has already been triggered. Run the guard before any trigger call — never assume "fresh". Also verify `status=stopped` before doing anything else; if it's `active`, stop it immediately.

---

## Stage 1: Filter Setup & Preview

Ask the user for filters. Collect the following:

**Industry** (required)
Must be an exact string from the list below. Validation is strict and case-sensitive. If the user's input doesn't exactly match, find the closest valid option, show it to them, and confirm before proceeding. Multiple industries are supported (repeatable flag).

Common pitfalls:
- `marketing & advertising` is NOT valid → use `advertising services` or `marketing services`
- `hospitals & health care` is NOT valid → use `hospitals and health care`
- `information technology & services` IS valid (ampersand kept)
- `software development` IS valid

**Full valid industry list:**
`abrasives and nonmetallic minerals manufacturing`, `accessible architecture and design`, `accessible hardware manufacturing`, `accommodation and food services`, `accounting`, `administration of justice`, `administrative and support services`, `advertising services`, `agricultural chemical manufacturing`, `agriculture, construction, mining machinery manufacturing`, `air, water, and waste program management`, `airlines and aviation`, `alternative dispute resolution`, `alternative fuel vehicle manufacturing`, `alternative medicine`, `ambulance services`, `amusement parks and arcades`, `animal feed manufacturing`, `animation`, `animation and post-production`, `apparel & fashion`, `apparel manufacturing`, `appliances, electrical, and electronics manufacturing`, `architectural and structural metal manufacturing`, `architecture and planning`, `armed forces`, `artificial rubber and synthetic fiber manufacturing`, `artists and writers`, `arts & crafts`, `assurances`, `audio and video equipment manufacturing`, `automation machinery manufacturing`, `automotive`, `aviation & aerospace`, `aviation and aerospace component manufacturing`, `baked goods manufacturing`, `banking`, `bars, taverns, and nightclubs`, `bed-and-breakfasts, hostels, homestays`, `beverage manufacturing`, `biomass electric power generation`, `biotechnology`, `biotechnology research`, `blockchain services`, `blogs`, `boilers, tanks, and shipping container manufacturing`, `book and periodical publishing`, `book publishing`, `breweries`, `broadcast media production and distribution`, `building construction`, `building equipment contractors`, `building finishing contractors`, `building materials`, `building structure and exterior contractors`, `business consulting and services`, `business content`, `business intelligence platforms`, `business supplies & equipment`, `cable and satellite programming`, `capital markets`, `caterers`, `chemical manufacturing`, `chemical raw materials manufacturing`, `child day care services`, `chiropractors`, `circuses and magic shows`, `civic and social organizations`, `civil engineering`, `claims adjusting, actuarial services`, `clay and refractory products manufacturing`, `climate data and analytics`, `climate technology product manufacturing`, `coal mining`, `collection agencies`, `commercial and industrial equipment rental`, `commercial and industrial machinery maintenance`, `commercial and service industry machinery manufacturing`, `commercial real estate`, `communications equipment manufacturing`, `community development and urban planning`, `community services`, `computer and network security`, `computer games`, `computer hardware`, `computer hardware manufacturing`, `computer networking`, `computer networking products`, `computers and electronics manufacturing`, `conservation programs`, `construction`, `construction hardware manufacturing`, `consumer electronics`, `consumer goods`, `consumer goods rental`, `consumer services`, `correctional institutions`, `cosmetics`, `cosmetology and barber schools`, `courts of law`, `credit intermediation`, `cutlery and handtool manufacturing`, `dairy`, `dairy product manufacturing`, `dance companies`, `data infrastructure and analytics`, `data security software products`, `defense & space`, `defense and space manufacturing`, `dentists`, `design`, `design services`, `desktop computing software products`, `digital accessibility services`, `distilleries`, `e-learning`, `e-learning providers`, `economic programs`, `education`, `education administration programs`, `education management`, `electric lighting equipment manufacturing`, `electric power generation`, `electric power transmission, control, and distribution`, `electrical equipment manufacturing`, `electronic and precision equipment maintenance`, `embedded software products`, `emergency and relief services`, `engineering services`, `engines and power transmission equipment manufacturing`, `entertainment`, `entertainment providers`, `environmental quality programs`, `environmental services`, `equipment rental services`, `events services`, `executive offices`, `executive search services`, `fabricated metal products`, `fabrication de véhicules à moteur`, `facilities services`, `family planning centers`, `farming`, `farming, ranching, forestry`, `fashion accessories manufacturing`, `financial services`, `fine art`, `fine arts schools`, `fire protection`, `fisheries`, `flight training`, `food & beverages`, `food and beverage manufacturing`, `food and beverage retail`, `food and beverage services`, `food production`, `footwear and leather goods repair`, `footwear manufacturing`, `forestry and logging`, `fossil fuel electric power generation`, `freight and package transportation`, `fruit and vegetable preserves manufacturing`, `fuel cell manufacturing`, `fundraising`, `funds and trusts`, `furniture`, `furniture and home furnishings manufacturing`, `gambling facilities and casinos`, `geothermal electric power generation`, `glass product manufacturing`, `glass, ceramics and concrete manufacturing`, `golf courses and country clubs`, `government administration`, `government relations`, `government relations services`, `graphic design`, `ground passenger transportation`, `health and human services`, `health, wellness & fitness`, `higher education`, `highway, street, and bridge construction`, `historical sites`, `holding companies`, `home health care services`, `horticulture`, `hospitality`, `hospitals`, `hospitals and health care`, `hotels and motels`, `household and institutional furniture manufacturing`, `household appliance manufacturing`, `household services`, `housing and community development`, `housing programs`, `human resources`, `human resources services`, `hvac and refrigeration equipment manufacturing`, `hydroelectric power generation`, `import & export`, `individual and family services`, `industrial automation`, `industrial machinery manufacturing`, `industry associations`, `information services`, `information technology & services`, `insurance`, `insurance agencies and brokerages`, `insurance and employee benefit funds`, `insurance carriers`, `interior design`, `international affairs`, `international trade and development`, `internet marketplace platforms`, `internet news`, `internet publishing`, `interurban and rural bus services`, `investment advice`, `investment banking`, `investment management`, `it services and it consulting`, `it system custom software development`, `it system data services`, `it system design services`, `it system installation and disposal`, `it system operations and maintenance`, `it system testing and evaluation`, `it system training and support`, `janitorial services`, `landscaping services`, `language schools`, `laundry and drycleaning services`, `law enforcement`, `law practice`, `leasing non-residential real estate`, `leasing residential real estate`, `leather product manufacturing`, `legal services`, `legislative offices`, `leisure, travel & tourism`, `libraries`, `lime and gypsum products manufacturing`, `loan brokers`, `luxury goods & jewelry`, `machinery manufacturing`, `magnetic and optical media manufacturing`, `manufacturing`, `maritime`, `maritime transportation`, `market research`, `marketing services`, `mattress and blinds manufacturing`, `measuring and control instrument manufacturing`, `meat products manufacturing`, `mechanical or industrial engineering`, `media and telecommunications`, `media production`, `medical and diagnostic laboratories`, `medical device`, `medical equipment manufacturing`, `medical practices`, `mental health care`, `metal ore mining`, `metal treatments`, `metal valve, ball, and roller manufacturing`, `metalworking machinery manufacturing`, `military and international affairs`, `mining`, `mobile computing software products`, `mobile food services`, `mobile gaming apps`, `motor vehicle manufacturing`, `motor vehicle parts manufacturing`, `movies and sound recording`, `movies, videos, and sound`, `museums`, `museums, historical sites, and zoos`, `music`, `musicians`, `nanotechnology research`, `natural gas distribution`, `natural gas extraction`, `newspaper publishing`, `non-profit organization management`, `non-profit organizations`, `nonmetallic mineral mining`, `nonresidential building construction`, `nuclear electric power generation`, `nursing homes and residential care facilities`, `office administration`, `office furniture and fixtures manufacturing`, `oil and coal product manufacturing`, `oil and gas`, `oil extraction`, `oil, gas, and mining`, `online and mail order retail`, `online audio and video media`, `online media`, `operations consulting`, `optometrists`, `outpatient care centers`, `outsourcing and offshoring consulting`, `outsourcing/offshoring`, `packaging & containers`, `packaging and containers manufacturing`, `paint, coating, and adhesive manufacturing`, `paper & forest products`, `paper and forest product manufacturing`, `pension funds`, `performing arts`, `performing arts and spectator sports`, `periodical publishing`, `personal and laundry services`, `personal care product manufacturing`, `personal care services`, `pet services`, `pharmaceutical manufacturing`, `philanthropic fundraising services`, `philanthropy`, `photography`, `physical, occupational and speech therapists`, `physicians`, `pipeline transportation`, `plastics and rubber product manufacturing`, `plastics manufacturing`, `political organizations`, `postal services`, `primary and secondary education`, `primary metal manufacturing`, `printing services`, `professional organizations`, `professional services`, `professional training and coaching`, `program development`, `public assistance programs`, `public health`, `public policy`, `public policy offices`, `public relations and communications services`, `public safety`, `racetracks`, `radio and television broadcasting`, `rail transportation`, `railroad equipment manufacturing`, `ranching`, `ranching and fisheries`, `real estate`, `real estate agents and brokers`, `real estate and equipment rental services`, `recreational facilities`, `regenerative design`, `religious institutions`, `renewable energy equipment manufacturing`, `renewable energy power generation`, `renewable energy semiconductor manufacturing`, `renewables & environment`, `repair and maintenance`, `research`, `research services`, `residential building construction`, `restaurants`, `retail`, `retail apparel and fashion`, `retail appliances, electrical, and electronic equipment`, `retail art dealers`, `retail art supplies`, `retail books and printed news`, `retail building materials and garden equipment`, `retail florists`, `retail furniture and home furnishings`, `retail gasoline`, `retail groceries`, `retail health and personal care products`, `retail luxury goods and jewelry`, `retail motor vehicles`, `retail musical instruments`, `retail office equipment`, `retail office supplies and gifts`, `retail pharmacies`, `retail recyclable materials & used merchandise`, `reupholstery and furniture repair`, `robot manufacturing`, `robotics engineering`, `rubber products manufacturing`, `satellite telecommunications`, `savings institutions`, `school and employee bus services`, `seafood product manufacturing`, `secretarial schools`, `securities and commodity exchanges`, `security and investigations`, `security guards and patrol services`, `security systems services`, `seguros`, `semiconductor manufacturing`, `semiconductors`, `services for renewable energy`, `services for the elderly and disabled`, `sheet music publishing`, `shipbuilding`, `shuttles and special needs transportation services`, `sightseeing transportation`, `skiing facilities`, `smart meter manufacturing`, `soap and cleaning product manufacturing`, `social networking platforms`, `software development`, `solar electric power generation`, `sound recording`, `space research and technology`, `specialty trade contractors`, `spectator sports`, `sporting goods`, `sporting goods manufacturing`, `sports and recreation instruction`, `sports teams and clubs`, `spring and wire product manufacturing`, `staffing and recruiting`, `steam and air-conditioning supply`, `strategic management services`, `subdivision of land`, `sugar and confectionery product manufacturing`, `surveying and mapping services`, `taxi and limousine services`, `technical and vocational training`, `technology, information and internet`, `technology, information and media`, `telecommunications`, `telecommunications carriers`, `telephone call centers`, `temporary help services`, `textile manufacturing`, `theater companies`, `think tanks`, `tobacco`, `tobacco manufacturing`, `translation and localization`, `transportation equipment manufacturing`, `transportation programs`, `transportation, logistics, supply chain and storage`, `transportation/trucking/railroad`, `travel arrangements`, `truck transportation`, `trusts and estates`, `turned products and fastener manufacturing`, `urban transit services`, `utilities`, `utilities administration`, `utility system construction`, `vehicle repair and maintenance`, `venture capital and private equity principals`, `veterinary`, `veterinary services`, `vocational rehabilitation services`, `warehousing`, `warehousing and storage`, `waste collection`, `waste treatment and disposal`, `water supply and irrigation systems`, `water, waste, steam, and air conditioning services`, `wellness and fitness services`, `wholesale`, `wholesale alcoholic beverages`, `wholesale apparel and sewing supplies`, `wholesale appliances, electrical, and electronics`, `wholesale building materials`, `wholesale chemical and allied products`, `wholesale computer equipment`, `wholesale drugs and sundries`, `wholesale food and beverage`, `wholesale footwear`, `wholesale furniture and home furnishings`, `wholesale hardware, plumbing, heating equipment`, `wholesale import and export`, `wholesale luxury goods and jewelry`, `wholesale machinery`, `wholesale metals and minerals`, `wholesale motor vehicles and parts`, `wholesale paper products`, `wholesale petroleum and petroleum products`, `wholesale photography equipment and supplies`, `wholesale raw farm products`, `wholesale recyclable materials`, `wind electric power generation`, `wine & spirits`, `wineries`, `wireless services`, `wood product manufacturing`, `writing and editing`, `zoos and botanical gardens`

**Employee size** (optional, repeatable)
Must be one of these exact strings: `1-10`, `11-50`, `51-200`, `201-500`, `501-1K`, `1K-5K`, `5K-10K`, `10K+`

If the user says "over 100 employees", explain that the closest options are `51-200` (includes 51–100) or starting from `201-500`. Confirm which ranges they want.

**Country** (optional, repeatable)
ISO 2-letter codes: e.g. `NL`, `US`, `GB`, `DE`, `FR`

**Technology** (optional, repeatable)
Technology slugs: e.g. `stripe`, `hubspot`, `salesforce`, `intercom`

Once filters are collected, run a count preview:

```bash
saber list company count-preview \
  --industry "<industry>" \
  [--size "<range>" --size "<range>"] \
  [--country "<code>"] \
  [--technology "<slug>"] \
  --json
```

Show the user: **"X companies matched. Each signal costs 2 credits — running N signals against X companies will cost N×X×2 credits total."**

For example, 3 signals against 2,466 companies = 7,398 signal runs × 2 credits = **14,796 credits**.

Ask: *"Happy with this count, or would you like to adjust the filters?"*

Iterate until the user confirms. Do not recommend narrowing filters based on list size — that's the user's call. Just be transparent about the credit implications.

---

## Stage 2: Create the List

Ask the user for a list name, or suggest one based on the filters (e.g. `"NL SaaS 200+"`).

```bash
saber list company create \
  --name "<name>" \
  --industry "<industry>" \
  [--size "<range>" --size "<range>"] \
  [--country "<code>"] \
  [--technology "<slug>"] \
  --json
```

Save the returned **list ID** — you'll need it for every subsequent step.

Confirm to the user: *"List '[name]' created (ID: `<listId>`)."*

---

## Stage 3: Signal Definition

Ask the user:

> *"What signals would you like to run against these companies? Just list the questions — I'll handle the answer types."*

**Infer the answer type** from the question. Do not ask the user to specify it. Use this guide:

| Answer type | When to use | Example question |
|---|---|---|
| `boolean` | Pure yes/no with no follow-up detail needed | "Is this company B2B?", "Do they have a mobile app?" |
| `number` | Numeric values | "How many employees?", "What is their Trustpilot rating?" |
| `open_text` | Free-form descriptions or summaries (single field, no structure) | "Describe what they sell", "What is their main product?" |
| `list` | Multiple distinct items of the same type (no boolean flag needed) | "What are their main competitors?", "What markets do they serve?" |
| `url` | A single URL | "What is their LinkedIn URL?", "What is their careers page?" |
| `percentage` | A percentage value | "What percentage of revenue is from SaaS?" |
| `currency` | A monetary amount | "What is their estimated ARR?", "What was their last funding amount?" |
| `json_schema` | **Compound questions** (yes/no + follow-up detail), or any question requiring multiple structured fields | "Have they raised funding? If so, what stage and amount?", "Are they hiring? If so, which roles?", "Have they launched new features? If so, which?" |

**When to prefer `json_schema`:** Any question that contains "if so", "if yes", or implicitly combines a boolean with follow-up detail fields should use `json_schema`. Design the schema to include a boolean flag (e.g. `raised`, `expanding`, `hiring`) plus the relevant detail fields (strings, arrays, etc.). This produces cleaner, more queryable output than `open_text`.

Examples of `json_schema` schemas for common compound questions:
- Funding: `{ raised: boolean, stage: string, amount: string, date: string }`
- Market expansion: `{ expanding: boolean, markets: [string] }`
- New features: `{ introduced: boolean, features: [string] }`
- Events: `{ attending: boolean, events: [string] }`
- Hiring: `{ hiring: boolean, roles: [string] }`

For `json_schema`: the CLI does not support passing an output schema. Use the Saber API directly instead:
```bash
# Test signal (sync)
curl -s -X POST 'https://api.saber.app/v1/companies/signals/sync' \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer $SABER_API_KEY" \
  -d '{
    "domain": "<domain>",
    "question": "<question>",
    "answerType": "json_schema",
    "outputSchema": {
      "type": "object",
      "properties": {
        "field1": { "type": "string" },
        "field2": { "type": "array", "items": { "type": "string" } }
      }
    }
  }'

# Template creation (for subscriptions)
curl -s -X POST 'https://api.saber.app/v1/companies/signals/templates' \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer $SABER_API_KEY" \
  -d '{
    "name": "<template name>",
    "question": "<question>",
    "answerType": "json_schema",
    "outputSchema": { ... }
  }'
```
The `json_schema` path requires `SABER_API_KEY` to be set in the environment for `curl`. Skip it (use `open_text` instead) if the user can't supply a key — every other answer type works through the CLI without a key.

Present the inferred signal list back to the user — showing question + answer type — and ask:

*"Before committing to the full run, would you like to test these signals against a sample of up to 5 companies? This lets you review answer quality and refine any questions before spending credits on the full list. (optional)"*

**If the user wants to test:**

1. Fetch the company list and take the first 5 domains:
```bash
saber list company companies <listId> --json
```
Extract up to 5 `domain` values.

2. Launch **all N×M signals simultaneously** as background Bash tasks — do not run them sequentially. Fire every domain × signal combination at once:
```bash
saber signal --domain <domain1> --question "<q1>" --answer-type <type> --yes --json  # background
saber signal --domain <domain1> --question "<q2>" --answer-type <type> --yes --json  # background
saber signal --domain <domain2> --question "<q1>" --answer-type <type> --yes --json  # background
# ... all N×M in parallel
```
Tell the user: *"Running [N×M] test signals in parallel — expect results in ~60–90 seconds."*

Collect results as background tasks complete. Display a running table as answers arrive, then show the full table once all are done.

3. Ask the user: *"How do the results look? Would you like to adjust any questions before running the full list?"*

4. If they want to refine: update the signal questions, re-infer answer types, and re-run using the same parallel launch pattern. Revised question wording automatically bypasses the cache (cache key = domain + question), so no extra flags needed. Only use `--force-refresh` if re-running the exact same question text to get a fresher result. Repeat until the user is satisfied.

5. Once happy: *"Ready to run [N] signals against [M] companies ([N×M] total = [N×M×2] credits)? This will start the full run."*

**If the user skips testing:**

Ask directly: *"Ready to run [N] signals against [M] companies ([N×M] total = [N×M×2] credits)? Confirm to proceed."*

Wait for explicit confirmation before proceeding.

Once confirmed, for each signal:

**1. Create a template:**
```bash
saber template create \
  --name "<signal name>" \
  --question "<question text>" \
  --answer-type <type> \
  --json
```
Save each **template ID**.

**2. Create a subscription (one per template) — one-off only:**
```bash
saber subscription create \
  --list <listId> \
  --template <templateId> \
  --frequency monthly \
  --run-once \
  --yes \
  --json
```
Save each **subscription ID**.

`--frequency monthly --run-once` is required by the API (a frequency must be set), but `--run-once` instructs the backend to leave the subscription in `status=stopped` and **never create a Temporal schedule entry**. With no schedule entry, the cron field in the record is dormant data — it will not fire. **Do not call `saber subscription start` on these subscriptions.** Doing so creates the Temporal schedule from the cron field and converts a one-off into a recurring monthly job. (This is exactly the failure mode that has burned thousands of credits in past incidents — every "one-off" sub had been started and then fired again on the cron's next monthly tick.)

**3. Trigger each subscription — via the pre-trigger guard:**

Trigger through the guard without ever calling `start`. The guard MUST be used for every trigger call:

```bash
SUB_ID="<subscriptionId>"

# Pre-trigger guard — refuses if subscription has already run
LAST_RUN=$(saber subscription get "$SUB_ID" --json | jq -r '.lastRunAt // empty')
if [ -n "$LAST_RUN" ]; then
  echo "ABORT: $SUB_ID already ran at $LAST_RUN — re-trigger would re-charge every company in the list"
  exit 1
fi

SIGNAL_COUNT=$(saber signal list --subscription-id "$SUB_ID" --limit 1 --json | jq '.total')
if [ "$SIGNAL_COUNT" -gt 0 ]; then
  echo "ABORT: $SUB_ID already has $SIGNAL_COUNT signals spawned — re-trigger would duplicate"
  exit 1
fi

# One-off safeguard — refuse if the subscription is somehow active
STATUS=$(saber subscription get "$SUB_ID" --json | jq -r '.status')
if [ "$STATUS" != "stopped" ]; then
  echo "ABORT: $SUB_ID has status=$STATUS (expected stopped). A Temporal schedule is armed; this is no longer a one-off."
  echo "Investigate before triggering — run 'saber subscription stop $SUB_ID' to disarm, then re-evaluate."
  exit 1
fi

saber subscription trigger "$SUB_ID" --yes --verbose
```

Use `--verbose` on trigger to confirm HTTP 200 — that is the only confirmation needed. Do NOT re-trigger if the displayed subscription status still shows "stopped" after triggering; that's the *expected* end state for a one-off, and the status display also lags briefly behind. If you need to verify the trigger landed, call `saber subscription get "$SUB_ID"` and check `lastRunAt` — don't re-trigger.

**4. Post-trigger safeguard — verify the subscription stayed one-off:**

After triggering each subscription, confirm it is still `status=stopped`. A one-off should never end up active:

```bash
sleep 2
POST_STATUS=$(saber subscription get "$SUB_ID" --json | jq -r '.status')
if [ "$POST_STATUS" != "stopped" ]; then
  echo "WARNING: $SUB_ID is now status=$POST_STATUS after trigger. Disarming immediately to prevent monthly re-fire."
  saber subscription stop "$SUB_ID"
fi
```

If any subscription ever shows `status=active`, stop it immediately — that is the failure mode that drains credits on the cron's next tick.

**Important:** Subscription triggers always do a full fresh run — they do NOT use the signal cache (domain + question). The guard above is the only sanctioned way to call `trigger`. Bypassing it to recover stalled signals or retry a failed-looking trigger will re-run and re-charge credits for every company in the list. There is no DELETE endpoint for company signal subscriptions in the public API, so the only available defenses are `--run-once` at create time and `stop` if a subscription ever drifts to active.

Confirm: *"[N] signals queued across [M] companies ([N×M] signal runs started)."*

---

## Stage 4: Time Estimate & Polling

**Estimate completion time** using this benchmark:
- Saber processes signals in parallel server-side — the bottleneck is backend concurrency, not list size
- Each individual signal takes ~30–60 seconds; the backend runs many simultaneously
- Use this table as your guide:

| List size | Estimated time |
|---|---|
| < 100 companies | 5–15 minutes |
| 100–500 companies | 10–30 minutes |
| 500–2,000 companies | 15–60 minutes |
| 2,000+ companies | 45–90 minutes |

- Adding more signals per company has minimal impact on total time (they run in parallel per company too)
- Do NOT use `N_companies × seconds` — that assumes sequential execution and will overestimate by 10–100×

Tell the user the estimate and that you'll check status on request.

**To check status**, run this progress snapshot. All commands are read-only — they never cause re-runs or charge credits:

```bash
SUB_ID="<subscriptionId>"
LIST_ID="<listId>"   # from subscription or saved from Stage 2

# Expected total = number of companies in the list (one signal per company per subscription)
LIST_SIZE=$(saber list company companies "$LIST_ID" --limit 1 --json | jq '.total')

# Live counts by status — use --limit 1, only .total matters
PROC=$(saber signal list --subscription-id "$SUB_ID" --status processing --limit 1 --json | jq '.total')
DONE=$(saber signal list --subscription-id "$SUB_ID" --status completed   --limit 1 --json | jq '.total')
FAIL=$(saber signal list --subscription-id "$SUB_ID" --status failed      --limit 1 --json | jq '.total')

SPAWNED=$((PROC + DONE + FAIL))
REMAINING=$((LIST_SIZE - DONE - FAIL))
PCT=$((LIST_SIZE > 0 ? 100 * DONE / LIST_SIZE : 0))

echo "Progress: $DONE / $LIST_SIZE complete (${PCT}%)"
echo "  in-flight (processing): $PROC"
echo "  completed:              $DONE"
echo "  failed:                 $FAIL"
echo "  spawned so far:         $SPAWNED / $LIST_SIZE"
echo "  remaining to finish:    $REMAINING"
```

**Key fields to always report to the user:**
1. **Completed** — finished signals
2. **Processing** — in flight right now
3. **Failed** — gave up
4. **Remaining** — `LIST_SIZE - (completed + failed)` — the ones that still need to resolve
5. **Percent complete** — `completed / list_size`

Early in a run, `SPAWNED < LIST_SIZE` because Saber is still queuing signals server-side. Later, `SPAWNED == LIST_SIZE` and the only movement is `processing → completed/failed`.

**Pagination note:** Use `--limit 1` for status counts — only the `.total` field matters, not the individual rows. To inspect which specific companies are stalled, paginate the `processing` list:

```bash
saber signal list --subscription-id "$SUB_ID" --status processing --json       # first 25
saber signal list --subscription-id "$SUB_ID" --status processing --offset 25 --json
```

To inspect a specific stalled signal:

```bash
saber signal get <signalId> --json
```

After each snapshot, report progress to the user and ask: *"Still waiting on [N] signals. Check again?"*

When `remaining == 0`: *"All signals finished. Ready for contact search."*

If any show `"status": "failed"`, report which company + question failed.

**If signals are genuinely stalled (processing for >5 minutes):** Do NOT re-trigger the subscription. The pre-trigger guard will refuse it anyway, and bypassing the guard to "recover" stalled signals would re-charge credits for every company already answered. The only acceptable options are:

1. **Wait** — Saber's backend will eventually time out and mark the signal `failed`. Most stalls clear within 10–15 minutes.
2. **Inspect** individual stalled signals with `saber signal get <signalId>` to understand what's happening.
3. **Export partial** — proceed to Stage 5/6 with what's completed and flag the gaps to the user.

Re-running a specific stalled signal outside the subscription (for one domain + question) can be done with `saber signal --domain <d> --question "<q>" --answer-type <t> --yes` — this uses the domain+question cache and costs 2 credits per signal, not a full list re-run.

---

## Stage 5: Contact Search

**Step 1: Extract company LinkedIn URLs**

```bash
saber list company companies <listId> --json
```

From the JSON response, each company has:
- `domain`: e.g. `booking.com`
- `handle`: e.g. `company/booking.com` → construct URL: `https://www.linkedin.com/company/booking.com`
- `enrichedData.liId`: e.g. `11348` → used later to match contacts back to companies

Build two mappings:
- `handle → domain` (for passing to contact search)
- `liId → domain` (for matching contact results back)

**Step 2: Ask for contact filters**

Ask the user:
- *"What job titles should I look for? (e.g. CEO, VP Sales, Head of Marketing)"*
- *"Any country filter for contacts? (optional)"*
- *"Any keyword filter? (optional)"*

**Step 3: Run contact search**

The contact search API accepts a maximum of **10 company LinkedIn URLs per call**. Batch the companies into groups of 10 and run sequentially, collecting all results:

```python
batches = [company_urls[i:i+10] for i in range(0, len(company_urls), 10)]
for batch in batches:
    # saber contact search --company-linkedin <url> ... --title CTO --json
```

Single-batch example:
```bash
saber contact search \
  --company-linkedin "https://www.linkedin.com/<handle1>" \
  --company-linkedin "https://www.linkedin.com/<handle2>" \
  [--title "<title>" --title "<title>"] \
  [--country "<code>"] \
  [--keyword "<keyword>"] \
  --json
```

**Step 4: Group contacts by company (max 2 per company)**

Parse the JSON response `contacts[]`. Each contact has a `positions[]` array. The first position's `companyUrl` is formatted as `https://www.linkedin.com/company/<liId>`. Extract the numeric ID and match it to the `liId → domain` mapping.

For each domain, keep the first 2 contacts returned. For each contact, record:
- `fullName`
- `positions[0].title` (current job title)

---

## Stage 6: Combined Export

**Step 1: Export companies + signals to CSV**

```bash
saber list company export <listId> \
  --signal-template-id <templateId1> \
  --signal-template-id <templateId2> \
  [--signal-template-id <templateId3>] \
  --output /tmp/prospecting-workflow-signals.csv
```

**Step 2: Merge contact columns using Python**

Run this via the Bash tool, substituting the `contact_map` dict with real data from the contact search results:

```python
import csv, re

# contact_map built from contact search: { "domain.com": [{"name": ..., "title": ...}, ...] }
contact_map = { ... }

with open('/tmp/prospecting-workflow-signals.csv', 'r') as f:
    rows = list(csv.DictReader(f))

if not rows:
    print("No data to export")
    exit()

fieldnames = list(rows[0].keys()) + [
    'contact1_name', 'contact1_title',
    'contact2_name', 'contact2_title'
]

import os
desktop = os.path.expanduser('~/Desktop/prospecting-workflow-export.csv')

with open(desktop, 'w', newline='') as f:
    writer = csv.DictWriter(f, fieldnames=fieldnames)
    writer.writeheader()
    for row in rows:
        # Extract domain from website column
        website = row.get('website', '')
        domain = re.sub(r'^https?://', '', website).split('/')[0]
        contacts = contact_map.get(domain, [])
        row['contact1_name']  = contacts[0]['name']  if len(contacts) > 0 else ''
        row['contact1_title'] = contacts[0]['title'] if len(contacts) > 0 else ''
        row['contact2_name']  = contacts[1]['name']  if len(contacts) > 1 else ''
        row['contact2_title'] = contacts[1]['title'] if len(contacts) > 1 else ''
        writer.writerow(row)

print(f"Saved to {desktop}")
```

**Step 3: Confirm to the user**

*"Export saved to `~/Desktop/prospecting-workflow-export.csv` — [N] companies, [M] signal columns, contact columns included."*

---

## Notes

- **Never display or log API keys.** The CLI authenticates automatically via stored credentials.
- **Industry strings** must exactly match the Saber Industries list (lowercase, case-sensitive). When uncertain, tell the user and ask them to confirm the exact string.
- **Always include `--yes`** on `saber signal`, `subscription create`, and `subscription trigger` to skip the interactive credit prompt.
- **Export after all signals complete** — partial exports leave blank cells for in-progress signals.
- **Contact matching** uses the numeric LinkedIn ID (`liId`) from the company list response matched against the `companyUrl` in each contact's positions. If a contact can't be matched to a company, skip it.
- **Size filter format** is strict: use `501-1K` not `501-1000`, `1K-5K` not `1000-5000`.
