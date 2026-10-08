# Results summary

Working notes for the group, not report text. Tables with the full numbers are in `results_skeleton.tex`.

## The two measures

| | Discovery-to-patch | Reservation-to-patch |
|---|---|---|
| Start of the clock | Date Discovered (Project Zero) | CVE dateReserved (CVE List) |
| Usable CVEs | 132 of 410 | 403 of 410 |
| Median latency | 9 days | 56 days |
| Patched within 7 days | 48.5% | 12.9% |
| Normality (Shapiro-Wilk) | Rejected, W = 0.371 | Rejected, W = 0.855 |

All tests are rank-based: Spearman, Kendall, Mann-Whitney, Kruskal-Wallis.

## The main issue: the proxy validates poorly

On the 133 CVEs that have both dates:

- The two dates are 55.7 days apart on average (SD 88.3).
- The ID was reserved before discovery in 83 cases, so it is not a discovery date.
- The two latencies agree only moderately (Spearman 0.438). The 0.994 between the dates is high only because the dates span ten years.
- The lead differs by vendor: Microsoft 79 days, Adobe 31, Google 6, Apple 6.
- The lead differs by period: 54 days up to 2021, about 2 days after discovery from 2022.

So the proxy is not a neutral error. It affects RQ1 and RQ2 directly.

## Findings per research question

| RQ | Discovery dates | Reserved dates | Check | Conclusion |
|---|---|---|---|---|
| RQ1, time | No trend (rho = -0.043, p = 0.63) | Decline (rho = -0.440, p < 0.001) | Same 130 CVEs: discovery latency flat (0.003), reserved latency falls (-0.422) | No trend in patching speed. The decline comes from the proxy. Hypothesis not supported. |
| RQ2, vendors | H = 36.68, p < 0.001. Medians: Google 4, Adobe 6, Apple 9.5, Microsoft 24 | H = 29.65, p < 0.001. Medians: Google 45, Apple 46.5, Adobe 52.5, Microsoft 62 | From 2022: H = 30.64, p < 0.001. Medians: Google 1, Apple 40, Microsoft 42 | Vendors differ, and Google is faster than Microsoft, in every view. Apple and Adobe cannot be ranked. |
| RQ3, products | Chrome 4 vs Android 116 days (p < 0.001). Microsoft and Apple groups too small | Chrome 3 vs Android 115.5 (p < 0.001). Microsoft p = 0.019. iOS vs WebKit p = 0.49 | Microsoft within one period: p = 0.61 | Supported for Google only. The Microsoft difference was a period effect. |
| RQ4, severity | Medians 69, 9, 4 days for Medium, High, Critical. rho = -0.648 | Medians 60, 57, 25. rho = -0.216 | Within vendors: holds for Microsoft and Google on discovery dates, only for Google on reserved dates | Direction matches the hypothesis. Strong on discovery dates, weak on reserved dates. |

## What holds in every view

- Vendors differ in patch latency.
- Google is faster than Microsoft.
- Chrome is faster than Android.
- Latency does not increase over time.

## Decisions needed

1. Which measure is the headline, given the proxy result?
2. Do we report both measures in adjacent columns? The skeleton is built that way.
3. Do we limit the reserved-date analysis to 2022 onwards, where it tracks discovery?
4. How do we answer the title question? The data shows that vendors differ. It cannot show intent.
5. Hypotheses: RQ1 not supported, RQ3 supported for Google only, RQ4 supported in direction, RQ2 has none yet.

## For Limitations

- Discovery dates are unevenly available: 15% of Microsoft CVEs, 66% of Google CVEs.
- Some incidents are counted several times (Apple's 10 discovery-dated CVEs come from 4 incidents).
- The reservation lead time varies by vendor and by year.
- CVSS barely varies: 305 of 410 CVEs are High, and 262 score 7.8 or 8.8.
- Recent years are cut short by the snapshot.

## Methodology edits

- Data cleaning: blank rows and one stray entry removed, whitespace trimmed in vendor names.
- Name the measure reservation-to-patch latency. Give the CVE List source and the pull date. Say that 6 CVEs reserved after their patch date are excluded.
- Proxy validation: add Spearman on the two latencies.
- State alpha = 0.05, and the pairwise Mann-Whitney tests with Bonferroni correction.
- Vendor counts analysed: Microsoft 166, Google 79, Apple 64, Adobe 28. Excluded vendors have twelve or fewer.
- Product counts analysed: Windows 98, Internet Explorer 23, Office 18, Chrome 61, Android 18, iOS 30, WebKit 27.
- Severity: CVSS v3.1 (v3.0 for one CVE) from NVD via the fkie-cad mirror. 313 scores are NVD's own, 97 come from the vendor or CISA. Low has one CVE and is left out of the tests.
