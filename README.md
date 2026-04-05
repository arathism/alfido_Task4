# Incident Response Simulation Report

### Task 4 – Windows Event Log Analysis

**Author:** Arathi Shekhar Munavalli **Internship:** Alfido Tech **Date:** 05 April 2026

---

## Overview

As part of this task, Windows Security Event Logs were analyzed using the built-in Windows Event Viewer on a Windows 11 machine. The goal was to identify suspicious logon patterns, detect privilege escalation, investigate group enumeration activity, and document appropriate incident response actions.

- **Log Source:** Windows Event Viewer → Windows Logs → Security
- **Total Events in Log:** 28,127
- **Events Analyzed:** Event ID 4624, 4672, 4798
- **Analysis Period:** 05-04-2026, 14:53 – 16:18
- **Tool:** Windows Event Viewer (Built-in)

---

## Tools Utilized

- **Windows Event Viewer** – for filtering, viewing, and analyzing Security Event Logs
- **Windows 11** – operating system where the log analysis was performed

---

## Approach Followed

The task was completed in four phases:

1. **Log Access**
   Opened Windows Event Viewer and navigated to Windows Logs → Security containing 28,127 total events.

2. **Event Filtering**
   Applied filters individually for Event IDs 4624, 4672, and 4798 to isolate relevant activity.

3. **Pattern Analysis**
   Recorded event counts, timestamps, burst clusters, and frequency patterns to detect anomalies.

4. **IOC Documentation**
   Identified Indicators of Compromise, documented findings, and prepared this report.

---

## Filters Applied & Results

| Event ID | Event Name | Count | Category | Description |
|----------|------------|-------|----------|-------------|
| `4624` | Successful Logon | 587 (2.1%) | Logon | Account successfully logged on to the system |
| `4672` | Special Privileges Assigned | 558 (2.0%) | Special Logon | Sensitive privileges assigned to new logon session |
| `4798` | Group Membership Enumerated | 3,085 (10.9%) | User Acct Mgmt | Process enumerated user's local group memberships |

---

## Key Findings

### Event ID 4624 — Successful Account Logon (587 events)
- Multiple logon events recorded at **1–2 second intervals** (e.g., 15:55:32, 15:55:33, 15:55:35)
- Rapid succession is atypical for normal interactive user sessions
- Strongly suggests **scripted or automated logon activity**

### Event ID 4672 — Special Privileges Assigned (558 events)
- Sensitive privileges (SeDebugPrivilege, SeTcbPrivilege, SeBackupPrivilege) assigned at nearly every logon
- 558 events closely mirror the 4624 logon timestamps
- Indicates **persistent administrative or SYSTEM-level access** at each session

### Event ID 4798 — User Group Membership Enumerated (3,085 events)
- Count of 3,085 is **2.7× higher** than combined logon events (1,145)
- Burst clusters observed at **16:10:32, 16:11:44, 16:14:32, 16:16:32** — multiple events per second
- Pattern strongly consistent with **automated reconnaissance or bulk group enumeration scripts**

---

## Anomalies Detected

- Rapid Successive Logons (1-sec intervals at 15:55:32, :33, :35) — Event ID 4624, 4672
- Excessive Group Enumeration (3,085 vs 587 logons — 5:1 ratio is abnormal) — Event ID 4798
- Burst Cluster Pattern (multi-events per second at 16:10, 16:11, 16:14, 16:16) — Event ID 4798
- Continuous Privilege Assignment (558 special privilege events matching logon volume) — Event ID 4672

---

## Risk Evaluation

- **High Risk:** Excessive Group Enumeration, Burst Cluster Pattern
- **Medium Risk:** Rapid Successive Logons, Continuous Privilege Assignment
- **Low Risk:** None detected
- **Info:** Normal SYSTEM logon sessions

---

## Indicators of Compromise (IOCs)

- Volume Anomaly — 3,085 × Event ID 4798 — excessive group enumeration, 5× higher than logon count
- Temporal Pattern — Burst clusters at 16:10–16:16 — multiple events per second in tight repeated intervals
- Privilege Anomaly — 558 × Event ID 4672 — special privileges assigned at every logon (persistent admin access)
- Behavioral Pattern — Rapid successive 4624 events at 1-sec intervals — indicates scripted/automated logon

---

## Recommendations

- Correlate 4798 burst events with running processes using **Process Monitor** to identify source application
- Inspect **scheduled tasks and startup scripts** for automated logon sequences
- Review all accounts holding **SeDebugPrivilege / SeTcbPrivilege** — restrict to minimum necessary
- Enable **Event ID 4688** (Process Creation) audit policy to trace which process triggered the 4798 spikes
- Deploy **Sysmon** with a community ruleset for deeper endpoint process and network visibility
- If malicious activity confirmed — **isolate endpoint immediately** and preserve the `.evtx` log as forensic evidence
- Configure **SIEM alerting rules** for bulk 4798 events exceeding 100 occurrences per minute

---

## Files in this Repository

| File | Description |
|------|-------------|
| `Task4_Incident_Response_Report.pdf` | Full incident response report with Event Viewer screenshots |
| `Event_4624_Logon.png` | Event ID 4624 filtered view — 587 Successful Logon events |
| `Event_4672_Privileges.png` | Event ID 4672 filtered view — 558 Special Logon events |
| `Event_4798_Enumeration.png` | Event ID 4798 filtered view — 3,085 Group Enumeration events |

---

## Conclusion

The Windows Security Event Log analysis conducted on 05-04-2026 revealed significant anomalies across three event categories. While Event IDs 4624 and 4672 volumes are within plausible ranges for an active workstation, the 3,085 occurrences of Event ID 4798 in tight burst clusters represent a strong indicator of automated group enumeration — a technique commonly associated with reconnaissance during lateral movement or credential harvesting attacks. No direct evidence of a confirmed breach was found through log analysis alone. However, the observed patterns are sufficient to warrant a deeper forensic investigation, including process-level analysis and network log correlation. Regular event log analysis is an essential skill for incident response and early threat detection in real environments.

---

## Disclaimer

All log analysis was performed on the analyst's own Windows 11 machine for learning purposes only, as part of the Alfido Tech Cybersecurity Internship program.
