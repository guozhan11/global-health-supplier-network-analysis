# PPOL 5205 Project: Market Structure in Global Fund–Financed Procurement

## Folder layout
- `data/raw/` original downloads, never edited
- `data/processed/` cleaned tables produced by scripts in `src/`
- `src/` numbered Jupyter notebooks, run in order (`00_utils.ipynb` holds shared helpers; the others load it with `%run`)
- `outputs/figures`, `outputs/tables` figures and tables for memos / final report
- `docs/` The Medicine Supplier Map, the Concept Showcase website (`data.js` is written by `src/06_showcase_data.ipynb`)
- `memos/` Memo 1, Memo 2, final report drafts
- `instructions/` course project guidelines and other instructions (not tracked by git)

## How to run
Tested with Python 3.12. From the project root:
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace src/0[1-6]*.ipynb
```
This rebuilds `data/processed/` and `outputs/` and the website data from `data/raw/` in about a minute and saves the results in the notebooks. To run them by hand, use `jupyter notebook` and run `src/01` to `src/06` in order.

## Data sources
### Global Fund Price & Quality Reporting (PQR), Transaction Summary
- File: `data/raw/pqr_transactions_2026-10-10.csv`
- Downloaded 2026-10-10 from the public Tableau view https://insights.theglobalfund.org/t/Public/views/PriceQualityReportingTransactionSummary/TransactionSummary ("Transaction Summary" view, sheet "1.Search Transaction", Crosstab -> CSV, all filters = All)
- Encoding: UTF-16 LE, tab-separated. Read with `pd.read_csv(path, sep="\t", encoding="utf-16")`; drop the first "Grand Total" row.
- 98,646 transactions (one row per Primary Key), 22 columns, PO dates 2003-2026 (dense from 2008)
- 137 countries, 565 manufacturers, 224 products, 6 product categories
- Medicines only (ARV/COVID, TB, malaria): 74,692 rows, 124 manufacturers, 128 products
- Caveats: see Global Fund PQR data caveats note (entry errors, extra costs bundled, currency conversion)

### WHO priority medicines
- `data/raw/who_eoi_priority_medicines.csv`: 71 molecule combinations, transcribed from the priority sections of WHO's latest Expressions of Interest: [HIV, 23rd, 9 July 2025](https://extranet.who.int/prequal/sites/default/files/document_files/EOI_23rd%20HIV_9July_2025_clean_PQ.pdf), [TB, 23rd, 24 February 2026](https://extranet.who.int/prequal/sites/default/files/document_files/EOI_TB_v23_final2026-2-24%20%282%29.pdf), [malaria, 24th, 19 November 2025](https://extranet.who.int/prequal/sites/default/files/document_files/EOI-MalariaV24_2.pdf).
- Strengths are not kept. `adult_form` and `child_form` show which populations the invitation lists.

### WHO Prequalification lists
- Downloaded 2026-10-10 via the "Download CSV" export on extranet.who.int/prequal (medicines)
- `data/raw/who_pq_fpp_2026-10-10.csv`: 655 finished pharmaceutical products, 137 applicants, 8 columns
  (WHO ref no., INN/form/strength, product type, therapeutic area, applicant, dosage form, basis of listing, date of PQ)
  - Dates: 564 parsed (2002-2026); the 77 "Alternative Listing" rows (SRA-approved, e.g. USFDA ANDA) have no PQ date
  - Date format like "25  Jun,  2026": collapse spaces, drop comma, parse "%d %b %Y"
- `data/raw/who_pq_api_2026-10-10.csv`: 194 active pharmaceutical ingredients, 65 applicants, PQ dates 2011-2026
- Caveat: lists show currently prequalified products only; withdrawn/delisted products are not included (survivor bias for early years)

## Processed data (`data/processed/`, rebuilt by scripts)
`src/01_clean_data.ipynb` does both steps:
1. `firm_crosswalk.csv`
   - every raw manufacturer/applicant name in PQR, PQ FPP, PQ API (326 names) mapped to
     `firm` (spelling and legal-entity variants merged), `group` (corporate parent after
     mergers, e.g. Matrix -> Mylan/Viatris, Lek -> Sandoz, Guilin -> Fosun), `hq_country`
   - rules live in `src/00_utils.ipynb` (ordered regex list; first match wins), others merge
     on a normalized key (lowercase, no punctuation, legal suffixes removed)
   - `method`: rule / unchanged / junk; `country_method`: researcher-coded / name-heuristic
   - result: PQR 124 raw names -> 110 firms -> 104 groups; 227 firms across all three sources
2. `pqr_medicines_clean.csv`, `pq_fpp_clean.csv`, `pq_api_clean.csv`
   (raw tables + firm, group, hq_country; 3 PQR rows whose manufacturer field is a note are dropped)

## Network analysis (`src/03_network.ipynb`)
Runs the main spec (min 2 transactions per firm-product link) and the sensitivity tables (min 1), each with 200 null draws.
1. Build -> `data/processed/network/` bipartite edges, product edges (phi), node tables
2. Analysis -> Louvain communities, null model (200 rewirings),
   centrality; tables t7-t9
3. Figures (main spec only) -> f5 product space, f6 null model, f7 community composition
Findings are explained in the notebook. Packages: `requirements.txt`.

## Entry prediction (`src/04_entry.ipynb`)
Runs all four steps:
1. Panel -> `data/processed/entry_panel.csv` (firm x product x cutoff, density, outcome)
2. Models -> t10 logits (clustered SE), t11 out-of-sample comparison, t11b entry by density
3. Candidates -> t12 possible suppliers and t14 network reading for WHO priority medicines
4. Figures -> f8, f9. Findings are explained in the notebook.

## Robustness: second buyer (PEPFAR/SCMS)
- `data/raw/scms_delivery_history_2006-2015.csv`: USAID SCMS Delivery History (data.usaid.gov a3rc-nmf6,
  now offline), from the Internet Archive capture of the official CSV; 10,324 rows, 33 columns.
- `src/05_robustness.ipynb` -> t13, f10;
  findings are explained in the notebook. Data-availability notes: `data/other_buyers_data_notes.md`.
