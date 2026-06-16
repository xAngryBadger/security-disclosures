# Security Disclosures Portfolio

Responsible vulnerability disclosure reports — 20+ findings in Brazilian government infrastructure, 5 confirmed fixes verified by CERT.br/CTIR Gov.

## Verification (16/06/2026)

| Finding | Organization | Type | Status | Verified |
|---------|-------------|------|--------|----------|
| FNAS/MDS SIAFI financial data | Ministério do Desenvolvimento Social | Directory Listing (CWE-548) | 🟢 **Fixed** — WAF blocking | ✅ 16/06 |
| Ibiraçu/ES municipal portal | Prefeitura de Ibiraçu | DOM-XSS + Directory Listing | 🟢 **Fixed** — Ouvidoria confirmed | ✅ 16/06 |
| CRMV-RS payroll/ethics docs | Conselho Regional de Medicina Veterinária RS | Directory Listing | 🟢 **Fixed** — 403 Forbidden | ✅ 16/06 |
| CAU/SC payroll/curricula | Conselho de Arquitetura SC | Directory Listing + vulnerable plugin | 🟢 **Fixed** — 403 Forbidden | ✅ 16/06 |
| metajobs.base44.app candidate PII | Base44 platform | Missing Auth (CWE-306) | 🟢 **Fixed** — 403 Forbidden | ✅ 16/06 |
| gestorcontratospro.base44.app | Base44 platform | Missing Auth | 🟢 **Fixed** — Auth required | ✅ 16/06 |
| Mogi Guaçu/SP backup dump | Prefeitura de Mogi Guaçu | Backup exposure (SQL + VPN tar) | 🔴 **Still open** | ✅ 16/06 |
| CAU/SE payroll with CPFs | Conselho de Arquitetura SE | Directory Listing | 🔴 **Still open** | ✅ 16/06 |
| Ministério da Economia Painel | Ministério da Economia | Directory Listing + Apache EOL | 🔴 **Still open** | ✅ 16/06 |
| VALIPREV benefit processes | Instituto de Previdência Valinhos | Directory Listing | 🔴 **Still open** | ✅ 16/06 |
| educbarueri FUNDEB docs | Barueri/SP | Directory Listing | 🔴 **Still open** | ✅ 16/06 |
| SISPREV-Brodowski payroll | Brodowski/SP | Directory Listing (CPF exposure) | 🔴 **Still open** | ✅ 16/06 |
| Aroeira user data | Prorural PE | Directory Listing | 🔴 **Still open** | ✅ 16/06 |
| Campo Bom SAMU schedules | Campo Bom/RS | Directory Listing | 🔴 **Still open** | ✅ 16/06 |
| mentoriadom admin panel | Base44 platform | SPA admin sitemap visible | 🟡 Uncertain | ✅ 16/06 |
| SAF/SUS unauth-file-upload | Ministério da Saúde | Directory Listing | 🟡 Uncertain (timeout) | ✅ 16/06 |

## Methodology

| Phase | Description | Compliance |
|-------|-------------|------------|
| **Passive Recon** | Google Dorking, Shodan, certificate transparency, passive DNS. No active payloads. | NIST CSF `ID.AM-5` |
| **Exposure Verification** | Confirm unauthenticated access as any citizen would find it. | NIST CSF `PR.AC-1` |
| **Documentation** | Screenshots, HTTP headers, timestamps. Minimal PoC only. | LGPD Art. 46 §1º |
| **Secure Deletion** | `shred -u` on all PoC files after documentation. Zero retention. | LGPD Art. 6 III |
| **Multi-channel Notification** | CERT.br + CTIR Gov + direct contact simultaneously. | LGPD Art. 48, NIST `RS.AN-3` |
| **Follow-up** | Post-fix verification, re-contact if needed, documented closure. | NIST CSF `RC.CO-1` |

## Stack (Security)

| Category | Tools/Frameworks |
|----------|------------------|
| **Offensive Recon** | Google Dorking · OSINT · Nmap · Wireshark · Burp Suite · SNMP enumeration · WordPress audit |
| **Defensive** | LGPD compliance · WAF verification · TLP protocol · Responsible disclosure (CERT.br/CTIR) · CVE analysis |
| **Frameworks** | NIST CSF · OWASP Top 10 · CIS Controls (awareness) |

## Repository Structure

```
security-disclosures/
├── README.md                    # This index table
├── template/
│   └── disclosure-template.md   # Reusable template
├── CVE-2026-XXXXX/              # Per-CVE folder (when assigned)
│   ├── README.md                # Full technical writeup
│   ├── advisory.md              # Coordinated disclosure timeline
│   └── poc/                     # Redacted proof of concept
├── bug-bounty/
│   └── platform-name/
│       └── vulnerability.md
├── gov-disclosures/
│   ├── fnas-mds-siafi/          # FNAS/MDS - FIXED
│   ├── ibiraçu-es/              # Ibiraçu - FIXED
│   ├── crmv-rs/                 # CRMV-RS - FIXED
│   ├── cau-sc/                  # CAU/SC - FIXED
│   ├── metajobs-base44/         # metajobs - FIXED
│   ├── mogi-guacu-backup/       # Mogi Guaçu - STILL OPEN
│   ├── cau-se/                  # CAU/SE - STILL OPEN
│   ├── economia-painel/         # Ministério Economia - STILL OPEN
│   ├── valiprev/                # VALIPREV - STILL OPEN
│   ├── educbarueri/             # Barueri - STILL OPEN
│   ├── sisprev-brodowski/       # SISPREV-Brodowski - STILL OPEN
│   ├── aroeira/                 # Aroeira - STILL OPEN
│   ├── campo-bom/               # Campo Bom - STILL OPEN
│   ├── mentoriadom/             # mentoriadom - UNCERTAIN
│   └── saf-sus/                 # SAF/SUS - UNCERTAIN
└── base44-platform-analysis/
    └── systemic-auth-failure.md  # Cross-app pattern analysis
```

## Contact

All disclosures reported via CERT.br and CTIR Gov following LGPD Art. 48 and NIST CSF responsible disclosure practices.