---
name: ask-vc-fundraising-skill
description: Use when working with Ask VC fundraising homework, local or Google Drive investor outreach workbooks, startup fundraising materials, investor source lists, VC contact enrichment, cold investor emails, LinkedIn messages, Pipeline investors, Outreach Tracker, or recurring fundraising follow-up operations.
---

# Ask VC Fundraising Skill

## Overview

Use this skill to operate the Ask VC fundraising workflow end-to-end: ingest startup materials, create or find the workbook, fill the target investor profile, build a maximum-coverage investor universe, draft outreach, and keep Pipeline / Outreach Tracker current.

The investor-facing language is English unless the user asks otherwise. User-facing collaboration can be in the user's language.

## Guided Workflow

This skill must lead the user step by step. Do not skip checkpoints or run paid/enrichment steps early.

1. **Intake materials.** Ask the user to share all available startup materials: website, pitch deck, one-liner, memo, pricing, traction, team info, prior investor lists, and existing outreach state.
2. **Find or create workbook.** Prefer Google Drive / Google Sheets. If Drive tools are available, first offer to create a new Google Sheet or upload the template workbook to Google Drive. If the user refuses Drive, or Drive tools are unavailable, work with a local `.xlsx` from `assets/ask-vc-fundraising-workbook-template.xlsx`. If the user gives an existing local or Drive workbook, use that file. The live workbook must be a Google Sheet or Excel workbook (`.xlsx`, `.xls`, `.xlsm`), never CSV-only. See `references/ask-vc-sheet.md`.
3. **Fill Page 1.** Fill `ДЗ№1 - Инвестор` only after reading materials. Ask targeted questions only for missing fields needed to complete Page 1.
4. **Checkpoint: Page 1 approval.** Show the completed Page 1 values to the user and explicitly ask them to confirm or edit. Do not source investors until the user confirms Page 1.
5. **Build built-in universe.** After Page 1 approval, use the built-in local cache in `data/investor_sources/` to build `Master Fund Universe`, dedupe, score, and create an initial `Pipeline investors` shortlist from the best ready funds.
6. **Checkpoint: additional lists.** Show counts and the workbook path, then ask whether the user has funds, contacts, CSV/XLSX files, Airtables, Google Sheets, or personal targets to add, or whether to continue with the current universe. Use/create `Источники инвесторов` only for user-provided additional lists.
7. **Optional paid discovery checkpoint.** Only after built-in and user-provided lists are processed, ask whether the user wants paid additional investor discovery via Exa/external research. Explain API key needs and approximate query/cost before running.
8. **Contact mapping.** Map 1-3 relevant people per priority fund using free/public data where practical.
9. **Paid contact enrichment checkpoint.** Before using paid tools to find LinkedIn/email at scale, explain the tool/API key, expected unit cost, and ask for approval.
10. **Enrich and score contacts.** After approval or using free data, create the contact-level outreach list with `confirmed`, `inferred`, `not_found`, and `needs review` labels.
11. **Select message templates.** Read `references/message-templates.md`, choose the best-fit candidate templates for this company, and present them to the user for approval. If writing to `ДЗ№2 - Письма инвестору` before approval, either include the full template library or clearly mark selected candidates as `Candidate - pending approval`.
12. **Checkpoint: message approval.** Ask the user to confirm the universal template and niche templates. Do not fill final personalized `Outreach Tracker` messages until approved.
13. **Write final trackers.** Fill `Outreach Tracker` at person level with personalized emails/LinkedIn messages, update `Pipeline investors` at fund level, and create/update `Final Output`. Ensure `Metrics` uses formulas, not static zeros.
14. **Export schema-compatible outputs.** After final tracker writing, export `final_output.json`, `output.csv`, `sendable_with_email.csv`, and `needs_review.csv` using the standard core fields defined below.
15. **Operate iteratively.** Prepare next 10 contacts to message, daily actions, overdue follow-ups, or updated status changes on request.

## Clarifying Questions

After startup materials are supplied, ask only questions that affect Page 1, targeting, or tracker accuracy:

- Raise: target round size, minimum check, lead/co-lead/follower preference.
- Company: HQ/legal entity, founder location, target customer geographies.
- Stage/traction: revenue status, paying customers, ARR/MRR if allowed, notable pipeline/customers.
- Investor filters: must-have sectors, must-avoid investor types, geography limits, warm intro availability.
- Outreach state: who was already contacted, last contact date, next action, response status.
- Page 1 `Industries` and `Business model` must be optimized for investor sourcing/scoring, not generic company description.

If the user wants speed, proceed with clearly labeled assumptions.

## Writing Rules

- Keep Ask VC Page 1 concise: one short phrase or sentence per cell.
- In Page 1, write `Team` as credibility proof: CEO sales/company-building/exit proof, CTO technical/data proof, and advisors if relevant.
- In Page 1, write `Industries` as short investor-search keywords, e.g. `B2B SaaS; AI agents; marketing automation; adTech; marTech; marketing analytics; user acquisition; performance marketing`.
- In Page 1, write `Business model` as a short business model filter, e.g. `B2B SaaS subscription; AI workflow automation for growth and marketing teams`.
- Keep investor emails human: `Hi FirstName,` then short paragraphs and `Best, [FounderName]`.
- Default investor outreach CTA is `Let's have a call next week?`; do not use deck-first asks like `Could I send over a short deck?` unless the user explicitly requests that style.
- Do not write database-looking hooks like `We screened X because source list shows...` in outbound messages. Keep those details in internal notes only.
- Preserve the original Ask VC workbook layout. For `Шаблон Pipeline инвесторов` and `Outreach Tracker`, keep the template's base columns, filters, dropdowns, and colors; append extra generated columns to the right instead of replacing the table.
- Mark uncertain data clearly: `confirmed`, `inferred`, `not_found`, `needs review`.
- For maximum-coverage lists, include low-confidence contacts but separate them from send-ready contacts.
- Use `Master Fund Universe` for the broad fund-level list. Use `contact-level outreach list` for individual people. Avoid calling the broad fund list a `shortlist`.
- Maintain stable IDs: every fund must have a `Fund ID`; every person row must have a `Contact ID`; `Pipeline investors`, `Outreach Tracker`, and `Final Output` must be joinable through those IDs.
- Never put a firm/company LinkedIn URL in a person LinkedIn column. If a person LinkedIn is missing, leave it blank and mark it in `Review Note`.
- Always label contact quality with `Contact Tier`, `Source Type`, and `Email Confidence`: use `confirmed`, `inferred`, or `not_found`; use `public`, `probabilistic`, or `not_found`.
- Keep Pipeline fund-level and Outreach person-level. Pipeline may show the primary contact, but the canonical contact row lives in `Outreach Tracker`.
- Include score breakdown fields when scoring funds: sector, stage, geography, check size, lead behavior, portfolio adjacency, recency, and partner relevance.
- Create `Final Output` as the old-skill-compatible send/review table, not as a replacement for Pipeline or Outreach Tracker.
- Select the best-fit message templates for the user's startup and investor segments; do not blindly use the first templates in the library.
- Before writing final outreach rows, show selected template candidates and wait for approval.
- Never invent portfolio facts or warm intros. Put source-list rationale in internal notes, not investor-facing copy.

## External API / Cost Boundary

Do not use paid external enrichment/search APIs until the company profile and Page 1 are filled and approved. Split paid work into two separate opt-in checkpoints.

Usually free/no external API key:

- reading local materials
- searching local files
- reading built-in investor source cache from `data/investor_sources/`
- reading/writing Google Sheets through the user's connected Drive/Sheets tools
- parsing user-provided CSV/XLSX files
- drafting messages from existing data

Paid checkpoint 1: optional additional investor discovery.

- Use only after built-in Ask VC source cache and user-provided lists have been processed.
- Explain that Exa or another research API may be used for extra fund discovery.
- Explain where to provide the API key and estimate cost from planned query volume before running.

Paid checkpoint 2: contact enrichment for collected funds.

- Explain that finding specific people, LinkedIn profiles, and emails may require people search and email enrichment tools.
- State the expected unit cost when known. Example: Icypeas/Apify email finder previously cost about `$0.028` per contact result.
- Ask for approval before running paid enrichment at scale.

Do not claim Airtable/Folk/source-list processing requires Apify by default. Use free/direct/local cache first. Only propose paid scraping if a source is inaccessible and the user wants to recover it.

## File Outputs

When producing local artifacts, use a project-specific folder such as:

`tmp/ask-vc-fundraising-skill/data/<campaign_name>/`

Recommended outputs:

- `company_profile.json`
- `source_manifest_used.json`
- `fund_candidates.json`
- `master_fund_universe.json`
- `investor_list.json`
- `investor_contacts.json`
- `scored_funds.json`
- `message_template_candidates.json`
- `approved_message_templates.json`
- `final_output.json`
- `output.csv`
- `sendable_with_email.csv`
- `needs_review.csv`

When writing the live workbook, prefer Google Sheets updates if a Google Sheet is available. If the workbook is local-only, update a local Excel workbook (`.xlsx`, `.xls`, `.xlsm`) and tell the user the exact path. Do not use CSV as the live workbook format.

Minimum final export columns:

`priority_rank, fund_id, fund_name, score_total, score_max_possible, investor_name, investor_title, contact_tier, source_type, work_email, email_confidence, linkedin, outreach_subject_line, outreach_email_body, outreach_linkedin_dm, outreach_personalization_hook, outreach_cta, review_note`

Keep these export fields stable so downstream outreach tooling can consume the same files across campaigns.

## Regular Operating Mode

When the user asks to continue fundraising work:

1. Read `Master Fund Universe`, `Pipeline investors`, `Outreach Tracker`, and `Metrics`.
2. Identify stale next actions and overdue follow-ups.
3. Suggest or write the next batch: new outreach, follow-up #1, follow-up #2, deck sent, meeting prep, or pass.
4. Update tracker rows and ensure `Metrics` formulas still reference the correct tabs/columns after changes.

Do not overwrite existing user-entered tracker history. Append or update only the relevant cells.

## Iteration Commands

Support these recurring requests:

- "Prepare next 10 contacts" -> return/send a copy-paste batch from Outreach Tracker sorted by priority and due status.
- "What should I do today?" -> inspect Pipeline + Outreach Tracker, then list first outreach, follow-ups, meetings, and manual research due today.
- "Update pipeline after replies" -> update statuses, responses, meeting booked, next action, follow-up date, and metrics.
- "Add this investor list" -> import/deduplicate/score/enrich, then merge into trackers.
- "Regenerate messages" -> rewrite messages in English using the current Page 1 profile and startup materials.

## References

- Read `references/ask-vc-sheet.md` when filling or editing the Ask VC workbook.
- Read `references/vc-pipeline.md` when sourcing investors, enriching contacts, scoring fit, drafting messages, exporting CSVs, or updating pipeline/outreach trackers.
- Read `references/investor-sources.md` when processing the Ask VC source lists or external investor spreadsheets.
- Read `references/message-templates.md` when drafting, comparing, approving, or regenerating investor email and LinkedIn message variants.

## Built-In Investor Source Cache

Before reading live source links, use the local cached CSVs in:

`data/investor_sources/`

Start with:

`data/investor_sources/source_extraction_manifest.json`

The manifest lists every built-in source, saved CSV filename, row count, and available raw/XLSX/HTML files. Treat these cached files as the default built-in investor universe. Only re-open live Airtable/Folk/Google links if the user asks for a refresh or a cached file is missing/corrupt.

When building a campaign:

1. Read `source_extraction_manifest.json`.
2. Load every `csv_files` entry listed in the manifest.
3. Normalize and deduplicate funds/contacts into `Master Fund Universe`.
4. Preserve `source_list` provenance from the manifest source name and file.
5. Then ask whether the user has additional lists to add.

## Message Template Library

The skill has a persistent template library in:

`references/message-templates.md`

When drafting outreach, do not silently choose one template and discard the rest. Choose best-fit candidates for the startup and investor segments, show them to the user, and ask them to approve:

- 1 universal base template
- 2 niche templates for specific investor groups
- backup variants to keep for later regeneration

If the user wants all templates in the workbook, write the full library to `ДЗ№2 - Письма инвестору`. Otherwise write only selected candidates and mark them `Candidate - pending approval` until the user confirms.

Recommended outputs:

- `message_template_candidates.json`
- `approved_message_templates.json`

Use approved templates as the base style for personalized rows in `Outreach Tracker`, while keeping all non-selected variants available for future campaigns or regeneration.
