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

**4. Failed-Login Attempt Categorization — Step 28.36**
- Categorized failed-login events according to the time between consecutive events on the same platform.
- Used a 60-second threshold to distinguish rapid attempts from longer intervals.

Verified results:

| Attempt Category | Number of Events |
|---|---:|
| First event | 2 |
| Rapid attempts | 2 |
| Longer interval | 1 |
| **Total** | **5** |

Platform breakdown:
- Linux: Four failed-login events.
- Windows: One failed-login event.

### Key Learning Outcomes

- Searching and correlating Windows and Linux authentication events in Splunk.
- Identifying the originating platform for authentication activity.
- Analyzing failed-login event timestamps.
- Using SPL commands such as `eval`, `sort`, `streamstats`, and `stats`.
- Categorizing authentication activity using time-based thresholds.
- Understanding how event patterns can help an analyst investigate suspicious authentication activity.

**Important:** Rapid failed-login attempts are indicators worth investigating, but they do not independently prove that a brute-force attack occurred.

### Project Status

Project 04 is in progress. Steps 1–28.36 have been completed and verified. Further investigation and documentation will be added as the project continues.
