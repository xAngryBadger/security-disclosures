# Vulnerability Disclosure Template

## Metadata

| Field | Value |
|-------|-------|
| **Title** | [Short descriptive title] |
| **Organization** | [Target organization name] |
| **Asset** | [URL, IP, or system identifier] |
| **Vulnerability Type** | [CWE category, e.g., CWE-548 Directory Listing] |
| **Severity** | [Critical/High/Medium/Low] |
| **CVSS Score** | [If calculated, e.g., 7.5 (CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N)] |
| **Discovery Date** | [DD/MM/YYYY] |
| **Reported Date** | [DD/MM/YYYY] |
| **Status** | [Open / Fixed / Uncertain / Won't Fix] |
| **Verification Date** | [DD/MM/YYYY] |
| **CERT.br Ticket** | [Ticket number or "Pending"] |
| **CTIR Gov Ticket** | [Ticket number or "Pending"] |

---

## Executive Summary

[2-3 sentences describing what was found, the impact, and current status.]

---

## Technical Details

### Affected Component

- **URL/Endpoint:** `[exact path]`
- **Method:** `[GET/POST/etc.]`
- **Parameters:** `[if applicable]`

### Vulnerability Description

[Detailed technical explanation of the vulnerability. Include:
- Root cause
- Attack vector
- Prerequisites (if any)
- Evidence of exploitability]

### Proof of Concept (Redacted)

```
[Minimal PoC showing the issue — REDACT sensitive data]
```

**Note:** All PoC artifacts securely deleted after documentation (`shred -u`).

### Impact Assessment

| Impact Area | Rating | Details |
|-------------|--------|---------|
| **Confidentiality** | [High/Medium/Low/None] | [PII, credentials, financial data, etc.] |
| **Integrity** | [High/Medium/Low/None] | [Data modification potential] |
| **Availability** | [High/Medium/Low/None] | [Service disruption potential] |
| **Regulatory** | [LGPD/GDPR/Other] | [Applicable regulations] |

### Evidence

- [ ] Screenshots (redacted)
- [ ] HTTP headers
- [ ] Response bodies (redacted)
- [ ] Timestamps
- [ ] Network captures (if applicable)

---

## Disclosure Timeline

| Date | Event |
|------|-------|
| DD/MM/YYYY | Vulnerability discovered |
| DD/MM/YYYY | Initial report sent to CERT.br |
| DD/MM/YYYY | Initial report sent to CTIR Gov |
| DD/MM/YYYY | Direct contact attempted (if applicable) |
| DD/MM/YYYY | Acknowledgment received |
| DD/MM/YYYY | Fix deployed |
| DD/MM/YYYY | Fix verified |
| DD/MM/YYYY | Public disclosure (if applicable) |

---

## Remediation

### Recommended Fix

[Specific technical recommendations for the organization]

### Verification Steps

[Steps to verify the fix is effective]

---

## Compliance & Legal

- **LGPD Art. 48** — Security incident notification
- **LGPD Art. 46 §1º** — Security measures documentation
- **NIST CSF RS.AN-3** — Forensics evidence preservation
- **TLP Protocol** — [TLP:GREEN / TLP:AMBER / TLP:RED]

---

## References

- CERT.br Ticket: `[number]`
- CTIR Gov Ticket: `[number]`
- CVE: `[if assigned]`
- Related advisories: `[links]`

---

## Notes

[Any additional context, follow-up actions, or lessons learned]