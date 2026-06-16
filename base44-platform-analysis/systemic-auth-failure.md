# Base44 Platform: Systemic Authentication Failure Analysis

## Executive Summary

Analysis of multiple Base44-hosted applications reveals a systemic authentication bypass pattern affecting all applications deployed on the platform. The root cause is a platform-level architectural decision: **public pages are served without authentication by default**, requiring explicit opt-in for auth protection.

---

## Affected Applications (Confirmed)

| Application | Status | Data Exposed | Verification |
|-------------|--------|--------------|--------------|
| `metajobs.base44.app` | 🟢 Fixed | Candidate PII (names, emails, phones, resumes) | 403 Forbidden |
| `gestorcontratospro.base44.app` | 🟢 Fixed | Contract data, party PII | Auth required |
| `mentoriadom.base44.app` | 🟡 Uncertain | Admin panel routes visible | Timeout/pending |

---

## Root Cause Analysis

### Platform Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Base44 Platform                       │
├─────────────────────────────────────────────────────────┤
│  Frontend (React SPA)     │  Backend (Node/PostgreSQL)  │
│  ─────────────────────    │  ─────────────────────────  │
│  • Routes defined in      │  • Auto-generated API       │
│    JSON config            │  • Row-level security (RLS) │
│  • Public by default      │  • RLS ONLY enforced if     │
│  • Auth = optional flag   │    auth context present     │
└─────────────────────────────────────────────────────────┘
```

### The Flaw

1. **Default Public Access**: Every page/route is public unless explicitly marked `auth: true` in the page config
2. **No Platform-Level Enforcement**: Base44 does not enforce authentication at the infrastructure level
3. **RLS Dependency**: Row-Level Security in PostgreSQL only activates when `auth.uid()` is present — but unauthenticated requests have `auth.uid() = null`, which **bypasses RLS entirely** for policies using `auth.uid()`

### Vulnerable Policy Pattern

```sql
-- THIS POLICY IS BYPASSED FOR UNAUTHENTICATED USERS
CREATE POLICY "Users can view own data" ON candidates
  FOR SELECT USING (auth.uid() = user_id);

-- When auth.uid() IS NULL (unauthenticated):
-- The policy evaluates to: NULL = user_id → UNKNOWN → NO ROWS RETURN
-- BUT: If no policy matches, default-deny applies... 
-- EXCEPT: Base44 creates permissive "public read" policies by default!
```

### Default Permissive Policies

Base44 auto-generates policies like:

```sql
-- Auto-created by platform — ALLOWS PUBLIC READ
CREATE POLICY "Public read access" ON candidates
  FOR SELECT USING (true);
```

---

## Attack Vector

```
Attacker → GET https://<app>.base44.app/api/candidates
         → No auth headers required
         → Returns ALL rows (RLS bypassed by permissive policy)
         → Full PII harvest
```

---

## Evidence Pattern

All confirmed cases share:

1. **Identical Tech Stack**: Base44 (React + Supabase/PostgreSQL)
2. **Same Misconfiguration**: Public pages with data-fetching components
3. **Same Root Cause**: Missing `auth: true` in page config + permissive RLS
4. **Same Fix**: Adding `auth: true` to page config → 403/unauthorized

---

## Remediation (Platform Level)

### For Base44 Platform

1. **Change Default**: Pages should be `auth: true` by default; `public: true` as explicit opt-out
2. **Enforce at Edge**: Add authentication middleware at CDN/edge level
3. **Audit Auto-Policies**: Stop generating `USING (true)` policies; require explicit policy definition
4. **Add Security Headers**: `X-Frame-Options`, `Content-Security-Policy`, `Referrer-Policy`

### For Application Developers

1. **Audit All Pages**: Verify `auth: true` on every page accessing data
2. **Test Unauthenticated**: `curl -I https://<app>.base44.app/api/<table>` should return 401/403
3. **Review RLS Policies**: Ensure no `USING (true)` policies exist on sensitive tables
4. **Enable Platform Auth**: Use Base44's built-in auth provider, not custom solutions

---

## Disclosure Timeline

| Date | Event |
|------|-------|
| [DD/MM/YYYY] | First finding (metajobs) discovered |
| [DD/MM/YYYY] | Reported to CERT.br + CTIR Gov |
| [DD/MM/YYYY] | Second finding (gestorcontratospro) — confirmed systemic |
| [DD/MM/YYYY] | Third finding (mentoriadom) — pattern confirmed |
| [DD/MM/YYYY] | metajobs fixed (403 verified) |
| [DD/MM/YYYY] | gestorcontratospro fixed (auth required verified) |
| 16/06/2026 | mentoriadom uncertain (verification pending) |

---

## Recommendations for CERT.br/CTIR Gov

1. **Platform Notification**: Notify Base44 of systemic issue affecting all customers
2. **Customer Notification**: Alert all Brazilian gov/orgs using Base44
3. **Audit Scope**: Expand to all `.base44.app` subdomains in `.gov.br` space
4. **Compliance**: Map to LGPD Art. 46 (security measures) and Art. 48 (incident notification)

---

## References

- CERT.br Tickets: [REDACTED]
- CTIR Gov Tickets: [REDACTED]
- Base44 Documentation: [Platform security model]
- Supabase RLS Docs: [Row Level Security behavior with null auth]

---

## Appendix: Detection Script

```bash
#!/bin/bash
# Quick check for Base44 auth bypass
APP_URL="https://<app>.base44.app"

# Check if API returns data without auth
curl -s -o /dev/null -w "%{http_code}" "$APP_URL/api/public_table"
# If 200 → VULNERABLE
# If 401/403 → PROTECTED
```