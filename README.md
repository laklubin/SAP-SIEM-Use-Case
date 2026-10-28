# SAP SIEM Detection Use Cases

SAP-specific security monitoring use cases for a SIEM. Every entry pairs three things:

- **Use case name** — what you are detecting and why it matters
- **Log source** — the SAP log that actually carries the event
- **Sample log** — a representative, normalised log line to build a parser or rule against

Each entry also carries a category, a severity rating, starting-point detection logic (fields, thresholds, correlations) and a hand-mapped [MITRE ATT&CK](https://attack.mitre.org) Enterprise technique reference.

## Coverage

| SAP layer | What it covers |
|---|---|
| **ABAP Application** | NetWeaver ABAP, ECC and S/4HANA — logon abuse, authorizations, user administration, critical transactions, debugging and code change, transports, downloads, table manipulation, audit tampering |
| **Infrastructure & Integration** | SAP gateway, SAProuter, message server, ICM / Web Dispatcher, PI/PO, OS-level access to SAP hosts, secure store, UCON |
| **HANA Database** | HANA audit trail — SYSTEM logon, privilege grants, direct schema access, audit policy changes, backup deletion, SQL injection |
| **BTP & Cloud** | BTP audit log service, Cloud Identity Services (IAS), Cloud Integration (CPI), SuccessFactors, Ariba |
| **Business Process** | Segregation-of-duties and fraud detections that only exist inside SAP — vendor and employee bank-detail changes, payment runs, three-way match, posting periods, credit limits |

Log sources used across the library include the SAP Security Audit Log (SM20 / RSAU_READ_LOG), System Log (SM21), change documents (CDHDR/CDPOS), table change logs (DBTABLOG), Read Access Logging, gateway and SAProuter logs, CTS/transport logs, job and spool logs, the HANA audit trail, BTP and IAS audit logs, OData/Gateway logs, UCON logs, GRC Emergency Access logs, and host OS logs.

## Files

- `index.html` — the published, filterable use case library (GitHub Pages)
- `usecases.json` — the structured dataset behind it

Live site: https://laklubin.github.io/SAP-SIEM-Use-Case/

## Data model

```json
{
  "id": "SAP-UC-001",
  "name": "Brute-force logon attempts against SAP dialog users",
  "description": "…what the use case detects and why it matters",
  "domain": "ABAP Application",
  "categories": ["Credential Access & Logon Abuse", "Discovery & Reconnaissance"],
  "severity": "High",
  "log_sources": ["SAP Security Audit Log (SM20 / RSAU_READ_LOG)"],
  "sample_log": "2026-10-27T14:22:31Z host=sapprd01 sid=PRD … msgid=AU2 …",
  "detection": "msgid=AU2 grouped by source IP: >= 10 events in 5 min …",
  "attack": ["T1110 Brute Force", "T1110.003 Password Spraying"]
}
```

## Important caveats

**Sample logs are representative, not verbatim.** They are synthetic, written in the shape the event takes once a SIEM has collected it, and contain no real system, user, IP, bank account or company. Real field names, delimiters and message text vary by SAP release, kernel patch level, audit configuration and collection method — the same Security Audit Log record looks different read through RSAU_READ_LOG, exported from SM20, pulled over XAL/RFC, forwarded by SAP Enterprise Threat Detection, or shipped by a log agent. Use these for normalisation and parser development, and validate against your own systems before promoting anything to an alert.

**Prerequisites matter more than the rules.** Most of this library is unreachable unless the Security Audit Log is configured and active (RSAU_CONFIG, formerly SM19), table change logging is enabled (`rec/client` plus per-table logging in SE13), Read Access Logging is configured for the fields you care about, HANA audit policies exist, and BTP / IAS audit log retrieval is wired up.

**Thresholds are starting points.** Every count and time window in the detection logic needs baselining per landscape. Interface accounts, batch users and migration windows generate volumes that would be alarming from a dialog user.

Compiled by LK as a detection-engineering reference for SAP security monitoring.
