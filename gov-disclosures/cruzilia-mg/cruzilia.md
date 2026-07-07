# Cruzília / MG — Salary Transparency Portal Excessive Exposure (LGPD)

**Organization:** Prefeitura Municipal de Cruzília / MG  
**Vulnerability Type:** Excessive Personal Data Exposure in Transparency Portal (LGPD Art. 5, 11, 46) + Unauthenticated API  
**Discovery Date:** 25/06/2026  
**Status:** 🔴 **Still open**  
**Verification Date:** 25/06/2026  
**CERT.br Ticket:**  
**CTIR Gov Ticket:**  

## Summary

Transparency portal at `https://cruzilia.mg.gov.br/doc/?p=salarios` exposes complete payroll data for all municipal servants (active, inactive, temporary) including full names, birth dates, gross/net salaries, deductions, position, workload, admission date — with bulk XLS downloads for 14+ months and an unauthenticated JSON API (`?format=json`) enabling mass scraping.

## Details

- **Asset:** `https://cruzilia.mg.gov.br/doc/?p=salarios`
- **API Endpoint:** `https://cruzilia.mg.gov.br/doc/?format=json`
- **Bulk Downloads:** 14 XLS files (2024-11 through 2025-12, 216–399KB each)

### Exposed Data Fields (per record):
- **NOME** — Full name
- **DATA DE NASCIMENTO** — Birth date (day/month)
- **ENTIDADE / ÓRGÃO** — Department
- **SITUAÇÃO DO SERVIDOR** — Active/Inactive/Temporary
- **NOME DO CARGO / FUNÇÃO** — Position title
- **TIPO DO CARGO** — CEF (efetivo) / STP (temporário)
- **MÊS REFERÊNCIA** — Payroll month
- **DATA DE INGRESSO** — Admission date
- **CARGA HORÁRIA** — Weekly hours
- **TIPO DE PAGAMENTO** — Mensal
- **RENDIMENTOS BRUTO** — Gross salary
- **DESCONTOS** — Deductions
- **RENDIMENTOS LÍQUIDOS** — Net salary
- **TETO REMUNERATÓRIO** — Salary ceiling (R$ 14.000,00)

### Sample Record Count:
~200+ servants per month × 14 months = **~2,800+ records** with full PII + financial data

### LGPD Violations:
| Art. | Violation |
|------|-----------|
| **Art. 5º** (Finalidade, Necessidade, Minimização) | Birth dates + full names + exact salaries exceed transparency purpose |
| **Art. 6º** (Base legal) | LAI 12.527/11 exige *acesso individualizado*, não *dump em massa* |
| **Art. 7º** (Consentimento/Interesse público) | Interesse público não justifica exposição de data de nascimento + valor líquido nominal |
| **Art. 11** (Dados sensíveis) | Vida financeira + dados biométricos implícitos (data nascimento) |
| **Art. 46** (Segurança) | Zero controle de acesso, rate limiting, ou autenticação no JSON API |
| **Art. 48** (Comunicação de incidente) | Exposição contínua desde 2024 sem notificação |

### CNPD/ANPD Precedents:
- ANPD: "Transparência não autoriza exposição de dados excessivos" (Guia de Transparência, 2022)
- TCU: Portal deve permitir busca nominal, não download irrestrito de base completa
- TCEs: Data de nascimento + salário nominal = risco de fraude previdenciária/bancária

## Evidence Verified

- Web table renders 50+ records with all fields visible
- XLS downloads confirmed: `2024-dezembro.xls` (376KB), `2025-05-maio.xls` (220KB), etc.
- JSON API returns full dataset without authentication or rate limiting
- Birth dates (day/month) + names enable re-identification in external bases
- Gross/net values expose exact financial capacity per individual

## Timeline

- **Reported:** [DD/MM/YYYY] via CERT.br + CTIR Gov
- **Acknowledged:**
- **Fixed:**
- **Verified:**