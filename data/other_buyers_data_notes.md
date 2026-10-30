# Other buyers: data availability check (2026-10-10)

## Who buys (funding shares as proxy for procurement)
- HIV ARVs, 34 PEPFAR countries, 2019 (USD 1.95 bn): country governments 45% (mostly South Africa), Global Fund 33%, PEPFAR 21%. Excl. South Africa: Global Fund 45%, PEPFAR 29%. (KFF)
- Malaria funding, 2013 (USD 2.6 bn): Global Fund 40%, US/PMI 26%, domestic 20%. (KFF)
- TB funding, 2024, 131 LMICs (USD 5.9 bn): domestic 82% (BRICS = 64% of domestic); international 18%, of which Global Fund 73%. (WHO Global TB Report 2025)
=> Single-institution buyers: Global Fund (1) and US government via USAID GHSC-PSM (2). "Domestic governments" is many separate buyers.

## US government (GHSC-PSM / SCMS)
| Dataset | Coverage | Supplier fields? | Status |
|---|---|---|---|
| GHSC-PSM Health Commodity Delivery Dataset (data.usaid.gov tikn-bfy4) | USAID/PEPFAR/PMI orders, ~2016-2024, 104 columns: country, product category, product name (generic), quantities, illustrative price, funding source, delivery dates/OTIF | **No vendor or manufacturer column** | Original offline (data.usaid.gov, data.gov catalog 404). Wayback snapshot 2025-01-29 of full CSV exists: web.archive.org/web/20250129194228id_/https://data.usaid.gov/api/views/tikn-bfy4/rows.csv?accessType=DOWNLOAD (first 37 MB = ~35k rows; total larger) |
| GHSC-PSM procurement dataset (ksx2-ew8m) | similar | unknown | Offline, no Wayback copy of CSV |
| SCMS Delivery History / Supply Chain Shipment Pricing Data (a3rc-nmf6) | PEPFAR HIV drugs + lab, 2006-2015, 10,324 rows, 33 columns | **Yes: Vendor, Manufacturing Site, Brand, Molecule** | Original + AmeriGEOSS copy offline; many Kaggle mirrors (e.g. kaggle.com/datasets/divyeshardeshana/supply-chain-shipment-pricing-data) |

## Others
- Domestic governments: no common source (e.g. South Africa national tender awards as PDFs).
- UNICEF Supply Division: contract awards >= USD 500k, PDF only, 2013-2026.
- GDF / UNICEF / IDA / PFSCM often procure on behalf of the Global Fund; already visible in PQR `channel` column.
