# Instructions — Lab 5 — RESTCONF vs NETCONF on Catalyst 8k

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


> **Before you begin:** Open the dev container, confirm outbound network access, and note the sandbox hostname/IP plus RESTCONF and NETCONF ports from DevNet. Create `logs/`, `data/raw/`, and `data/reports/` if missing.


## Resources
- [Cisco DevNet Always-On Sandbox (IOS-XE/C8Kv)](https://developer.cisco.com/site/sandbox/)- [Requests (Python)](https://requests.readthedocs.io/en/latest/)- [ncclient (Python NETCONF)](https://ncclient.readthedocs.io/)- [RESTCONF (concepts)](https://www.rfc-editor.org/rfc/rfc8040)- [NETCONF (concepts)](https://www.rfc-editor.org/rfc/rfc6241)
## Deliverables
- Standardized README with goals, overview, resources, grading, and tips.
- Stepwise INSTRUCTIONS for RESTCONF + NETCONF + compare; logs + artifacts saved.
- Grading: **75 points**

Follow these steps in order.

> **Logging Requirement:** Write progress to `logs/*.log` as you complete each step.

## Step 1 — Clone the Repository
**Goal:** Get your Classroom repo locally.

**What to do:**  
Clone and `cd` into the repo. Confirm `src/`, `data/`, and `logs/` exist (create if needed).


**You’re done when:**  
You see the three folders; Git is tracking changes.

**Log marker to add:**  
`[LAB5_START]`

## Step 2 — Open a Dev Container
**Goal:** Use the standardized environment.

**What to do:**  
Reopen in container; wait for dependencies. Verify `python --version`. Write a banner to `logs/DEVCONTAINER_STATUS.txt`.


**You’re done when:**  
You log `[STEP 2] Dev Container Started` and `DEVCONTAINER_OK`.

**Log marker to add:**  
`[[STEP 2] Dev Container Started, DEVCONTAINER_OK]`

## Step 3 — Confirm Sandbox Details
**Goal:** Identify target host/ports and basic reachability.

**What to do:**  
From container, ping the sandbox host and note RESTCONF and NETCONF ports from DevNet page.


**You’re done when:**  
You log sandbox reachability in `logs/devcontainer_health.log`.

**Log marker to add:**  
`[LAB5_HEALTH_START, DNS_OK, NET_OK, LAB5_HEALTH_END]`

## Step 4 — Review API Docs
**Goal:** Pick the exact RESTCONF endpoint and NETCONF RPC for system/platform info.

**What to do:**  
Open vendor docs; record path/RPC and any auth/header requirements.

**You’re done when:**  
You log a line stating the chosen endpoint/RPC.

**Log marker to add:**  
`[DOCS_REVIEWED]`

## Step 5 — RESTCONF — system/platform info
**Goal:** Fetch JSON with Python `requests`.

**What to do:**  
Implement `src/restconf_system_info.py` using HTTP Basic Auth and an Accept header for YANG/JSON.
Save raw to `data/raw/restconf_system.json`.


**You’re done when:**  
Status shows OK; JSON saved.

**Log marker to add:**  
`[CONNECT_OK: RESTCONF, CMD=GET system, RAW_SAVED=restconf_system.json, PARSE_JSON_OK]`

## Step 6 — NETCONF — same info
**Goal:** Fetch XML with `ncclient`.

**What to do:**  
Implement `src/netconf_system_info.py` connecting to NETCONF port; issue the RPC for system/platform info.
Save raw to `data/raw/netconf_system.xml`.


**You’re done when:**  
Session succeeds; XML saved.

**Log marker to add:**  
`[CONNECT_OK: NETCONF, RPC=system info, RAW_SAVED=netconf_system.xml, PARSE_XML_OK]`

## Step 7 — Compare outputs
**Goal:** Verify fields match and note differences.

**What to do:**  
Implement `src/compare_outputs.py` to load both artifacts, extract model/version (and hostname if available),
print/compare, and append `data/reports/system_report.txt`.


**You’re done when:**  
Comparison shown; report updated.

**Log marker to add:**  
`[COMPARE_OK, REPORT_SAVED=system_report.txt]`

## Step 8 — Commit, push, submit
**Goal:** Hand in cleanly.

**What to do:**  
Commit artifacts and logs; push; open a PR to `main`.

**You’re done when:**  
PR opens and CI is green.

**Log marker to add:**  
`[LAB5_END]`


## FAQ
**Q:** RESTCONF returns HTML instead of JSON—why?  
**A:** Set an Accept header for YANG/JSON (e.g., `application/yang-data+json`) and use the exact RESTCONF path.

**Q:** NETCONF fails with SSH/EOF errors.  
**A:** Use the NETCONF port (often 830), correct credentials, and `hostkey_verify=False` for the sandbox.

**Q:** Do I need YANG files locally?  
**A:** Not for these read-only queries; you just need the correct endpoint/RPC names that map to YANG.


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

## Autograder Notes
- Log file: `logs/*.log`
- Required markers: `LAB5_START`, `[STEP 2] Dev Container Started`, `DEVCONTAINER_OK`, `PKG_OK: requests`, `PKG_OK: ncclient`, `CONNECT_OK: RESTCONF`, `CONNECT_OK: NETCONF`, `CMD=GET system`, `RPC=system info`, `RAW_SAVED=restconf_system.json`, `RAW_SAVED=netconf_system.xml`, `PARSE_JSON_OK`, `PARSE_XML_OK`, `COMPARE_OK`, `REPORT_SAVED=system_report.txt`, `LAB5_HEALTH_START`, `DNS_OK`, `NET_OK`, `HEALTH_OVERALL=True`, `LAB5_HEALTH_END`, `LAB5_END`

## Submission Checklist
- [ ] All three scripts exist in `src/` and run without tracebacks.
- [ ] `data/raw/restconf_system.json` and `data/raw/netconf_system.xml` are present and non-empty.
- [ ] `data/reports/system_report.txt` summarizes hostname, model, and version with method labels.
- [ ] `logs/restconf_system_info.log` includes RESTCONF CONNECT_OK, CMD=GET, and RAW_SAVED markers.
- [ ] `logs/netconf_system_info.log` includes NETCONF CONNECT_OK, RPC marker, and RAW_SAVED markers.
- [ ] `logs/compare_outputs.log` shows PARSE_JSON_OK, PARSE_XML_OK, and COMPARE_OK.
- [ ] `logs/devcontainer_health.log` shows DNS_OK, NET_OK, HEALTH_OVERALL=True.
- [ ] `logs/DEVCONTAINER_STATUS.txt` shows Overall status: READY.
