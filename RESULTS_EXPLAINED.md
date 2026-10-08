# Results explained, block by block

Plain-language notes on what each block of `patch_latency_analysis.ipynb` produced and what it means. Working notes for the group, not report text.

## How to read the output

- **p-value:** how often a result like this would appear if nothing were going on. Below 0.05 counts as significant.
- **Numbers like `1.91e-02`:** scientific notation. `e-02` moves the decimal point two places left, so this is 0.019.
- **adjusted p:** the p-value multiplied by the number of comparisons made in that block (Bonferroni correction). Use this one to decide whether two groups differ.
- **rho and tau:** run from -1 to +1. Zero means no relationship, a negative value means one goes down as the other goes up.
- **H and U:** test statistics for Kruskal-Wallis and Mann-Whitney. The p-value next to them carries the verdict.
- **25%, 50%, 75%:** quartiles. 50% is the median, and 25% to 75% is the middle half of the cases.

## Block 1: Load and clean

- **What came out:** 410 CVEs.
- **What it means:** this is the full dataset. The sheet also held about 11,700 blank rows and one stray entry, which are removed here. Four rows spelled "Apple " with a trailing space are merged into Apple.

## Block 2: Compute latency

- **What came out:** 132 of 410 CVEs have a usable latency.
- **Why so few:** 277 CVEs have "???" as their discovery date, and one has no patch date.
- **What it means:** only a third of the dataset can be measured this way, and it is not a fair third. Discovery dates exist for 25 of 166 Microsoft CVEs (15%) but 53 of 80 Google CVEs (66%). Results on this measure describe zero-days with a recorded discovery date, not all zero-days.

## Block 3: Descriptive statistics

- **What came out:** median 9 days, mean 34.2 days, middle half between 3 and 30 days, slowest case 834 days. 48.5% are patched within a week and 90.2% within 90 days.
- **What it means:** a typical zero-day with a known discovery date is patched in about nine days. A few take months, which pulls the mean far above the median. That is why the median is the number to report.

## Block 4: Normality check

- **What came out:** W = 0.371, p < 0.001, skewness 7.38. The histogram has one tall bar on the left and a long tail to the right.
- **What it means:** the data is nowhere near a bell curve (W would be close to 1). Tests that assume a bell curve, such as the t-test and ANOVA, cannot be used. Every test from here on works on ranks instead.

## Block 5: RQ1, time trend (discovery dates)

- **What came out:** yearly medians move between 3 and 37.5 days with no direction. Spearman rho = -0.043 (p = 0.63), Kendall tau = -0.026 (p = 0.68).
- **What it means:** there is no sign that latency rose or fell between 2015 and 2024. The hypothesis that latency increases over time is not supported.
- **Caution:** 2017 to 2019 have only 2 to 5 cases each, so a small trend could be hidden.

## Block 6: RQ2, vendor comparison (discovery dates)

- **What came out:** medians of 4 days (Google), 6 (Adobe), 9.5 (Apple) and 24 (Microsoft). Kruskal-Wallis H = 36.68, p < 0.001.
- **Which pairs differ (adjusted p below 0.05):** Microsoft against each of the other three, and Google against Apple. Google vs Adobe and Apple vs Adobe do not differ.
- **What it means:** vendors differ, and Microsoft is the slowest on this measure.
- **Caution:** this is not a ranking of effort. Vendors ship on different release cycles, and Apple's 10 CVEs come from only 4 incidents.

## Block 7: Vendor box plot (discovery dates)

- **What came out:** Microsoft's box sits above the other three. Apple's box is almost a line. Google has a low box with many circles above it.
- **What it means:** the picture agrees with the test in Block 6. The Google circles above 80 days are all Android, which is the first hint that products within a vendor differ.
- **Note:** the axis is logarithmic, so each gridline is ten times the one below.

## Block 8: RQ3, product comparison (discovery dates)

- **What came out:** Chrome (median 4 days, 47 cases) vs Android (116 days, 6 cases): p < 0.001. Microsoft products: p = 0.59 with groups of 13, 3 and 3. Apple: p = 0.89 with groups of 8 and 2.
- **What it means:** only the Google comparison carries weight. For Microsoft and Apple the groups are too small to detect a difference, so those results are inconclusive. They do not show that the products are the same.

## Block 9: Reserved-date pull (run once)

- **What came out:** a reserved date for all 410 CVEs, saved to `reserved_dates.csv`.
- **What it means:** the reserved date is the day the CVE ID was set aside. It is the candidate stand-in for the missing discovery dates, and Block 10 tests whether it is a good one.

## Block 10: Proxy validation

- **What came out:** 133 CVEs have both dates. They are 55.7 days apart on average. The ID was reserved before discovery in 83 cases, on the same day in 1, and after in 49.
- **The two correlations:** 0.994 between the dates, 0.438 between the latencies. The first is high only because the dates span ten years. The second is the one that matters, and it is moderate.
- **By vendor:** IDs were reserved a median of 79 days before discovery for Microsoft, 31 for Adobe, 6 for Google and 6 for Apple.
- **What it means:** the reserved date is a weak stand-in. It is often set before the flaw is found, probably because vendors reserve IDs in advance. The error also differs by vendor, so it adds far more days to Microsoft than to Google.

## Block 11: Reserved-date dataset

- **What came out:** 403 usable CVEs. Six are dropped because the ID was reserved after the patch, and one has no patch date. Median 56 days, middle half 23.5 to 114.5 days, 12.9% patched within a week. Shapiro-Wilk W = 0.855, p < 0.001.
- **What it means:** the sample triples, but the clock is different. This latency includes however long the ID sat reserved before the flaw was found, so it runs much longer than the 9-day median in Block 3. The data is still not bell-shaped, so the same rank-based tests apply.

## Block 12a: RQ1 on reserved dates

- **What came out:** medians above 100 days in 2015, 2016, 2018 and 2019, and between 33 and 60 days from 2021 to 2025. Spearman rho = -0.440, Kendall tau = -0.312, both p < 0.001.
- **What it means:** on this measure latency appears to fall over time, which contradicts Block 5. Block 12b checks which of the two to believe.

## Block 12b: RQ1 check, same CVEs

- **What came out:** on the same 130 CVEs, reservation-to-patch latency falls over time (rho = -0.422) while discovery-to-patch latency is flat (rho = 0.003, p = 0.97). The reservation lead shrinks over time (rho = -0.552): a median of 37 to 138 days before discovery up to 2021, and about 2 days after discovery from 2022.
- **What it means:** the decline in 12a comes from the proxy, not from faster patching. IDs used to be reserved weeks before discovery and are now reserved at about the time of discovery, so the measured latency shrank by itself.
- **Conclusion for RQ1:** no evidence of a trend in patching speed under either measure.

## Block 13a: RQ2 on reserved dates

- **What came out:** medians of 45 days (Google), 46.5 (Apple), 52.5 (Adobe) and 62 (Microsoft). Kruskal-Wallis H = 29.65, p < 0.001. After adjustment, Microsoft differs from Google and from Apple. No other pair differs.
- **What it means:** vendors still differ, but the gaps are much smaller than in Block 6. The reservation lead time is added to every vendor, which pushes the medians together.

## Block 13b: RQ2 check, 2022 onwards

- **What came out:** for reservations from 2022 onwards, medians of 1 day (Google, 50 cases), 40 (Apple, 42) and 42 (Microsoft, 65). Kruskal-Wallis H = 30.64, p < 0.001. Google differs from both others. Microsoft and Apple do not differ (adjusted p = 0.98). Adobe appears in the table with 2 cases but is not tested.
- **What it means:** in the period where the reserved date is close to discovery, Google stands apart and Microsoft and Apple look alike.
- **Across Blocks 6, 13a and 13b:** two results hold every time. Vendors differ, and Google is faster than Microsoft. Apple's position changes from view to view, so it should not be ranked.
- **Caution:** the evidence that recent reserved dates track discovery comes mostly from Google CVEs.

## Block 14a: RQ3 on reserved dates

- **What came out:** Chrome (median 3 days) vs Android (115.5): p < 0.001. Microsoft products: H = 7.91, p = 0.019, driven by Windows (60 days) vs Internet Explorer (130), adjusted p = 0.015. iOS (22 days) vs WebKit (49): p = 0.49.
- **What it means:** Chrome and Android differ again, as in Block 8. Apple's two products do not differ, and this time the groups are large enough (30 and 27) for that to count. Microsoft's products seem to differ, which Block 14b checks.

## Block 14b: RQ3 check, Microsoft by period

- **What came out:** the median reservation year is 2016 for Internet Explorer and Office, and 2022 for Windows. Restricted to reservations up to 2021, the medians are 141, 120.5 and 124 days, with H = 0.99 and p = 0.61.
- **What it means:** the Microsoft difference in 14a was a period effect. Internet Explorer cases are older, and older cases have longer reservation-to-patch latencies (Block 12b). Compared within one period, the products do not differ.
- **Conclusion for RQ3:** a product difference within a vendor is supported for Google only.

## Block 15: CVSS pull (run once)

- **What came out:** a CVSS v3 score for all 410 CVEs, saved to `cvss_scores.csv`. 313 are NVD's own assessment, and 97 come from the vendor or CISA because NVD has not scored those CVEs itself.
- **What it means:** severity is now available for every CVE. The 97 without an NVD assessment are mostly recent.

## Block 16a: RQ4, severity

- **What came out:** 1 CVE is Low, 48 Medium, 305 High and 56 Critical.
- **Discovery dates:** medians of 69 days (Medium), 9 (High) and 4 (Critical). H = 30.87, p < 0.001, every pair differs. Spearman rho = -0.648.
- **Reserved dates:** medians of 60, 57 and 25 days. H = 9.33, p = 0.009. Medium and High do not differ. Spearman rho = -0.216.
- **What it means:** more severe vulnerabilities are patched faster, as hypothesised. The link is strong on discovery dates and weak on reserved dates.
- **Caution:** three quarters of the CVEs are High, so severity barely varies. The bands are also tied to vendors: in the discovery set, 8 of the 21 Medium cases are Samsung, and 20 of the 25 Critical cases are Google or Adobe.

## Block 16b: RQ4 check, within vendors

- **What came out:** on discovery dates, the link holds inside Microsoft (rho = -0.521, p = 0.008) and inside Google (rho = -0.524, p < 0.001). Apple and Adobe show nothing, with 10 and 14 cases. On reserved dates, it holds only inside Google (rho = -0.396), and Microsoft shows none (rho = -0.022, p = 0.78).
- **What it means:** on discovery dates the severity link is more than a vendor difference, since it appears within two vendors separately. On reserved dates it survives only for Google, where it reflects Chrome flaws scoring higher and being patched faster than Android flaws.
- **Conclusion for RQ4:** supported in direction, clearly on discovery dates and weakly on reserved dates.

## Summary

**Holds in every view**

- Vendors differ in patch latency.
- Google is faster than Microsoft.
- Chrome is faster than Android.
- Latency does not increase over time.

**Depends on the measure**

- The typical latency: 9 days from discovery, 56 days from reservation.
- Where Apple and Adobe sit among the vendors.
- How strong the link between severity and latency is.

**Produced by the proxy, not by patching**

- The decline over time on reserved dates (Block 12).
- The difference between Microsoft products (Block 14).
