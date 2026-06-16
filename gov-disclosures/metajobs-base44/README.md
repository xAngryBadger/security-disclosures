# metajobs.base44.app Candidate PII

**Organization:** Base44 Platform (metajobs.app)  
**Vulnerability Type:** Missing Authentication (CWE-306)  
**Discovery Date:** [DD/MM/YYYY]  
**Status:** 🟢 **Fixed** — 403 Forbidden  
**Verification Date:** 16/06/2026  
**CERT.br Ticket:** [REDACTED]  
**CTIR Gov Ticket:** [REDACTED]

## Summary

Candidate PII exposed without authentication on Base44-hosted application. Fixed with auth enforcement.

## Details

- **Asset:** https://metajobs.base44.app
- **Exposed Data:** Candidate names, emails, phone numbers, resumes
- **Root Cause:** Base44 platform default configuration lacks auth on public pages
- **Fix Verified:** 403 Forbidden / authentication required

## Timeline

- **Reported:** [DD/MM/YYYY] via CERT.br + CTIR Gov
- **Acknowledged:** [DD/MM/YYYY]
- **Fixed:** [DD/MM/YYYY]
- **Verified:** 16/06/2026