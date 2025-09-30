# Lab 5 — RESTCONF vs NETCONF on Catalyst 8k

**Course:** Software Defined Networking  
**Module:** Network Automation Fundamentals • **Lab #:** 5  
**Estimated Time:** 1.5–2 hours

## Objectives
- Read Catalyst 8k API docs and identify valid RESTCONF endpoints.
- Use Python `requests` to call RESTCONF and retrieve system/platform details.
- Explain the role of YANG models for device data.
- Use Python `ncclient` to call NETCONF for the same info.
- Compare RESTCONF (HTTP/JSON) vs NETCONF (SSH/XML/RPC) outputs.
- Log clearly and save raw artifacts + a human-readable report.

## Prerequisites
- Python 3.11 (via the provided dev container)
- Accounts: GitHub
- Devices/Sandboxes: Cisco DevNet Always-On Sandbox: Catalyst 8000v (RESTCONF + NETCONF)

## Overview
Work directly with network device APIs. First, call a Catalyst 8k RESTCONF endpoint with Python `requests` to fetch system/platform information (e.g., model/version). Then perform the equivalent query with NETCONF via `ncclient`. Save raw JSON/XML, compare results, and generate a short report. Along the way, note where YANG models shape both interfaces.


## Resources
- [Cisco DevNet Always-On Sandbox (IOS-XE/C8Kv)](https://developer.cisco.com/site/sandbox/)- [Requests (Python)](https://requests.readthedocs.io/en/latest/)- [ncclient (Python NETCONF)](https://ncclient.readthedocs.io/)- [RESTCONF (concepts)](https://www.rfc-editor.org/rfc/rfc8040)- [NETCONF (concepts)](https://www.rfc-editor.org/rfc/rfc6241)

## FAQ
**Q:** RESTCONF returns HTML instead of JSON—why?  
**A:** Set an Accept header for YANG/JSON (e.g., `application/yang-data+json`) and use the exact RESTCONF path.

**Q:** NETCONF fails with SSH/EOF errors.  
**A:** Use the NETCONF port (often 830), correct credentials, and `hostkey_verify=False` for the sandbox.

**Q:** Do I need YANG files locally?  
**A:** Not for these read-only queries; you just need the correct endpoint/RPC names that map to YANG.



## Deliverables
- Standardized README with goals, overview, resources, grading, and tips.
- Stepwise INSTRUCTIONS for RESTCONF + NETCONF + compare; logs + artifacts saved.
- Grading: **75 points**

## Grading Breakdown
| Step | Requirement | Points |
|---|---|---|
| 1 | Repo cloned and structure verified | 5 |
| 2 | Dev container opened; dependencies OK | 5 |
| 3 | Sandbox reachable (ping/ports) | 5 |
| 4 | API docs reviewed; endpoint/RPC chosen | 5 |
| 5 | RESTCONF script runs, retrieves JSON | 10 |
| 6 | RESTCONF raw saved + log markers present | 5 |
| 7 | NETCONF script runs, retrieves XML | 10 |
| 8 | NETCONF raw saved + log markers present | 5 |
| 9 | Compare script parses both and matches key fields | 10 |
| 10 | Final report generated in `data/reports/system_report.txt` | 10 |
| 10b | All deliverables pushed; logs complete | 5 |
| **Total** |  | **75** |

## 🔧 Troubleshooting & Pro Tips
**Got HTML from RESTCONF**  
*Symptom:* Device responded with a web page  
*Fix:* Add `headers={'Accept': 'application/yang-data+json'}` and verify the exact path against docs.

**Requests hangs**  
*Symptom:* No response on RESTCONF  
*Fix:* Pass `timeout=10` and handle `requests.Timeout`.

**Messy XML**  
*Symptom:* NETCONF output hard to read  
*Fix:* Use `xmltodict` or `xml.dom.minidom` to pretty-print.

**File not found**  
*Symptom:* Nothing written under `data/`  
*Fix:* Create directories and use correct relative paths from repo root.

**No points despite success**  
*Symptom:* Autograder says missing markers  
*Fix:* Ensure required log lines exactly match the tokens below.



## Autograder Notes
- Log file: `logs/*.log`
- Required markers: `LAB5_START`, `[STEP 2] Dev Container Started`, `DEVCONTAINER_OK`, `PKG_OK: requests`, `PKG_OK: ncclient`, `CONNECT_OK: RESTCONF`, `CONNECT_OK: NETCONF`, `CMD=GET system`, `RPC=system info`, `RAW_SAVED=restconf_system.json`, `RAW_SAVED=netconf_system.xml`, `PARSE_JSON_OK`, `PARSE_XML_OK`, `COMPARE_OK`, `REPORT_SAVED=system_report.txt`, `LAB5_HEALTH_START`, `DNS_OK`, `NET_OK`, `HEALTH_OVERALL=True`, `LAB5_HEALTH_END`, `LAB5_END`

## License
© 2025 Your Name — Classroom use.