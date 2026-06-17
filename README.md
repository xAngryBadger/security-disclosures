# Security Disclosures Portfolio

[![Findings](https://img.shields.io/badge/Findings-20+-456A4B?style=flat-square)]()
[![Fixed](https://img.shields.io/badge/Fixed-5-2EA043?style=flat-square)]()
[![Open](https://img.shields.io/badge/Open-9+-DA3633?style=flat-square)]()
[![Uncertain](https://img.shields.io/badge/Uncertain-2+-D29922?style=flat-square)]()

**🇧🇷 PT-BR** — Relatórios de divulgação responsável de vulnerabilidades — 20+ findings em infraestrutura governamental e setorial brasileira, 5 correções confirmadas e verificadas via CERT.br/CTIR Gov.

<details>
<summary><b>🇺🇸 English version</b></summary>

**🇺🇸 English** — Responsible vulnerability disclosure reports — 20+ findings in Brazilian government and sectoral infrastructure, 5 confirmed fixes verified via CERT.br/CTIR Gov.

</details>

---

## Verificação (16/06/2026)

**🇧🇷 PT-BR** — Tabela completa de todas as vulnerabilidades identificadas, com mapeamento para artigos da LGPD.

<details>
<summary><b>🇺🇸 English version</b></summary>

Complete table of all identified vulnerabilities, with LGPD article mapping.

</details>

| Finding | Organization | Type | Status | LGPD |
|---------|-------------|------|--------|------|
| FNAS/MDS SIAFI financial data | Ministério do Desenvolvimento Social | Directory Listing (CWE-548) | 🟢 Fixed — WAF blocking | Art. 5, Art. 48 |
| Ibiraçu/ES municipal portal | Prefeitura de Ibiraçu | DOM-XSS + Directory Listing | 🟢 Fixed — Ouvidoria confirmed | Art. 5, Art. 46 |
| CRMV-RS payroll/ethics docs | Conselho Regional de Medicina Veterinária RS | Directory Listing | 🟢 Fixed — 403 Forbidden | Art. 5, Art. 6 III |
| CAU/SC payroll/curricula | Conselho de Arquitetura SC | Directory Listing + vulnerable plugin | 🟢 Fixed — 403 Forbidden | Art. 5, Art. 6 III |
| metajobs.base44.app candidate PII | Base44 platform | Missing Auth (CWE-306) | 🟢 Fixed — 403 Forbidden | Art. 5, Art. 48 |
| gestorcontratospro.base44.app | Base44 platform | Missing Auth | 🟢 Fixed — Auth required | Art. 5, Art. 48 |
| gestorcontratospro school contracts | Base44 platform | School contract data (values, scope, duration) | 🟢 Fixed | Art. 5, Art. 48 |
| Mogi Guaçu/SP backup dump | Prefeitura de Mogi Guaçu | Backup exposure (SQL + VPN tar) | 🔴 Still open | Art. 5, Art. 6 III, Art. 143 |
| CAU/SE payroll with CPFs | Conselho de Arquitetura SE | Directory Listing | 🔴 Still open | Art. 5, Art. 6 III |
| Ministério da Economia Painel | Ministério da Economia | Directory Listing + Apache EOL | 🔴 Still open | Art. 46 §1º, Art. 48 |
| VALIPREV benefit processes | Instituto de Previdência Valinhos | Directory Listing | 🔴 Still open | Art. 5, Art. 6 III |
| educbarueri FUNDEB docs | Barueri/SP | Directory Listing | 🔴 Still open | Art. 5, Art. 6 III |
| SISPREV-Brodowski payroll | Brodowski/SP | Directory Listing (CPF exposure) | 🔴 Still open | Art. 5, Art. 6 III |
| Aroeira user data | Prorural PE | Directory Listing | 🔴 Still open | Art. 5 |
| Campo Bom SAMU schedules | Campo Bom/RS | Directory Listing | 🔴 Still open | Art. 5 (health operational) |
| e-SUS PEC health records | Mato Queimado/RS (177.10.85.168) | Missing Auth (CWE-306) | 🔴 Still open | Art. 5 (health data) |
| SNMP/MikroTik network topology | Piauí (201.71.219.73) | Information Disclosure (CWE-200) | 🔴 Still open | Art. 6 III, Art. 46 |
| VivaSUS/UFBA EOL nginx | UFBA Salvador/BA (200.128.103.103) | Known CVEs on EOL software | 🔴 Still open | Art. 46 §1º |
| cPanel/WHM Jequitaí/MG | 162.240.59.231 | Admin interface exposed (CWE-419) | 🔴 Still open | Art. 46 |
| cPanel/WHM Araras/SP | 187.9.42.28 | Admin interface exposed (CWE-419) | 🔴 Still open | Art. 46 |
| Araguainha/MT | Prefeitura de Araguainha | DOM-XSS + Dir Listing + WP enum + Java traces | 🔴 Still open | Art. 46, Art. 5 |
| Câmara de Mantena/MG | Câmara Municipal | Financial documents exposed | 🔴 Still open | Art. 5, Art. 6 III |
| PMPR police agent data | Polícia Militar do Paraná | Agent names + photos exposed | 🔴 Still open | Art. 5 (physical safety) |
| Ruralvaliprev/FUNDEB | Instituto de Previdência | Financial docs + beneficiary data | 🔴 Still open | Art. 5, Art. 6 III |
| SISPREV DB integration | — | DB integration files exposed | 🔴 Still open | Art. 6 III, Art. 46 |
| mentoriadom admin panel | Base44 platform | SPA admin sitemap visible | 🟡 Uncertain | Art. 46 |
| SAF/SUS unauth-file-upload | Ministério da Saúde | Directory Listing | 🟡 Uncertain (timeout) | Art. 6 III, Art. 46 |
| BairesDev credential mix-up | BairesDev (private sector) | Credential exposure | 🟡 Uncertain (resolved internally) | Art. 6 III |

---

## Timeline

**🇧🇷 PT-BR** — Cronologia das divulgações e verificações.

**Março 2026 — 1ª Onda de Divulgação**
- FNAS/MDS, Ibiraçu, CRMV-RS, CAU/SC, CAU/SE, Mogi Guaçu, VALIPREV, educbarueri, SISPREV-Brodowski, Aroeira, Campo Bom, Araguainha, Câmara de Mantena, PMPR, SAF/SUS, Ruralvaliprev, SISPREV integration, Ministério da Economia
- Reportado via CERT.br + CTIR Gov simultaneamente

**Abril 2026 — Verificação**
- Ibiraçu: FIXED (Ouvidoria confirmed)
- CRMV-RS: FIXED (403)
- CAU/SC: FIXED (403)
- BairesDev: internally resolved

**Maio 2026 — 2ª Onda (Infraestrutura Pública)**
- e-SUS PEC (health records), SNMP/MikroTik, VivaSUS/UFBA, cPanel/WHM (2 instances)
- Reportado via CERT.br/CTIR Gov (TLP:AMBER)

**Junho 2026 — 3ª Onda (Base44 Platform)**
- metajobs, gestorcontratospro, mentoriadom — systemic auth failure pattern
- metajobs + gestorcontratospro: FIXED
- FNAS/MDS: FIXED (WAF)
- mentoriadom: UNCERTAIN

**Junho 2026 — Verificação Geral (16/06)**
- Re-verificação completa de todos os findings

<details>
<summary><b>🇺🇸 English version</b></summary>

Chronological timeline of disclosures and verifications.

**March 2026 — 1st Disclosure Wave**
- FNAS/MDS, Ibiraçu, CRMV-RS, CAU/SC, CAU/SE, Mogi Guaçu, VALIPREV, educbarueri, SISPREV-Brodowski, Aroeira, Campo Bom, Araguainha, Câmara de Mantena, PMPR, SAF/SUS, Ruralvaliprev, SISPREV integration, Ministério da Economia
- Reported via CERT.br + CTIR Gov simultaneously

**April 2026 — Verification**
- Ibiraçu: FIXED (Ouvidoria confirmed)
- CRMV-RS: FIXED (403)
- CAU/SC: FIXED (403)
- BairesDev: internally resolved

**May 2026 — 2nd Wave (Public Infrastructure)**
- e-SUS PEC (health records), SNMP/MikroTik, VivaSUS/UFBA, cPanel/WHM (2 instances)
- Reported via CERT.br/CTIR Gov (TLP:AMBER)

**June 2026 — 3rd Wave (Base44 Platform)**
- metajobs, gestorcontratospro, mentoriadom — systemic auth failure pattern
- metajobs + gestorcontratospro: FIXED
- FNAS/MDS: FIXED (WAF)
- mentoriadom: UNCERTAIN

**June 2026 — Full Verification (16/06)**
- Full re-verification of all findings

</details>

---

## Base44 Platform — Análise Sistêmica

**🇧🇷 PT-BR** — Análise de múltiplas aplicações hospedadas na Base44 revela um padrão sistêmico de bypass de autenticação. A causa raiz é uma decisão arquitetural da plataforma: **páginas públicas são servidas sem autenticação por padrão**, exigindo opt-in explícito para proteção.

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

**Aplicações afetadas:**

| Aplicação | Status | Dados expostos |
|-----------|--------|----------------|
| `metajobs.base44.app` | 🟢 Fixed | PII de candidatos (nomes, e-mails, telefones, currículos) |
| `gestorcontratospro.base44.app` | 🟢 Fixed | Dados de contratos, PII de partes |
| `mentoriadom.base44.app` | 🟡 Uncertain | Rotas do painel admin visíveis |

**Vetor de ataque:**

```
Atacante → GET https://<app>.base44.app/api/candidates
         → Sem headers de auth
         → Retorna TODAS as rows (RLS bypassed por policy permissive)
         → Harvest completo de PII
```

Análise completa: [`base44-platform-analysis/systemic-auth-failure.md`](base44-platform-analysis/systemic-auth-failure.md)

<details>
<summary><b>🇺🇸 English version</b></summary>

Analysis of multiple Base44-hosted applications reveals a systemic authentication bypass pattern. The root cause is a platform-level architectural decision: **public pages are served without authentication by default**, requiring explicit opt-in for auth protection.

**Affected applications:**

| Application | Status | Data exposed |
|-------------|--------|--------------|
| `metajobs.base44.app` | 🟢 Fixed | Candidate PII (names, emails, phones, resumes) |
| `gestorcontratospro.base44.app` | 🟢 Fixed | Contract data, party PII |
| `mentoriadom.base44.app` | 🟡 Uncertain | Admin panel routes visible |

**Attack vector:**

```
Attacker → GET https://<app>.base44.app/api/candidates
         → No auth headers required
         → Returns ALL rows (RLS bypassed by permissive policy)
         → Full PII harvest
```

Full analysis: [`base44-platform-analysis/systemic-auth-failure.md`](base44-platform-analysis/systemic-auth-failure.md)

</details>

---

## Diferenciais

**🇧🇷 PT-BR** — Princípios que diferenciam esta abordagem de divulgação responsável.

| # | Diferencial | Descrição |
|---|---|---|
| 1 | **Zero Retention** | Todos os dados de PoC são deletados de forma segura após documentação (`shred -u`). Nenhum dado é retido. |
| 2 | **Notificação Multi-canal** | CERT.br + CTIR Gov + contato direto simultaneamente. Não apenas um canal. |
| 3 | **Conformidade com Protocolo TLP** | Todas as comunicações seguem classificação TLP:AMBER/TLP:GREEN. Nenhum detalhe de exploit público. |
| 4 | **Verificação Pós-Fix** | Toda correção é re-verificada. Status nunca é assumido. |
| 5 | **Recon Passivo Apenas** | Nenhuma exploração ativa. Findings são acessíveis como qualquer cidadão os encontraria. |
| 6 | **Análise Sistêmica de Padrões** | Vai além de findings individuais para identificar falhas em nível de plataforma (caso Base44). |

<details>
<summary><b>🇺🇸 English version</b></summary>

Principles that differentiate this responsible disclosure approach.

| # | Differentiator | Description |
|---|---|---|
| 1 | **Zero Retention** | All PoC data securely deleted after documentation (`shred -u`). No data kept. |
| 2 | **Multi-channel Notification** | CERT.br + CTIR Gov + direct contact simultaneously. Not just one channel. |
| 3 | **TLP Protocol Compliance** | All communications follow TLP:AMBER/TLP:GREEN classification. No public exploit details. |
| 4 | **Post-Fix Verification** | Every fix is re-verified. Status is never assumed. |
| 5 | **Passive Recon Only** | No active exploitation. Findings are accessible as any citizen would find them. |
| 6 | **Systemic Pattern Analysis** | Goes beyond individual findings to identify platform-level flaws (Base44 case). |

</details>

---

## Respostas CERT.br / CTIR Gov

**🇧🇷 PT-BR** — Todos os findings foram reportados através dos canais oficiais, conforme LGPD Art. 48.

| Autoridade | Resposta |
|-----------|----------|
| CERT.br | `"TLP:AMBER — Informações de uso restrito"` — classificação atribuída aos relatórios |
| CTIR Gov | `"CTIR Gov encaminhou contatos para a equipe responsável"` — encaminhamento confirmado |

Todos os findings reportados via CERT.br + CTIR Gov conforme LGPD Art. 48.

<details>
<summary><b>🇺🇸 English version</b></summary>

All findings were reported through official channels per LGPD Art. 48.

| Authority | Response |
|-----------|----------|
| CERT.br | `"TLP:AMBER — Informações de uso restrito"` — classification assigned to reports |
| CTIR Gov | `"CTIR Gov encaminhou contatos para a equipe responsável"` — forwarding confirmed |

All findings reported via CERT.br + CTIR Gov per LGPD Art. 48.

</details>

---

## Metodologia

**🇧🇷 PT-BR** — Metodologia de divulgação responsável, alinhada a NIST CSF e LGPD.

| Fase | Descrição | Conformidade |
|------|-----------|-------------|
| **Recon Passivo** | Google Dorking, Shodan, certificate transparency, DNS passivo. Sem payloads ativos. | NIST CSF `ID.AM-5` |
| **Verificação de Exposição** | Confirmar acesso não autenticado como qualquer cidadão encontraria. | NIST CSF `PR.AC-1` |
| **Documentação** | Screenshots, headers HTTP, timestamps. PoC mínimo apenas. | LGPD Art. 46 §1º |
| **Deleção Segura** | `shred -u` em todos os arquivos de PoC após documentação. Zero retenção. | LGPD Art. 6 III |
| **Notificação Multi-canal** | CERT.br + CTIR Gov + contato direto simultaneamente. | LGPD Art. 48, NIST `RS.AN-3` |
| **Follow-up** | Verificação pós-fix, re-contato se necessário, encerramento documentado. | NIST CSF `RC.CO-1` |

<details>
<summary><b>🇺🇸 English version</b></summary>

Responsible disclosure methodology, aligned with NIST CSF and LGPD.

| Phase | Description | Compliance |
|-------|-------------|------------|
| **Passive Recon** | Google Dorking, Shodan, certificate transparency, passive DNS. No active payloads. | NIST CSF `ID.AM-5` |
| **Exposure Verification** | Confirm unauthenticated access as any citizen would find it. | NIST CSF `PR.AC-1` |
| **Documentation** | Screenshots, HTTP headers, timestamps. Minimal PoC only. | LGPD Art. 46 §1º |
| **Secure Deletion** | `shred -u` on all PoC files after documentation. Zero retention. | LGPD Art. 6 III |
| **Multi-channel Notification** | CERT.br + CTIR Gov + direct contact simultaneously. | LGPD Art. 48, NIST `RS.AN-3` |
| **Follow-up** | Post-fix verification, re-contact if needed, documented closure. | NIST CSF `RC.CO-1` |

</details>

---

## Stack (Security)

**🇧🇷 PT-BR** — Ferramentas e frameworks utilizados.

| Categoria | Ferramentas/Frameworks |
|-----------|----------------------|
| **Recon Ofensivo** | Google Dorking · OSINT · Nmap · Wireshark · Burp Suite · SNMP enumeration · WordPress audit |
| **Defensivo** | Conformidade LGPD · verificação WAF · protocolo TLP · divulgação responsável (CERT.br/CTIR) · análise CVE |
| **Frameworks** | NIST CSF · OWASP Top 10 · CIS Controls (conscientização) |

<details>
<summary><b>🇺🇸 English version</b></summary>

Tools and frameworks used.

| Category | Tools/Frameworks |
|----------|------------------|
| **Offensive Recon** | Google Dorking · OSINT · Nmap · Wireshark · Burp Suite · SNMP enumeration · WordPress audit |
| **Defensive** | LGPD compliance · WAF verification · TLP protocol · Responsible disclosure (CERT.br/CTIR) · CVE analysis |
| **Frameworks** | NIST CSF · OWASP Top 10 · CIS Controls (awareness) |

</details>

---

## Estrutura do Repositório

**🇧🇷 PT-BR** — Organização dos diretórios.

```
security-disclosures/
├── README.md                    # Este índice
├── template/
│   └── disclosure-template.md   # Template reutilizável
├── CVE-2026-XXXXX/              # Pasta por CVE (quando atribuído)
│   ├── README.md                # Writeup técnico completo
│   ├── advisory.md              # Timeline de divulgação coordenada
│   └── poc/                     # Proof of concept redigido
├── bug-bounty/
│   └── platform-name/
│       └── vulnerability.md
├── gov-disclosures/
│   ├── fnas-mds-siafi/          # FNAS/MDS - FIXED
│   ├── ibiraçu-es/              # Ibiraçu - FIXED
│   ├── crmv-rs/                 # CRMV-RS - FIXED
│   ├── cau-sc/                  # CAU/SC - FIXED
│   ├── metajobs-base44/         # metajobs - FIXED
│   ├── gestorcontratospro/      # gestorcontratospro - FIXED
│   ├── mogi-guacu-backup/       # Mogi Guaçu - STILL OPEN
│   ├── cau-se/                  # CAU/SE - STILL OPEN
│   ├── economia-painel/         # Ministério Economia - STILL OPEN
│   ├── valiprev/                # VALIPREV - STILL OPEN
│   ├── educbarueri/             # Barueri - STILL OPEN
│   ├── sisprev-brodowski/       # SISPREV-Brodowski - STILL OPEN
│   ├── aroeira/                 # Aroeira - STILL OPEN
│   ├── campo-bom/               # Campo Bom - STILL OPEN
│   ├── esus-pec/                # e-SUS PEC - STILL OPEN
│   ├── snmp-mikrotik/           # SNMP/MikroTik - STILL OPEN
│   ├── vivasus-ufba/            # VivaSUS/UFBA - STILL OPEN
│   ├── cpanel-jequitai/         # cPanel/WHM Jequitaí - STILL OPEN
│   ├── cpanel-araras/           # cPanel/WHM Araras - STILL OPEN
│   ├── araguainha-mt/           # Araguainha - STILL OPEN
│   ├── camara-mantena/          # Câmara de Mantena - STILL OPEN
│   ├── pmpr/                    # PMPR - STILL OPEN
│   ├── ruralvaliprev-fundeb/    # Ruralvaliprev/FUNDEB - STILL OPEN
│   ├── sisprev-integration/     # SISPREV DB integration - STILL OPEN
│   ├── mentoriadom/             # mentoriadom - UNCERTAIN
│   ├── saf-sus/                 # SAF/SUS - UNCERTAIN
│   └── bairesdev/               # BairesDev - UNCERTAIN
└── base44-platform-analysis/
    └── systemic-auth-failure.md  # Análise de padrão cross-app
```

<details>
<summary><b>🇺🇸 English version</b></summary>

Repository directory organization.

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
│   ├── gestorcontratospro/      # gestorcontratospro - FIXED
│   ├── mogi-guacu-backup/       # Mogi Guaçu - STILL OPEN
│   ├── cau-se/                  # CAU/SE - STILL OPEN
│   ├── economia-painel/         # Ministério Economia - STILL OPEN
│   ├── valiprev/                # VALIPREV - STILL OPEN
│   ├── educbarueri/             # Barueri - STILL OPEN
│   ├── sisprev-brodowski/       # SISPREV-Brodowski - STILL OPEN
│   ├── aroeira/                 # Aroeira - STILL OPEN
│   ├── campo-bom/               # Campo Bom - STILL OPEN
│   ├── esus-pec/                # e-SUS PEC - STILL OPEN
│   ├── snmp-mikrotik/           # SNMP/MikroTik - STILL OPEN
│   ├── vivasus-ufba/            # VivaSUS/UFBA - STILL OPEN
│   ├── cpanel-jequitai/         # cPanel/WHM Jequitaí - STILL OPEN
│   ├── cpanel-araras/           # cPanel/WHM Araras - STILL OPEN
│   ├── araguainha-mt/           # Araguainha - STILL OPEN
│   ├── camara-mantena/          # Câmara de Mantena - STILL OPEN
│   ├── pmpr/                    # PMPR - STILL OPEN
│   ├── ruralvaliprev-fundeb/    # Ruralvaliprev/FUNDEB - STILL OPEN
│   ├── sisprev-integration/     # SISPREV DB integration - STILL OPEN
│   ├── mentoriadom/             # mentoriadom - UNCERTAIN
│   ├── saf-sus/                 # SAF/SUS - UNCERTAIN
│   └── bairesdev/               # BairesDev - UNCERTAIN
└── base44-platform-analysis/
    └── systemic-auth-failure.md  # Cross-app pattern analysis
```

</details>

---

## Contato

[![Email](https://img.shields.io/badge/Email-isaacnathandasilva-D4A017?style=flat-square&logo=gmail)](mailto:isaacnathandasilva@gmail.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Isaac%20Nathan-0A66C2?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/isaac-nathan-da-silva-barbosa-815b212ab/)
[![GitHub](https://img.shields.io/badge/GitHub-xAngryBadger-181717?style=flat-square&logo=github)](https://github.com/xAngryBadger)
[![Portfolio](https://img.shields.io/badge/Portfolio-Isaac%20Vitae-456A4B?style=flat-square)](https://xangrybadger.github.io/isaac-vitae/)

**🇧🇷 PT-BR** — Isaac Nathan da Silva Barbosa — Engenheiro de Computação. Todas as divulgações reportadas via CERT.br e CTIR Gov seguindo LGPD Art. 48.

<details>
<summary><b>🇺🇸 English version</b></summary>

**🇺🇸 English** — Isaac Nathan da Silva Barbosa — Computer Engineer. All disclosures reported via CERT.br and CTIR Gov following LGPD Art. 48.

</details>
