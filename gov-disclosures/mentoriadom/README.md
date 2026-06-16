# mentoriadom Admin Panel

**Organization:** Base44 Platform (mentoriadom.app)  
**Vulnerability Type:** SPA Admin Sitemap Visible  
**Discovery Date:** [DD/MM/YYYY]  
**Status:** 🟡 **Uncertain**  
**Verification Date:** 16/06/2026  
**CERT.br Ticket:** [REDACTED]  
**CTIR Gov Ticket:** [REDACTED]

## Summary

Single Page Application admin routes visible in sitemap/JS bundles. Access control uncertain.

## Details

- **Asset:** [REDACTED]
- **Finding:** Admin panel routes (`/admin/*`, `/dashboard/*`) discoverable in client-side bundles
- **Risk:** Potential auth bypass if backend doesn't enforce authorization
- **Current Status:** Uncertain — cannot verify if backend enforces auth (verified 16/06/2026)

## Timeline

- **Reported:** [DD/MM/YYYY] via CERT.br + CTIR Gov
- **Acknowledged:** [DD/MM/YYYY]
- **Follow-up:** [DD/MM/YYYY] — pending verification
- **Last Verified:** 16/06/2026 — uncertain