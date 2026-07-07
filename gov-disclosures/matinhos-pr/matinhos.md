# Matinhos / PR — Patient Waitlists, Financial & HR Data Exposure

**Organization:** Prefeitura Municipal de Matinhos / PR (TI Matinhos)  
**Vulnerability Type:** Directory Listing (CWE-548) + Health Data Exposure (LGPD Art. 11) + Financial Data Exposure (LGPD Art. 11)  
**Discovery Date:** 25/06/2026  
**Status:** 🔴 **Still open**  
**Verification Date:** 25/06/2026  
**CERT.br Ticket:**  
**CTIR Gov Ticket:**  

## Summary

Directory listing enabled on `http://ti.matinhos.pr.gov.br/` exposing multiple sensitive directories: patient waitlists with Cartão SUS (CNS) numbers, banking statements, payroll/HR documents, licitation files (2.6G zip), CELEPAR backup directory, social benefits with CPF/CNPJ, and pension fund reports.

## Details

- **Asset:** `http://ti.matinhos.pr.gov.br/`
- **Exposed Directories & Data:**

  ### 1. Health Data — Patient Waitlists (CRÍTICO)
  - **Path:** `listas-dos-pacientes-que-aguardam-por-consultas-exames-e-intervencoes-cirurgicas/2019/[mês]/`
  - **Content:** 20+ PDFs per month (370–460KB each), one per medical specialty (oncologia, cardiologia, neurocirurgia, cirurgia vascular, ginecologia, oftalmologia, ortopedia, etc.)
  - **PII:** Patient names + **Cartão SUS (CNS)** numbers visible in scanned tables
  - **LGPD:** Art. 11 §1º (dado de saúde) + sigilo médico

  ### 2. Financial Data — Banking Statements
  - **Path:** `extrato-bancario/201[6-9]/`
  - **Content:** Monthly PDF statements (39–135KB) — conta, agência, movimentos bancários
  - **LGPD:** Art. 11 (vida financeira)

  ### 3. HR & Payroll Data
  - **Path:** `rh/` — Editais de convocação, perícia médica
  - **Path:** `gestao-pessoal-permutas-e-cedidos/2019/[mês]/`
  - **Files:** `SERVIDORES-CEDIDOS-SAUDE.pdf` (523 matrículas), `SERVIDORES-CEDIDOS-EDUCACAO.pdf` (80 matrículas), `SERVIDORES-CEDIDOS-ASSISTENCIA-SOCIAL.pdf`, `SERVIDORES-CEDIDOS-DEFESA-SOCIAL.pdf`, `PERMUTA-EDUCACAO.pdf`
  - **PII:** Servidor names, matrículas, lotação, cargo — 600+ records across files
  - **LGPD:** Art. 5, 11 (dado funcional)

  ### 4. Social Benefits — Auxílio Atleta
  - **Path:** `auxilio-atleta/2019/janeiro/18-01-19-GISELE-DE-SOUZA-SILVA-EMPENHO-613-19.pdf` (40 páginas, 9.1MB)
  - **Content:** Scanned empenhos with beneficiary name, **CPF**, **CNPJ**, **valor pago**, **COF** (código bancário), conta corrente
  - **LGPD:** Art. 5, 11 (identificador + vida financeira) + LAI violação de forma (directory listing bruto)

  ### 5. Pension Fund — Previdência
  - **Path:** `previdencia/relatorio-mensal/`, `previdencia/CARTEIRA-DE-INVESTIMENTOS/`
  - **Files:** 12 monthly reports (1.6–1.8MB each), investment portfolio PDFs, atas do conselho, avaliação atuarial (10MB), leis, dispensas de licitação
  - **LGPD:** Art. 11 (dado previdenciário/financeiro) + sigilo previdenciário

  ### 6. Licitações — Massive Exposure
  - **Path:** `licitacao/` — 300+ PDFs (atas, editais, laudos, contratos, recursos)
  - **File:** `licitacao.zip` — **2.6GB** (potential full database dump / SQL / wp-config)
  - **Risk:** Supply chain, financial fraud, contract manipulation

  ### 7. CELEPAR Backup
  - **Path:** `celepar-bkp/arquivos/` — Backup from CELEPAR (Paraná state IT company)
  - **Content:** Eventos, File, Flash, Gallery, Icones, Image, Imagens_Noticias, Media, _thumbs
  - **Risk:** Supply-chain / infrastructure exposure

  ### 8. Other Financial Directories
  - `notas-fiscais-e-empenhos/` (2016-2019)
  - `operacoes-financeiras-de-qualquer-natureza/`
  - `conselhos/`, `tce/`, `contratos-de-repasse/`, `convenios/`

- **Fix Verified:** N/A — still open

## Evidence Verified

- Downloaded and analyzed: `CARDIOLOGIA-1.pdf` (patient waitlist) → contains CNS numbers in scanned tables
- Downloaded and analyzed: `18-01-19-GISELE-DE-SOUZA-SILVA-EMPENHO-613-19.pdf` (auxílio atleta) → 40-page scanned empenho with CPF, CNPJ, valor, COF, conta bancária
- Downloaded and analyzed: `SERVIDORES-CEDIDOS-SAUDE.pdf` → 523 matrículas + names + lotação
- Downloaded and analyzed: `SERVIDORES-CEDIDOS-EDUCACAO.pdf` → 80 matrículas
- Directory listings confirmed on all paths above

## Timeline

- **Reported:** [DD/MM/YYYY] via CERT.br + CTIR Gov
- **Acknowledged:**
- **Fixed:**
- **Verified:**