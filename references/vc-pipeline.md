# VC Fundraising Pipeline Reference

This condenses the VC fundraising flow for use with Ask VC sheets.

This file is the Ask VC operating version and should be enough to execute the workflow without opening any external project-specific reference.

## Pipeline

1. Clarify thesis.
2. Fill Page 1 in the workbook.
3. Stop and ask the user to approve Page 1.
4. After approval, process the built-in local investor source cache and any user-provided lists.
5. Deduplicate and score the `Master Fund Universe`.
6. Create the initial `Pipeline investors` shortlist from ready/high-priority funds.
7. Stop and ask whether the user has additional funds/contacts/lists to add or wants to continue with the current universe.
8. Ask whether to run optional paid extra fund discovery.
9. Map relevant people at each firm.
10. Ask before paid LinkedIn/email enrichment.
11. Enrich contacts with email and LinkedIn.
12. Select best-fit outreach template candidates and ask the user to approve them.
13. After message approval, update `Outreach Tracker`, `Шаблон Pipeline инвесторов` / `Pipeline investors`, `Final Output`, CSV/JSON exports, and formula-driven `Metrics`.

## Company Profile Fields

Collect or infer:

- company name
- one-line thesis
- sector/subsector
- stage
- HQ and founder/customer geography
- business model
- target round and first-check size
- lead/co-lead/follower preference
- traction summary
- comparable companies
- must-have / must-avoid investor traits

Ask only missing fields that materially affect targeting.

Recommended local output:

`data/normalized/company_profile.json`

Include assumptions and open questions when fields are inferred.

Review checkpoint before sourcing:

- company
- thesis
- stage
- sector
- geography
- check size
- lead preference
- traction
- comparables

## Candidate Fund Evaluation

Score conservatively. Do not reward prestige alone.

| Dimension | Max |
|---|---:|
| Sector fit | 25 |
| Stage fit | 20 |
| Geography fit | 10 |
| Check size fit | 10 |
| Lead behavior fit | 10 |
| Portfolio adjacency | 10 |
| Recency | 10 |
| Partner relevance | 5 |

Interpretation:

- `90-100`: excellent fit
- `75-89`: strong fit
- `60-74`: possible fit
- `<60`: low priority / verify fit

## Sourcing: Existing Lists First

First use already assembled local sources:

- built-in local cache at `data/investor_sources/source_extraction_manifest.json`
- every cached CSV listed in that manifest
- user-provided CSV lists
- user-provided Google Sheets / Airtables / fund names

Then deduplicate into the `master fund universe`.

Use or create the workbook tab `Источники инвесторов` only when the user provides additional lists. Do not depend on that tab for the built-in source universe.

## Optional Paid Extra Discovery

Only after existing/user lists are processed, ask whether the user wants additional paid investor discovery.

If yes, use multiple search angles:

- OpenVC / Signal / Crunchbase / VC lists / Airtable lists
- portfolio adjacency: investors in comparable or adjacent companies
- stage + geography + sector search
- broader web research

Run at least 5 search angles:

| Source angle | Query pattern |
|---|---|
| OpenVC | `site:openvc.app [sector] [stage] investors` |
| Crunchbase | `site:crunchbase.com/organization [sector] VC fund [stage]` |
| Signal by NFX | `site:signal.nfx.com [sector] [stage]` |
| Notion/Airtable lists | `"vc list" [sector] [stage] site:notion.so OR site:airtable.com` |
| General web | `[sector] seed fund OR venture capital investors [geography]` |
| Portfolio adjacency | `"[comparable_company]" investors OR backed by OR portfolio` |

For each fund, capture:

- `fund_id` slug
- fund name
- website
- location/geography
- stage focus
- sector focus
- check size range if available
- lead/co-lead/follower behavior
- recent investments if available
- rationale
- concerns
- evidence confidence

`fund_id` is mandatory. Use the same value in `Master Fund Universe`, `Pipeline investors`, `Outreach Tracker`, and `Final Output`.

Recommended local output:

`data/raw/fund_candidates.json`

Before running paid discovery, tell the user:

- which API/tool will be used, e.g. Exa
- where to provide the key, e.g. environment variable or connector/tool configuration
- rough query count
- rough cost estimate when known

Do not imply paid discovery is required. It is optional expansion beyond existing lists.

Process the built-in cached sources sequentially. Do not stop at a small top-N list; build the largest practical `master fund universe`, deduplicate it, and label quality.

When a source cannot be accessed or scraped, log it as `unavailable` with the reason and continue.

After built-in sources are processed, ask: `Do you have any additional investor lists you want to include?`

For broad CSV/source-list work, produce:

- a full `master fund universe` at fund level
- a `contact-level outreach list` at person level
- a send-ready list
- a needs-review list

## Contact Mapping

Find 1-3 people per firm:

1. Partner / General Partner / Managing Partner
2. Principal
3. Investor / Investment Team
4. Associate

Prefer people with sector, stage, or geography relevance. Avoid platform/talent roles unless clearly relevant.

Before contact mapping/enrichment at scale, explain the next cost boundary:

- free/manual option: use firm team pages and visible LinkedIn URLs where available
- paid option: people search / LinkedIn profile search / email finder at scale
- email finder cost when known, e.g. Icypeas/Apify about `$0.028` per contact result from prior usage

Ask approval before paid contact enrichment.

Search patterns:

- `"[fund_name] venture capital" partners principals investors`
- `"[fund_name]" "[sector]" partner OR principal OR investor`
- `"[person]" "[fund_name]" venture capital LinkedIn`
- `site:[fund_domain] team partner [fund_name]`

Extract:

- full name
- title
- firm
- `contact_id` stable slug, usually `fund_id-first-last`
- `fund_id`
- location if available
- LinkedIn URL
- firm profile URL
- why relevant

Recommended local output:

`data/raw/investor_list.json`

Review checkpoint: show mapped people by fund and ask for confirmation before contact enrichment when the user is actively reviewing. If the user requested full automation, proceed and label assumptions.

## Contact Enrichment

Allowed:

- public firm emails
- professional emails returned by a trusted enrichment actor/tool
- public LinkedIn URLs

Mark quality:

- `confirmed`: high-confidence email or public source
- `inferred`: lower-confidence email
- `not_found`: no email; use LinkedIn/manual route

Do not present guessed or low-confidence emails as confirmed.

Also capture:

- `source_type`: `public`, `probabilistic`, or `not_found`
- `email_confidence`: numeric confidence when available
- `reason_to_contact`: internal rationale for why this person/fund fits
- `review_note`: missing email, missing person LinkedIn, inferred contact, or any manual check needed

Never place a firm/company LinkedIn URL in a person LinkedIn field. Keep company URLs in firm profile fields only.

Primary enrichment pattern:

1. Build `firstName,lastName,domain` rows from investor names and fund domains.
2. Run a professional email finder such as Apify `icypeas_official/bulk-email-finder` when available.
3. Map results back by first + last + domain.
4. Fill missing LinkedIn via people/web search.

For Apify `icypeas_official/bulk-email-finder`, the actor input must be a list of strings, not objects:

```json
{
  "people": [
    "FirstName,LastName,domain.com"
  ]
}
```

Do not send `{"people":[{"firstName":"..."}]}` to this actor; it can run but return empty/invalid results.

Tier mapping:

| Result | Tier |
|---|---|
| found/high confidence | `confirmed` |
| found/lower confidence | `inferred` |
| not found/risky | `not_found` |

Recommended local output:

`data/normalized/investor_contacts.json`

## Outreach Message Format

Investor-facing language is English unless the user asks otherwise.

Before drafting, read `references/message-templates.md`. Treat it as the persistent template library. Do not discard templates just because one was selected for a prior campaign.

Template approval flow:

1. Choose best-fit candidate templates from the library based on company profile, traction, and target investor segment.
2. Explain why each candidate was chosen.
3. Save all viable selected candidates to `message_template_candidates.json`.
4. Show the selected candidates to the user before filling final tracker rows.
5. Ask the user to confirm or edit 1 universal template and 2 niche templates.
6. Save the selected set to `approved_message_templates.json`.
7. Keep non-selected library variants as backup styles for regeneration.

If writing templates into `ДЗ№2 - Письма инвестору` before approval, either:

- write the full template library; or
- write only the selected best-fit candidates and mark every row `Candidate - pending approval`, with a note explaining the selection reason.

Email #1:

```text
Hi FirstName,

I'm reaching out because [Fund] looks relevant for [Startup] given [specific but natural reason].

[Company one-liner / product sentence.]

[Traction sentence.]

Let's have a call next week?

Best,
[FounderName]
```

For uncertain-fit funds:

```text
Hi FirstName,

I'm reaching out because [Fund] came up in our search for software investors, and I wanted to check if [Startup] is within your current scope.

...

If this is within scope, let's have a call next week?
```

Do not use internal database phrasing in emails:

- Bad: `We screened Frontiers Capital because the source list shows...`
- Good: `Frontiers Capital came up in our search for software investors...`

LinkedIn:

- Max 300 characters when possible.
- Use `Hi FirstName - ...`
- Include one-liner, traction, and simple ask.

Required message set:

- email #1
- email follow-up #1, usually 5-7 days later
- email follow-up #2, usually 10-14 days later
- LinkedIn connection request
- LinkedIn first message after connection
- LinkedIn follow-up #1
- LinkedIn follow-up #2

Before filling the final outreach table, show candidate templates plus 2-3 personalized examples and ask the user to approve or edit the tone.

Recommended local output:

- `message_template_candidates.json`
- `approved_message_templates.json`
- `data/normalized/final_output.json`
- `data/normalized/output.csv`
- `sendable_with_email.csv`
- `needs_review.csv`

Recommended CSV columns:

`priority_rank, fund_id, fund_name, score_total, score_max_possible, investor_name, investor_title, contact_tier, source_type, work_email, email_confidence, linkedin, outreach_subject_line, outreach_email_body, outreach_linkedin_dm, outreach_personalization_hook, outreach_cta, review_note`

Recommended extra CSV/JSON fields:

`sector_fit, stage_fit, geography_fit, check_size_fit, lead_behavior_fit, portfolio_adjacency, recency, partner_relevance, why_match, risks, status, contact_id, firm_profile_url`

## Pipeline Updates

When creating Outreach Tracker rows, use the contact-level outreach list: one row per person/contact, including all message columns from `ask-vc-sheet.md`.

When creating `Master Fund Universe` rows, use the broad deduplicated fund universe: one row per fund with score, status, source provenance, best contact, rationale, concerns, and internal notes.

When creating `Шаблон Pipeline инвесторов` / `Pipeline investors` rows, use only active or high-priority funds from the master fund universe: one row per fund.

Preserve the Ask VC template's first columns in this order:

`№, Investor / Fund, Website, Crunchbase, Contact, LinkedIn Profile, Stage Fit, [blank separator], Geography, Intro Source, Status, Last Contact, Next Action, Follow-up Date, Notes`

Map generated data into those base columns first:

- `Investor / Fund`: fund name
- `Website`: firm site
- `Contact`: best contact(s), comma-separated
- `LinkedIn Profile`: primary profile URL
- `Stage Fit`: pre-seed / seed / etc.
- `Geography`: US / Europe / Global
- `Intro Source`: Cold email / LinkedIn / Warm intro
- `Status`: Research or Targeted
- `Next Action`: Send deck / Send first outreach / Find intro / Verify fit
- `Notes`: score, contact tier, concern, personalization hook

Append all additional generated fields to the right of the base template columns. Do not replace the template table with a technical-only schema.

After creating or changing Outreach Tracker, sync fund-level Pipeline fields:

- `Fund ID`
- `Primary Contact ID`
- `Primary Contact`
- `Primary Contact Title`
- `Primary Contact LinkedIn`
- `Primary Contact Email`
- `Outreach Rows`
- `Outreach Status`

After tracker sync, create/update `Final Output` from Outreach Tracker and export:

- `final_output.json`
- `output.csv`
- `sendable_with_email.csv`
- `needs_review.csv`

`sendable_with_email.csv` includes contacts with work email. `needs_review.csv` includes missing email, missing person LinkedIn, inferred contacts, or any row with a non-empty review note.

When updating the workbook, ensure `Metrics` remains formula-driven. Do not type static counts into `Metrics`; write/repair formulas that count rows and statuses from `Master Fund Universe`, `Pipeline investors`, and `Outreach Tracker`.

When logging outreach:

- append a new row to `Outreach Tracker`
- set date to today
- record `Type of Contact`
- set response to `Waiting` unless known
- set next follow-up date in pipeline if applicable

## Follow-Up Cadence

- Follow-up #1: 5-7 days after first outreach.
- Follow-up #2: 10-14 days after first outreach or 5-7 days after follow-up #1.
- Stop if investor passes, meeting booked, or user marks not relevant.

## Daily Action Mode

When asked what to do today:

1. Read `Master Fund Universe`, `Outreach Tracker`, and `Pipeline investors`.
2. Find rows where `Next Action` is due or `Follow-up Date` <= today.
3. Group actions by type: first outreach, LinkedIn connect, follow-up #1, follow-up #2, meeting prep, manual research.
4. Return a copy-paste-ready list of contacts and messages.
5. Offer to update tracker statuses after the user sends messages.

## Next 10 Mode

When asked for the next 10 contacts:

1. Sort by status, score, contact tier, and whether email/LinkedIn exists.
2. Exclude passed/contacted-too-recently contacts.
3. Return exactly 10 if available, otherwise all available.
4. Include fund, person, channel, email/LinkedIn, and the exact message to send.
