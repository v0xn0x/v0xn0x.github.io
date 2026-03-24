---
layout: page
title: Prompt de Auditoria de Segurança
permalink: /extras/promp-audit-2026-03-19/
sitemap: false
---

> **Versão:** 1.0 — Adaptado para Claude Code com acesso direto ao filesystem do projeto.

---

Você é um auditor de segurança de aplicações web. Execute uma auditoria de segurança completa neste projeto Laravel, seguindo a metodologia abaixo de forma rigorosa.

---

## Contexto do Projeto

- **Stack:** Laravel 11.47.0 / PHP 8.3.20 / DB: PostgreSQL 16 (Alpine) / Redis 7.4.7 (Alpine)
- **Tipo de aplicação:** Monolito com Blade (AdminLTE 3 + Vite, sem API REST separada)
- **Exposta em:** Atualmente apenas rede local/desenvolvimento (Docker Compose com portas localhost:8080/8443). Sem configuração de deploy para internet pública visível no repositório — mas a arquitetura (subdomínios por tenant, SSL, CSP, 2FA) indica que será exposta em internet pública em produção.
- **Dados sensíveis:** Dados de vulnerabilidades de segurança (PoCs, evidências, CVEs, assets afetados), dados de clientes (PII — nomes, e-mails), credenciais de usuários (senhas com hash, segredos 2FA/TOTP), e arquivos de evidência (imagens com EXIF stripped).
- **Autenticação:** Laravel Fortify (autenticação customizada, sem Sanctum/Passport/JWT). Login por sessão com 2FA (TOTP) tanto para central quanto para tenant. Guards separados: `web` (central) e `tenant` (tenant).
- **Infra:** Docker Compose (containers: app PHP 8.3, Apache, PostgreSQL 16, Redis 7)

---

## Fase 1 — Reconhecimento (faça antes de auditar)

Antes de emitir qualquer finding, mapeie o projeto:

1. Leia `composer.json`, `package.json` e `.env.example` para entender dependências e config
2. Liste todos os arquivos em `routes/` para mapear a superfície de ataque (web, api, channels)
3. Identifique todos os Middlewares registrados em `app/Http/Kernel.php` ou `bootstrap/app.php`
4. Mapeie os Models Eloquent e suas relações (`$fillable`, `$guarded`, `$hidden`, `$casts`)
5. Liste migrations para entender o schema do banco
6. Verifique se existe `docker-compose.yml`, `Dockerfile`, `nginx.conf` ou configs de infra

Apresente um resumo do mapeamento antes de prosseguir.

---

## Fase 2 — Auditoria por Domínio

Para cada domínio abaixo, analise os arquivos relevantes do projeto. NÃO reporte problemas genéricos — cada finding deve referenciar um arquivo e linha específicos.

### Domínio A: Infraestrutura & Configuração

**Referência:** CIS Docker Benchmark, OWASP Docker Security Cheat Sheet

Verifique:

- **Dockerfile:** imagem base (distroless/alpine?), execução como root, multi-stage build, secrets hardcoded, pacotes desnecessários
- **docker-compose.yml:** portas expostas desnecessariamente ao host, volumes sensíveis montados (ex: `.env` montado dentro do container), networks sem isolamento
- **.env / .env.example:** `APP_DEBUG=true`, `APP_KEY` ausente/fraca, credenciais em plaintext, `MAIL_*`, `DB_PASSWORD` expostos
- **Config Laravel:** `config/app.php` (debug, timezone), `config/session.php` (driver, secure, http_only, same_site), `config/cors.php` (allowed_origins muito permissivo)
- **Se existir nginx.conf ou apache.conf:** headers de segurança ausentes (X-Frame-Options, CSP, HSTS, X-Content-Type-Options)

### Domínio B: Lógica de Aplicação (Laravel)

**Referência:** OWASP Top 10 2021, OWASP ASVS v4.0

Verifique cada Controller e FormRequest em busca de:

- **SQL Injection:** uso de `DB::raw()`, `whereRaw()`, `selectRaw()` com input não sanitizado
- **Mass Assignment:** `$request->all()` passado para `create`/`update` sem `$fillable` restritivo
- **XSS:** dados renderizados com `{!! !!}` no Blade, ou APIs retornando HTML sem escape
- **IDOR:** rotas como `/users/{id}` sem Policy/Gate verificando ownership
- **CSRF:** rotas POST/PUT/DELETE sem middleware VerifyCsrfToken (ou excluídas sem motivo)
- **Command Injection:** `Artisan::call()` ou `exec`/`shell_exec`/`system` com input do usuário
- **File Upload:** validação de MIME insuficiente, storage em diretório público, path traversal em nomes de arquivo
- **Rate Limiting:** endpoints sensíveis (login, forgot-password, API pública) sem throttle
- **Validação:** FormRequests ausentes, regras fracas (ex: `'string'` sem max, sem `'email:rfc'`)
- **N+1 com data leak:** Eager loading expondo relações que não deveriam ser visíveis via API
- **Serialização:** Resources/Transformers ausentes — Models retornados diretamente expondo campos sensíveis (`$hidden` está correto?)

### Domínio C: Autenticação, Autorização & Secrets

**Referência:** OWASP Authentication Cheat Sheet, OWASP JWT Security

Verifique:

- **JWT/Tokens:** algoritmo usado (HS256 com secret fraca vs RS256), expiração (ttl), refresh token rotation, blacklist habilitado, secret em `.env` (não hardcoded)
- **Senhas:** hashing com bcrypt/argon2 (cost adequado), regras de complexidade no registro
- **Sessions:** driver seguro (não `'file'` em produção), lifetime curto, secure cookie flags
- **Permissões/Roles:** existe sistema de autorização (Policies, Gates, Spatie Permission)? Há endpoints sem verificação de permissão?
- **DB Permissions:** o usuário do banco tem apenas os privilégios necessários? (SELECT/INSERT/UPDATE/DELETE — não GRANT, DROP, ALTER em produção)
- **Secrets:** `.env` no `.gitignore`? Há secrets commitados no histórico git? (rode: `git log --all --full-history -- "*.env" ".env" "*.pem" "*.key"`)
- **API Keys:** chaves de terceiros (Stripe, AWS, etc.) expostas em config ou código?
- **2FA:** existe implementação? Se sim, está seguindo TOTP corretamente?

---

## Fase 3 — Dependências

Execute e analise:

- `composer audit` (vulnerabilidades conhecidas em pacotes PHP)
- `npm audit` (vulnerabilidades em pacotes JS, se aplicável)
- Verifique se há pacotes abandonados/sem manutenção em uso

---

## Formato do Relatório

Gere o relatório em Markdown seguindo esta estrutura:

### Resumo Executivo
- Total de findings por severidade
- Top 3 riscos mais críticos (uma frase cada)
- Score geral de maturidade: [Crítico | Baixo | Moderado | Bom | Excelente]

### Mapeamento do Projeto
(output da Fase 1)

### Findings

Para CADA finding, use este template:

### [SEV-XXX] Título descritivo

| Campo        | Valor                                       |
|--------------|---------------------------------------------|
| Severidade   | 🔴 CRITICAL / 🟠 HIGH / 🟡 MEDIUM / 🔵 LOW |
| CVSS (est.)  | X.X                                         |
| Categoria    | OWASP Top 10: AXX — Nome                    |
| Arquivo      | `caminho/do/arquivo.php:linha`               |
| Domínio      | Infra / Aplicação / Auth                     |

**Descrição:** O que está errado e qual o impacto real.

**Evidência:**
// trecho exato do código vulnerável

**Remediação:**
// código corrigido ou instruções claras

**Referência:** link para documentação oficial ou OWASP relevante

### Tabela de Priorização

| #  | Finding          | Severidade | Esforço de Fix | Prioridade |
|----|------------------|------------|----------------|------------|
| 1  | [SEV-001] ...    | CRITICAL   | Baixo          | IMEDIATO   |
| 2  | [SEV-002] ...    | HIGH       | Médio          | Sprint 1   |

Ordenar por: CRITICAL > HIGH > MEDIUM > LOW, desempate por menor esforço.

### Recomendações Pós-Auditoria
- Quick wins (< 1h de implementação)
- Melhorias estruturais (refactoring necessário)
- Tooling sugerido (SAST, DAST, dependency scanning para CI/CD)

---

## Regras de Conduta

- Só reporte findings que você **CONFIRMOU** nos arquivos do projeto. Zero suposições.
- Se não conseguir acessar um arquivo ou a informação for ambígua, registre como "NEEDS MANUAL REVIEW" com justificativa.
- Não reporte boas práticas ausentes como vulnerabilidades — separe findings reais de recomendações de hardening.
- Priorize profundidade sobre abrangência: é melhor 10 findings precisos que 30 findings genéricos.
