# Patch latency for zero-day exploits: analysis

Analysis code for TPM020 Deliverable 2, Group 4. The notebook reproduces every number in the Results chapter.

## Files

| File | What it is |
|---|---|
| `patch_latency_analysis.ipynb` | The analysis, in 16 labelled blocks, with outputs and an explanation under each result |
| `0day _In the Wild_.xlsx` | Google Project Zero sheet, snapshot of 5 October 2026 |
| `reserved_dates.csv` | CVE reserved date per CVE, pulled 8 October 2026 from the CVE List (`CVEProject/cvelistV5`) |
| `cvss_scores.csv` | CVSS v3 base score per CVE, pulled 8 October 2026 from NVD via the mirror `fkie-cad/nvd-json-data-feeds` |
| `RESULTS_EXPLAINED.md` | What each block produced and what it means, in plain language |
| `RESULTS.md` | Summary of the findings and the open decisions |
| `results_skeleton.tex` | Results chapter with filled tables and the facts for each paragraph |
| `*.png` | The four figures the notebook saves |

## How to run

1. Install Anaconda, or run `pip install pandas scipy matplotlib openpyxl notebook`.
2. Open `patch_latency_analysis.ipynb` in Jupyter.
3. Choose Kernel > Restart & Run All.

Blocks 9 and 15 download data. They are stored as raw cells, so Run All skips them and reuses the two CSV files. Leave them that way unless you want a fresh snapshot: the online records change, and the numbers would stop matching the paper. To re-pull, change both cells to Code and run `pip install requests`.

## Blocks

| Block | What it does |
|---|---|
| 1 | Load and clean |
| 2 | Compute discovery-to-patch latency (`usable`, 132 CVEs) |
| 3 | Descriptive statistics |
| 4 | Normality check |
| 5 | RQ1, time trend |
| 6 | RQ2, vendor comparison |
| 7 | Vendor box plot |
| 8 | RQ3, product comparison |
| 9 | Reserved-date pull (run once) |
| 10 | Proxy validation |
| 11 | Reservation-to-patch dataset (`res`, 403 CVEs) |
| 12a, 12b | RQ1 on reserved dates, and the same-CVE check |
| 13a, 13b | RQ2 on reserved dates, and the check from 2022 onwards |
| 14a, 14b | RQ3 on reserved dates, and the Microsoft period check |
| 15 | CVSS pull (run once) |
| 16a, 16b | RQ4, severity, and the within-vendor check |

## Notes

- Significance level is 0.05. Pairwise p-values are Bonferroni-adjusted.
- `gap` in Block 10 is discovery date minus reserved date. A positive value means the ID was reserved first.
- The code was written with AI assistance (Claude) and run by Kaushik. This belongs in the AI-use appendix.
