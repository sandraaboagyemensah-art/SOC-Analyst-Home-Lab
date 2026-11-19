## Verified Progress and Investigation Results

### Project 04: Windows and Linux Security Event Correlation

This project investigates authentication activity across Windows and Linux systems using Splunk. The goal is to understand failed-login activity, identify where authentication events originate, and compare event patterns across platforms.

### Completed and Verified Work

**1. Combined Windows and Linux Authentication Investigation — Step 26**

- Successfully searched for and combined Windows and Linux authentication events.
- Identified five events in the investigation:
  - Four Linux events.
  - One Windows event.
- Verified the results in Splunk.

**2. Authentication Origin Classification — Step 27**

- Classified authentication events according to their originating platform and location.
- Linux events were associated with the local Linux terminal (`/dev/pts/0`).
- The Windows event was associated with localhost (`127.0.0.1`).
- Verified the classification results in Splunk.

**3. Failed-Login Timing Analysis — Step 28.35**

- Analyzed five failed-login events recorded on November 9, 2026.
- Identified two rapid attempts.
- Rapid attempts represented 40% of the five events.

**4. Failed-Login Attempt Categorization — Steps 28.36–28.37**

- Categorized failed-login events according to the time between consecutive events on the same platform.
- Used a 60-second threshold to distinguish rapid attempts from longer intervals.
- Verified the category counts separately by platform and across both platforms.

Verified combined results:

| Attempt Category | Number of Events |
|---|---:|
| First event | 2 |
| Rapid attempts | 2 |
| Longer interval | 1 |
| **Total** | **5** |

**5. Linux Rapid Authentication Investigation — Steps 28.38–28.42**

Inspected the two Linux rapid failed-authentication events and examined their timing.

Verified findings:

- Host: `kali`
- User: `kali`
- Authentication mechanism: `sudo` / PAM
- Terminal: `/dev/pts/0`
- No remote host was identified in the inspected messages.
- Two rapid failures occurred at:
  - `16:01:06.452`
  - `16:01:16.910`

Timing analysis:

| Measurement | Verified Result |
|---|---:|
| Number of rapid events | 2 |
| Average gap | 18.40 seconds |
| Shortest gap | 10.46 seconds |
| Longest gap | 26.33 seconds |
| First-to-last event span | 10.46 seconds |

**6. Platform-Specific Rapid Attempt Analysis — Steps 28.43–28.45**

Calculated the percentage of rapid attempts across all events and separately for each platform.

Verified results:

| Platform | Total Events | Rapid Attempts | Rapid Percentage |
|---|---:|---:|---:|
| Linux | 4 | 2 | 50.00% |
| Windows | 1 | 0 | 0.00% |
| **Combined** | **5** | **2** | **40.00%** |

**7. Final Authentication Category Summary — Steps 28.46–28.47**

Completed the final category-count analysis by platform and across both operating systems.

Platform breakdown:

| Platform | First Event | Longer Interval | Rapid Attempts | Total |
|---|---:|---:|---:|---:|
| Linux | 1 | 1 | 2 | 4 |
| Windows | 1 | 0 | 0 | 1 |
| **Combined** | **2** | **1** | **2** | **5** |

All five events were accounted for in the final classification.

### Key Learning Outcomes

- Searching and correlating Windows and Linux authentication events in Splunk.
- Identifying the originating platform for authentication activity.
- Analyzing failed-login event timestamps.
- Using SPL commands such as `eval`, `case`, `sort`, `streamstats`, and `stats`.
- Calculating time differences between authentication events.
- Calculating averages, minimums, maximums, and percentages.
- Categorizing authentication activity using time-based thresholds.
- Investigating local Linux sudo/PAM authentication failures.
- Comparing authentication patterns across multiple operating systems.
- Understanding how event patterns can help an analyst investigate suspicious authentication activity.

**Important:** Rapid failed-login attempts are indicators worth investigating, but they do not independently prove that a brute-force attack occurred. The 60-second threshold was used as a triage guideline, not a definitive security detection rule.

### Project Status

**Project 04 investigation and analysis: Completed through Step 28.47.**

Steps 1–28.47 have been completed and verified according to the available project records and investigation results. Query 92 in the original query document duplicates Query 91, which was already completed as Step 28.47.

The final project documentation and README updates remain part of the wrap-up process.
