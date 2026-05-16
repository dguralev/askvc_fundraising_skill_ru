# Ask VC Investor Sources

Process these sources sequentially before any optional paid extra VC search. Prefer accessible structured data. If a source is inaccessible, mark it unavailable and continue.

## Built-In Sources

1. US Woman Angels  
   `https://airtable.com/appytsOoxXWmjvQ2R/shrzWIHSoK0gvE82D/tble9siT6RDtZNc39`
2. The Ultimate List of 750+ Seed Funds  
   `https://docs.google.com/spreadsheets/d/1VVr-z3ujLzWZGHX3-3C9C9dBVtnjALa3_cr1xGlDPmE/edit`
3. Active VC list  
   `https://airtable.com/appYlRDIZLwvRPsRh/shrkohpeE2AO2ldeq/tbl5Q8N7NuW22z5Bt?`
4. No Warm Intro Required List  
   `https://airtable.com/appS9cG0ccqmQM111/shrsDIW1FMuA5cI9P/tblaahhCCc2v0065Q/viwdm9nLc4Aj3sHJO?`
5. New Europe-Based VC Funds  
   `https://docs.google.com/spreadsheets/d/1BUkJPnBqny3VNmoIcXN1MgS7KtviiiLY9ejYh74r9a8/edit?gid=1029036388#gid=1029036388`
6. 370+ Israeli VC Funds  
   `https://docs.google.com/spreadsheets/d/10I1Iga7bQqikeqvMA_CgzyYsvrhw4BcpWUJstPxLTw0/edit?gid=558818348#gid=558818348`
7. Europe VCs List  
   `https://docs.google.com/spreadsheets/d/1cRdFZhXLqat04xe7qO-p48wXQ00GWJyrzgHSb3YVIp0/edit?usp=sharing`
8. New VC Funds below $200M in size  
   `https://docs.google.com/spreadsheets/d/1ebGZ6-ivf_3woBGC4Kz_3217DhjGsefoRu_5iP3nuFQ/edit#gid=0`
9. US Seed Funds List  
   `https://docs.google.com/spreadsheets/d/1VVr-z3ujLzWZGHX3-3C9C9dBVtnjALa3_cr1xGlDPmE/edit?usp=sharing`
10. Seed & Series A US VCs List  
    `https://app.folk.app/shared/Seed-and-Series-A-US-VC's-b6PIuvmTQF7Mdj4sM4TM1wbsP69DwQP0`
11. US Women Angels List  
    `https://airtable.com/shrzWIHSoK0gvE82D/tble9siT6RDtZNc39?ref=vitalize.vc`
12. Diverse Background Angels  
    `https://airtable.com/shrNoRaOvuL9YPGd1/tblqGWGDzEwf8OnGL`
13. Active Angels in the US List  
    `https://docs.google.com/spreadsheets/d/1tugiIXvkprHMrtLX15GC5ZryXX2g0y3RQzmP7B2KCFw/edit#gid=577007066`
14. Female Angels List  
    `https://airtable.com/shrUhvQQmot1ACwnb/tbl08Q4Ks1CkLBZ1D`
15. Pre-seed VCs List  
    `https://airtable.com/shrpwuo8CPeRiq2Wc/tblG7mCsi7g8au7cm/viwYYV957BBBZb05L?backgroundColor=cyan`
16. Venture Debt in Australia List  
    `https://airtable.com/shrGflFe4YWW2Jjbs/tblQIsXHKoY0EtXO7?backgroundColor=purple&viewControls=on`

## Local Cache

Use the built-in local cache before fetching live URLs:

`data/investor_sources/source_extraction_manifest.json`

This manifest points to cached CSVs in `data/investor_sources/`. It currently covers sources 2-16 from the built-in list, with source 9 intentionally duplicated from source 2 because both URLs point to the same Google Sheet. Source 1 is also present as an older Airtable extraction file named `airtable_us_women_angels.csv`, while source 11 is the current canonical `11_us_women_angels.csv`.

Default behavior:

1. Load the manifest.
2. Read each listed CSV.
3. Preserve the manifest `name`, `id`, and filename as source provenance.
4. Use raw JSON/XLSX/HTML files only when a CSV needs re-extraction or field inspection.
5. Re-fetch live URLs only if the user requests a refresh or cache validation fails.

Known cached files:

| ID | Source | Main CSV |
|---|---|---|
| 02 | The Ultimate List of 750+ Seed Funds | `02_ultimate_seed_funds.csv` |
| 03 | Active VC list | `03_active_vc_list.csv` |
| 04 | No Warm Intro Required List | `04_no_warm_intro_required.csv` |
| 05 | New Europe-Based VC Funds | `05_new_europe_based_vc_funds.csv` |
| 06 | 370+ Israeli VC Funds | `06_israeli_vc_funds.csv` |
| 07 | Europe VCs List | `07_europe_vcs_list.csv` |
| 08 | New VC Funds below $200M in size | `08_new_vc_funds_under_200m.csv` |
| 09 | US Seed Funds List | `09_us_seed_funds_list.csv` |
| 10 | Seed & Series A US VCs List | `10_seed_series_a_us_vcs_folk.csv` |
| 11 | US Women Angels List | `11_us_women_angels.csv` |
| 12 | Diverse Background Angels | `12_diverse_background_angels.csv` |
| 13 | Active Angels in the US List | `13_active_angels_us.csv` |
| 14 | Female Angels List | `14_female_angels.csv` |
| 15 | Pre-seed VCs List | `15_pre_seed_vcs.csv` |
| 16 | Venture Debt in Australia List | `16_venture_debt_australia.csv` |

## Processing Rules

For each source:

1. Fetch/read as much structured data as access allows using free/direct access first.
2. Normalize fund/person fields: fund name, website, contact, title, LinkedIn, geography, stage, sector, check size, source URL.
3. Filter for the company profile but do not discard uncertain fits silently; label them `review`.
4. Deduplicate by normalized fund domain first, then fund name.
5. Preserve source provenance in `source_list` or notes.
6. Add resulting funds to the `master fund universe`, not to a small shortlist.

Do not use paid scraping for these sources by default. If a source cannot be accessed directly, mark it `unavailable`; only suggest paid scraping if the user explicitly wants to recover that source.

## Output Labels

- `ready`: strong enough to include in outreach.
- `review`: possible fit or auto-found contact; verify before sending.
- `low-priority`: likely wrong stage/sector/check/geography but preserved in master list.
- `unavailable`: source could not be accessed.

## Ask for Extra Lists

After built-in sources are processed, ask whether the user has additional investor lists, CSVs, Airtables, Google Sheets, or personal targets to include. Merge them into the same normalization/deduplication pipeline.
