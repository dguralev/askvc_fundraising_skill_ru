# Ask VC Workbook Reference

## Find or Create the Workbook

Workbook title: `Спринт "Холодные письма инвесторам" | Ask VC`.

Reusable local template:

`assets/ask-vc-fundraising-workbook-template.xlsx`

Use this template when the user wants to manage the workflow locally or wants a file that can be uploaded to Google Drive. If creating a new Google Sheet from scratch, mirror this template's tabs, headers, example rows, validations, and `Metrics` formulas.

Preferred default: keep the live workbook in Google Drive / Google Sheets. At the beginning of a new workflow, if Drive tools are available, offer to create a Google Sheet or upload a copy of the template workbook to Google Drive. Only use a local workbook as the primary workspace if the user declines Drive, provides a local-only path, or Drive tools are unavailable.

The working file is always either:

- a Google Sheet
- an Excel workbook: `.xlsx`, `.xls`, or `.xlsm`

Do not create a CSV-only workbook. CSV files may be exported as supporting artifacts, but the live Ask VC workspace must remain a spreadsheet workbook.

Resolution order:

1. If the user provides an existing Google Sheet / Drive workbook, use it.
2. If the user provides a local path, use it, but offer to upload/create a Google Drive copy unless they explicitly want local-only work.
3. If Google Drive tools are available, search Drive for `Спринт Холодные письма инвесторам Ask VC`.
4. If duplicates exist, prefer the newest workbook that already has company-specific edits.
5. If no workbook exists and Drive tools are available, ask whether to create a Google Sheet / upload the template to Drive. This is the recommended option.
6. If the user declines Drive, or Drive tools are unavailable, create a local `.xlsx`.
7. If a template workbook exists but the user wants a separate copy, duplicate/create a new file before writing.

For Google Sheets, always get metadata before writing and use returned sheet IDs. For local workbooks, preserve formulas and existing user-entered rows.

## Canonical Tabs

Core tabs to find or create:

- `ДЗ№1 - Инвестор`
- `ДЗ№2 - Письма инвестору`
- `Master Fund Universe`
- `Шаблон Pipeline инвесторов` or existing `Pipeline investors`
- `Outreach Tracker`
- `Final Output`
- `Metrics`

Optional tab:

- `Источники инвесторов`

Create or use `Источники инвесторов` only when the user provides additional investor lists, fund names, Airtable links, Google Sheets, or CSV/XLSX sources to merge. Do not depend on this tab for the built-in investor universe; built-in sources live in the local skill cache at `data/investor_sources/`.

If the workbook was created from the Ask VC Google Sheet template, preserve its original tab and base table names. Do not strip `Шаблон` from `Шаблон Pipeline инвесторов` unless the user explicitly asks. If a workbook already uses the normalized local name `Pipeline investors`, continue using it.

If an existing workbook has duplicate legacy tabs containing the word `Шаблон`, merge them carefully instead of creating parallel trackers:

- `ДЗ№2 - Шаблоны писем инвестору` -> `ДЗ№2 - Письма инвестору`

Do not destroy the template formatting, filters, dropdown validations, or color rules while merging.

## Page 1 Row Mapping

Tab: `ДЗ№1 - Инвестор`.

Values go in column C. Column A may be hidden; it only preserves source row numbers. Do not create or rely on a Page 1 `Notes` column.

| Row | Label | Fill with |
|---:|---|---|
| 2 | Startup name | Startup name |
| 5 | Investor type | VC / Angel / Syndicate + lead/check preference |
| 8 | HQ | Legal HQ and founder location |
| 9 | Target investor markets | Target investor/customer geographies |
| 12 | Investment stage | Pre-seed / Seed / etc. |
| 13 | Revenue | Revenue / pre-revenue |
| 14 | Traction | Very short traction proof |
| 15 | Team | Founder-market fit, CEO sales/company-building proof, CTO technical/data proof, advisors |
| 18 | First check | Preferred first check + round size |
| 19 | Follow-on | Yes/no + why |
| 22 | Industries | Investor-search keywords: sectors, categories, and fund theses that should match |
| 23 | Business model | Investor-search business model filters: revenue model, customer type, GTM, margin profile |

Use compact answers, not memo paragraphs. When creating or repairing this tab, keep only these columns: `Row`, `Field`, `Value`. Hide `Row` if the user prefers a cleaner view.

Good:

- `VC / Angel / Syndicate. Lead or co-lead for $500k+ pre-seed.`
- `US company, founders in Europe.`
- `Live product, 10 paying customers, 6 active deals.`
- `Team: CEO: 5+ years in sales with deal sizes from $100k to $20M; 6 years building own businesses; successful exit. CTO: 20+ years building large-scale data systems and analytics infrastructure.`
- `Industries: B2B SaaS; AI agents; marketing automation; adTech; marTech; marketing analytics; user acquisition; performance marketing.`
- `Business model: B2B SaaS subscription; AI workflow automation for growth and marketing teams.`

## Master Fund Universe

Tab: `Master Fund Universe`.

This is the broad fund-level table produced from built-in cached sources plus any user-provided lists. It is not a top-10 shortlist.

Recommended columns:

`Fund ID, Fund, Status, Priority, Fit score, Score Max Possible, Sector Fit, Stage Fit, Geography Fit, Check Size Fit, Lead Behavior Fit, Portfolio Adjacency, Recency, Partner Relevance, Score Notes, Why Match, Risks, Rationale, Website, Domain, LinkedIn, General email, Known contact, Stage, Geography, Sectors / thesis, Check size, Source lists, Notes, Last Updated`

Recommended statuses:

- `ready`
- `review`
- `low-priority`
- `unavailable`

Rules:

- One row per fund/investor organization.
- Every row must have a stable `Fund ID` slug. Reuse it in `Pipeline investors`, `Outreach Tracker`, and `Final Output`.
- Deduplicate by normalized domain first, then normalized fund name.
- Keep uncertain fits, but mark them `review` or `low-priority`.
- Preserve source provenance from `source_extraction_manifest.json`.
- Do not overwrite manual notes unless the user asks.

## Pipeline Investors

Tab: `Шаблон Pipeline инвесторов` in the Ask VC template, or `Pipeline investors` in normalized local workbooks.

Pipeline is fund-level and operational: who we are actively pursuing and where each relationship stands.

The first columns must preserve the original Ask VC template order:

`№, Investor / Fund, Website, Crunchbase, Contact, LinkedIn Profile, Stage Fit, [blank separator], Geography, Intro Source, Status, Last Contact, Next Action, Follow-up Date, Notes`

Append generated columns to the right of those base columns:

`Fund ID, Primary Contact ID, Primary Contact Email, Priority, Fit score, Score Max Possible, Sector Fit, Stage Score, Geography Score, Check Size Fit, Lead Behavior Fit, Portfolio Adjacency, Recency, Partner Relevance, Score Notes, Why Match, Risks, Why relevant, Known contact, Firm LinkedIn, Email, Target check, Outreach Status, Outreach Rows, Owner, Final Output Status`

Recommended statuses:

- `Research`
- `Targeted`
- `Contacted`
- `Follow-up`
- `Meeting`
- `Partner meeting`
- `Due diligence`
- `Passed`

Create Pipeline rows from `Master Fund Universe`, usually only for funds with `ready` or strong `review` status.

Rules:

- Pipeline is fund-level. Do not create one Pipeline row per person.
- Keep filters, dropdown lists, and status colors on the base table.
- Sync `Primary Contact ID`, name, title, person LinkedIn, and email from the best matching `Outreach Tracker` row.
- Do not put a firm LinkedIn URL into `Primary Contact LinkedIn`; use the firm URL only in `Website`, `LinkedIn`, or notes.
- `ready` means ready for the next workflow step, not necessarily send-ready. Use contact fields and `Outreach Tracker Review Note` to distinguish send-ready vs review.

## Outreach Tracker

Tab: `Outreach Tracker`.

Outreach Tracker is person-level. Use one row per investor/contact in the final outreach list, and append event rows when logging activity. Do not overwrite history unless the user asks.

Expected base columns:

`№, Date, Investor, Fund, Type of Contact, Response, Meeting Booked, Notes`

These eight columns must stay first and keep the template filters, dropdown lists, and colors. Add these operational columns to the right when preparing the final outreach table:

- `Contact ID`
- `Fund ID`
- `Priority`
- `Fund Name`
- `Fund Website`
- `Email`
- `LinkedIn`
- `Contact Tier`
- `Source Type`
- `Email Confidence`
- `Fit Score`
- `Status`
- `Send Channel`
- `Current Step`
- `Next Action`
- `Follow-up Date`
- `Sent Date`
- `First Touch Sent`
- `Reply Status`
- `Reply Date`
- `Meeting Booked`
- `Pass Reason`
- `Do Not Contact`
- `Owner`
- `Email Subject`
- `Email #1`
- `Email Follow-up #1`
- `Email Follow-up #2`
- `LI Connect`
- `LI After Connect`
- `LI Follow-up #1`
- `LI Follow-up #2`
- `Personalization Hook`
- `Outreach CTA`
- `Review Note`
- `Internal Notes`

Rules:

- Outreach Tracker is person-level. Use one row per contact/person.
- `Fund ID` must match `Pipeline investors` and `Master Fund Universe`.
- `Contact ID` must be stable and unique, e.g. `fund-slug-first-last`.
- `LinkedIn` must be a person profile when possible (`linkedin.com/in/...`). If only a company LinkedIn exists, keep it in `Firm website / LinkedIn` or notes and mark `Review Note`.
- `Contact Tier`: `confirmed`, `inferred`, or `not_found`.
- `Source Type`: `public`, `probabilistic`, or `not_found`.
- `Email Confidence`: numeric confidence or blank when no email exists.
- `Review Note` should explicitly list missing email, missing person LinkedIn, inferred contacts, or any manual verification needed.

## Final Output

Tab: `Final Output`.

This is the final send/review table. It is generated from `Outreach Tracker` and should not replace Pipeline or Outreach.

Required columns:

`priority_rank, fund_id, fund_name, score_total, score_max_possible, investor_name, investor_title, contact_tier, source_type, work_email, email_confidence, linkedin, outreach_subject_line, outreach_email_body, outreach_linkedin_dm, outreach_personalization_hook, outreach_cta, review_note`

Recommended extra columns:

`sector_fit, stage_fit, geography_fit, check_size_fit, lead_behavior_fit, portfolio_adjacency, recency, partner_relevance, why_match, risks, status, contact_id, firm_profile_url`

Rules:

- Sort by priority rank / score.
- Include every viable contact, not only send-ready rows.
- Use `sendable_with_email.csv` for email-ready contacts and `needs_review.csv` for missing/uncertain contact data.
- Keep investor-facing fields in English unless the user asks otherwise.

Recommended contact types:

- `First outreach`
- `Warm intro`
- `LinkedIn connect`
- `LinkedIn message`
- `Follow-up #1`
- `Follow-up #2`
- `Meeting`
- `Deck sent`
- `Metrics sent`

## Additional Investor Sources

Tab: `Источники инвесторов`.

Create this tab only when the user provides additional sources to merge.

Recommended columns:

`№, Source Name, Source Type, URL / File Path, Status, Imported Rows, Notes, Last Imported`

Statuses:

- `pending`
- `imported`
- `failed`
- `skipped`

Built-in sources should not be pasted here by default. They are already stored in `data/investor_sources/`.

## Metrics

Tab: `Metrics`.

Metrics must be formula-driven, not manually typed zeros. When creating or repairing the workbook, write formulas that reference `Master Fund Universe`, `Pipeline investors`, and `Outreach Tracker`.

Recommended layout:

| Cell | Value to write |
|---|---|
| A1 | `Metric` |
| B1 | `Value` |
| A2 | `Total funds in universe` |
| B2 | `=COUNTA('Master Fund Universe'!D2:D10000)` |
| A3 | `Ready funds` |
| B3 | `=COUNTIF('Master Fund Universe'!C2:C10000,"ready")` |
| A4 | `Review funds` |
| B4 | `=COUNTIF('Master Fund Universe'!C2:C10000,"review")` |
| A5 | `Low-priority funds` |
| B5 | `=COUNTIF('Master Fund Universe'!C2:C10000,"low-priority")` |
| A6 | `Pipeline funds` |
| B6 | `=COUNTA('Pipeline investors'!B2:B10000)` |
| A7 | `Total outreach contacts` |
| B7 | `=COUNTA('Outreach Tracker'!C2:C10000)` |
| A8 | `Contacted` |
| B8 | `=COUNTIF('Outreach Tracker'!E2:E10000,"First outreach")+COUNTIF('Outreach Tracker'!E2:E10000,"LinkedIn connect")+COUNTIF('Outreach Tracker'!E2:E10000,"LinkedIn message")` |
| A9 | `Replies` |
| B9 | `=COUNTIF('Outreach Tracker'!F2:F10000,"Replied")+COUNTIF('Outreach Tracker'!F2:F10000,"Interested")+COUNTIF('Outreach Tracker'!F2:F10000,"Meeting booked")` |
| A10 | `Meetings booked` |
| B10 | `=COUNTIF('Outreach Tracker'!G2:G10000,"Yes")` |
| A11 | `Follow-ups due today` |
| B11 | `=COUNTIF('Outreach Tracker'!Q2:Q10000,"<="&TODAY())` |
| A12 | `Final output rows` |
| B12 | `=COUNTA('Final Output'!A2:A10000)` |

If a workbook uses different columns, adapt formulas to the actual headers. Do not leave formulas broken; verify they evaluate or at least reference existing tabs/columns.
