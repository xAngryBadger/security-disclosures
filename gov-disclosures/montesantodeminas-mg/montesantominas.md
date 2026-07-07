# Monte Santo de Minas / MG — FUNDEB Council Resignation Letters (CPF Exposure)

**Organization:** Prefeitura Municipal de Monte Santo de Minas / MG  
**Vulnerability Type:** Directory Listing (CWE-548) + PII Exposure  
**Discovery Date:** 25/06/2026  
**Status:** 🔴 **Still open**  
**Verification Date:** 25/06/2026  
**CERT.br Ticket:**  
**CTIR Gov Ticket:**  

## Summary

Directory listing enabled on `/wp-content/uploads/2024/01/` exposing FUNDEB council resignation letters containing full names and CPF numbers of council members.

## Details

- **Asset:** `https://montesantodeminas.mg.gov.br/wp-content/uploads/2024/01/`
- **Exposed Data:** Full name and CPF of FUNDEB council members in scanned resignation letters (e.g. `Carta-Renuncia-Jaqueline-*.jpg`, `Carta-de-renuncia-Roseli-*.jpg`, `Carta-de-renuncia-Juliana-*.jpg`, etc. — 20+ files)
- **File Types:** JPEG scans of signed resignation letters (60KB–266KB each)
- **Fix Verified:** N/A — still open

## Timeline

- **Reported:** [DD/MM/YYYY] via CERT.br + CTIR Gov
- **Acknowledged:**
- **Fixed:**
- **Verified:**