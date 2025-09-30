# Instructions — Lab 5 — RESTCONF vs NETCONF on Catalyst 8k

> **Before you begin:** Open the dev container, confirm outbound network access, and note the sandbox hostname/IP plus RESTCONF and NETCONF ports from DevNet. Create `logs/`, `data/raw/`, and `data/reports/` if missing.


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


## Submission Checklist
- [ ] All three scripts exist in `src/` and run without tracebacks.
- [ ] `data/raw/restconf_system.json` and `data/raw/netconf_system.xml` are present and non-empty.
- [ ] `data/reports/system_report.txt` summarizes hostname, model, and version with method labels.
- [ ] `logs/restconf_system_info.log` includes RESTCONF CONNECT_OK, CMD=GET, and RAW_SAVED markers.
- [ ] `logs/netconf_system_info.log` includes NETCONF CONNECT_OK, RPC marker, and RAW_SAVED markers.
- [ ] `logs/compare_outputs.log` shows PARSE_JSON_OK, PARSE_XML_OK, and COMPARE_OK.
- [ ] `logs/devcontainer_health.log` shows DNS_OK, NET_OK, HEALTH_OVERALL=True.
- [ ] `logs/DEVCONTAINER_STATUS.txt` shows Overall status: READY.
