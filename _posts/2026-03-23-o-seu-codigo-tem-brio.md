---
title: O seu código tem brio?
author: v0xn0x
date: 2026-03-23 15:43:00 -0300
categories: [SEGURANCA, AUDITORIA, OPINIAO]
tags: [AUDITORIA, LARAVEL, DOCKER, IA, OWASP]
pin: false
math: true
mermaid: true
image:
  path: /img/o-seu-codigo-tem-brio-01.png
---

## E aí, qual vai ser?

Você acha que o seu código é seguro? Você se importa com isso?

O que você vai ler aqui não é um relatório técnico asséptico para um burocrata ver. Este é o relato de uma humilhação necessária. Eu tenho um SaaS Laravel "pronto". Cinquenta mil linhas de código. Multitenancy, Docker, PostgreSQL - o pacote completo.

Eu também não gosto de admitir, mas auditar é um trabalho árduo que precisa ser feito. E com o advento das IAs, não há mais desculpas para negligenciar o que, nesse caso, nem foi tão árduo assim.

Nessa jornada eu utilizei o Claude Code (Modelo Opus 4.6) para auditar o meu código. E em uma única sessão, a máquina fez o que nós, humanos, evitamos por puro ego, preguiça ou falta de conhecimento: ela olhou para o código sem julgamento prévio e devolveu 14 findings. Sem falsos positivo. Sem achismo.

A gente jura que o `.env` local está seguro, que o Redis "ninguém vai achar", que aquele Xdebug na imagem de produção é "só por um momento". Eu jurava tudo isso.

Este texto é sobre o brio de quem decide parar de mentir para si mesmo. Vou mostrar os findings que mais doeram, como corrigi cada um e, no final, o que aprendi sobre a diferença entre código que funciona e código que merece existir.

## O Programa

A aplicação é um gerenciador de vulnerabilidades, um SaaS multi-tenant construído com Laravel 11, PostgreSQL 16, Docker e uma arquitetura que eu já considerava sólida. E foi o seu objetivo que me obrigou a realizar a auditoria: o mínimo que se espera de um programa que gerencia vulnerabilidades é que ele próprio não seja a vulnerabilidade a ser gerida.

Mas antes de mostrar os problemas, eu vou destacar o que já estava certo. A auditoria confirmou 22 padrões seguros no projeto, e isso importa tanto quanto os findings:

- **Zero SQL injection** — todos os `whereRaw` usam bindings parametrizados
- **Zero `$request->all()`** — services extraem campos individualmente
- **CSP nonce-based** — `script-src` sem `unsafe-inline`, nonce gerado por request
- **2FA com `hash_equals()`** — comparação timing-safe nos cookies de "lembrar dispositivo"
- **EXIF stripping** em todos os uploads de imagem
- **Rate limiting** em login, password reset e enumeração de subdomínios
- **Row-Level Security** no PostgreSQL para isolamento de dados por tenant
- **UUIDs** em vez de IDs sequenciais em todos os models

Saber que nem tudo é pranto e ranger de dentes me deu confiança para encarar o que veio pela frente. Não se trata de um código amador, se trata de um código maduro com brechas que só aparecem quando alguém tem coragem de procurar.

## Como a Auditoria Funcionou

O prompt que usei não foi um "analise a segurança deste projeto". Foi um documento estruturado com cinco camadas:

1. **Contexto explícito:** stack, tipo de aplicação, dados sensíveis tratados (PoCs, CVEs, PII, credenciais), modelo de autenticação multi-tenant, infraestrutura Docker
2. **Fase de reconhecimento obrigatória:** mapear antes de auditar: `composer.json`, rotas, middlewares, models, migrations, Docker
3. **Domínios de auditoria definidos:** Infraestrutura & Configuração, Lógica de Aplicação, Autenticação e Autorização
4. **Formato de relatório exigido:** severidade, CVSS, categoria OWASP, `arquivo:linha`, evidência, remediação
5. **Regras de conduta:** zero suposições, zero achismos, zero falsos positivos

> O prompt completo está disponível [aqui](/extras/promp-audit-2026-03-19). Se você pretende fazer algo parecido, vale a leitura, pois a qualidade da auditoria depende diretamente da qualidade do que você pede.

O Claude Code paralelizou o trabalho em três agentes simultâneos:

| Agente | Escopo | Cobertura |
|--------|--------|-----------|
| Models & Services | `$fillable`, SQL injection, lógica de negócio | 16 models + 18 services |
| Controllers & Requests | IDOR, mass assignment, XSS, autorização | 24 controllers + 32 form requests |
| Middleware & Config | CSP, sessão, CORS, headers, views `{!! !!}` | 12 middlewares + 11 configs + todas as views |

Em paralelo, executou `composer audit`, `npm audit`, varredura no histórico do git por secrets e verificação do `.gitignore`. Tudo em uma única sessão.

O resultado: **14 findings reais, de CRITICAL a LOW. Zero falsos positivos.**

Vou detalhar aqui os cinco findings que mais me ensinaram, por gravidade, por profundidade da análise ou por terem me forçado a tomar decisões difíceis. Os 14 findings com todas as evidências, códigos e justificativas estão no [relatório completo](/extras/audit-2026-03-19).

---

## Atire a primeira pedra

**CRITICAL — CVSS 8.6 — OWASP A07**

Aqui está a primeira pedrada. E ela é vergonhosa.

O arquivo `.env` guardava credenciais reais em texto puro. Senha real do Gmail. API Key real do Bugsnag. Tudo ali, exposto, como se a segurança fosse um problema para o "eu do futuro" resolver.

Como o diretório `src/` é montado via volume Docker (`./src:/var/www/html`), qualquer processo com acesso ao filesystem do host, um malware, um colega com o laptop aberto, um backup não criptografado, teria as credenciais de produção na mão. O `.env` nunca foi commitado (`.gitignore` correto), mas isso não basta.

Para desenvolvimento local, não há justificativa para usar SMTP real. O Laravel oferece o driver `log`, que escreve os e-mails no log da aplicação em vez de enviá-los. Por que eu estava usando uma conta real? Por preguiça. Por querer ver o e-mail chegando "de verdade" em vez de ler o log.

```php
# ANTES
MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PASSWORD="**** **** **** ****"
BUGSNAG_API_KEY=**********************

# DEPOIS
MAIL_MAILER=log
MAIL_HOST=127.0.0.1
MAIL_PASSWORD=null
BUGSNAG_API_KEY=
```

A correção no código levou 10 segundos. A remediação de verdade foi outra história: revogar a senha do Gmail imediatamente (ela já foi exposta, *mesmo que só localmente*), rotacionar a chave do Bugsnag no dashboard do serviço e planejar um secrets manager para não cair de novo na tentação da conveniência.

O `.env.example` já estava limpo, com `MAIL_MAILER=log` como default. Isso prova que eu sabia o que era certo e fiz o errado por puro descuido.

---

## O Cofre sem Senha

**HIGH - CVSS 7.2 - OWASP A07**

O Redis é o motor silencioso da aplicação. Guarda cache, gerencia filas, guarda as sessões dos usuários. E como ele estava rodando? Sem senha. Porta 6379 escancarada para o host.

Qualquer processo na máquina ou qualquer malware podia simplesmente conectar no Redis e fazer o que quisesse. Com a sessão no Redis, isso se tornava ainda mais crítico: um atacante local podia sequestrar sessões diretamente, sem nem precisar interceptar tráfego.

Três mudanças simultâneas:

```yaml
# docker-compose.yml - ANTES
redis:
  command: redis-server --appendonly yes
  ports:
    - "${REDIS_PORT:-6379}:6379"
  healthcheck:
    test: ["CMD", "redis-cli", "ping"]

# docker-compose.yml - DEPOIS
redis:
  command: redis-server --appendonly yes --requirepass "${REDIS_PASSWORD}"
  # Port mapping removido. O host não precisa ver o Redis.
  healthcheck:
    test: ["CMD", "redis-cli", "-a", "${REDIS_PASSWORD}", "ping"]
```

Remover o port mapping vai incomodar quem gosta de "olhar" o Redis pelo RedisInsight? Vai. Mas se precisar inspecionar, `docker compose exec redis redis-cli -a $REDIS_PASSWORD` resolve. O conforto não pode custar a segurança.

---

## O Fantasma

**HIGH — CVSS 6.5 — OWASP A05**

O Xdebug estava instalado incondicionalmente no Dockerfile. A mesma imagem usada em desenvolvimento seria a base para produção. Mesmo com `start_with_request=trigger`, um atacante que enviasse o cookie `XDEBUG_TRIGGER` poderia ativar o debugger remoto expondo variáveis, stack traces, potencialmente executando código arbitrário.

O "depois eu corrijo" havia caído no esquecimento e se tornado permanente.

```dockerfile
# ANTES (sempre instala)
RUN pecl install xdebug && docker-php-ext-enable xdebug

# DEPOIS (opt-in via build arg)
ARG INSTALL_XDEBUG=false
RUN if [ "$INSTALL_XDEBUG" = "true" ]; then \
    pecl install xdebug && docker-php-ext-enable xdebug; \
fi
```

No `docker-compose.yml`, apenas o container `app` recebe `INSTALL_XDEBUG=true`. Workers de fila e scheduler nunca recebem, não há razão para debug nesses containers. Em produção, basta não definir a variável. Sem mágica, sem multi-stage build, sem complexidade. Só a disciplina de tornar o inseguro opt-in em vez de default.

---

## DoS no Checkout

**MEDIUM — CVSS 4.3 — OWASP A04**

Esse foi o finding que mais me impressionou. Não pela gravidade, mas pela profundidade da análise.

O método `isEmailAvailable()` do `CheckoutService` executava `Tenant::all()` e fazia uma query em **cada banco de dados de tenant** para verificar se o e-mail já existia.

```php
// ANTES — carrega todos os tenants em memória + query em cada banco
$tenants = Tenant::all();
foreach ($tenants as $tenant) {
    $exists = $tenant->run(function () use ($email) {
        return TenantUser::where('email', $email)->exists();
    });
}
```

O endpoint de checkout é público, sem autenticação. Com 100 tenants, são 101 queries por requisição. Com 1000 tenants, 1001 queries. Um script simples podia degradar o servidor em minutos.

Um scanner SAST tradicional não teria encontrado isso. Ele veria código PHP válido e seguiria em frente. O Claude Code entendeu o contexto: endpoint público, `Tenant::all()`, multi-tenant com database-per-tenant e calculou o impacto real. Essa é a diferença entre rodar uma ferramenta e conduzir uma auditoria.

```php
// DEPOIS — lazy loading + early return
foreach (Tenant::cursor() as $tenant) {
    $exists = $tenant->run(function () use ($email) {
        return TenantUser::where('email', $email)->exists();
    });
    if ($exists) return false;
}
```

Rate limiting na rota (`throttle:5,1`) como camada adicional. A solução ideal, um índice centralizado de e-mails na central database, está no backlog. Por enquanto, o risco imediato está controlado sem adicionar complexidade prematura.

---

## O Cavalo de Troia

**LOW — CVSS 3.7 — Mitigado**

O campo `role` estava no `$fillable` do model `Tenant\User`. Em teoria: se um controller futuro usasse `User::create($request->all())`, um atacante poderia enviar `role=admin` e escalar privilégios.

Minha primeira reação foi remover do `$fillable`. Fiz a alteração e veio o caos: `UserFactory` parou de funcionar, seeders quebraram, testes automatizados começaram a berrar. Remover o campo exigiria refatorar meses de infraestrutura de testes.

O próprio Claude Code passou por isso. Ele propôs a remoção, testou, detectou a quebra na test suite e pivotou sozinho para defense-in-depth. Funcionou, mas foi um processo iterativo que exigiu minha supervisão, a IA ainda precisa de um humano que saiba quando aceitar o pivô e quando insistir na solução ideal.

A solução: em vez de lutar contra meu próprio sistema, blindei todas as camadas ao redor. Para o atacante escalar privilégios, ele teria que passar por middleware que exige role admin, policy que verifica permissão, FormRequest que filtra o input, e o service que agora atribui a role explicitamente:

```php
// CheckoutService.php
$user = TenantUser::create([
    'name'     => $data['admin_name'],
    'email'    => $data['admin_email'],
    'password' => Hash::make($data['admin_password']),
]);

// role não entra no mass-assignment - atribuição explícita
$user->role = TenantUserRole::ADMIN;
$user->save();
```

A correção teórica nem sempre é a correção prática. A perfeição que quebra o sistema não é virtude, é ego técnico. O que importa é se o risco real está controlado.

---

## Conclusão

Dos 14 findings, 11 foram corrigidos e 3 foram aceitos como risco consciente e documentado. Todos os CRITICAL e HIGH foram resolvidos. Os riscos aceitos não são "ignorados", são decisões com justificativa técnica, registradas e revisáveis. O relatório completo com cada finding, evidência, CVSS e status está [aqui](/extras/audit-2026-03-19).

A diferença entre um scanner SAST e o que foi feito aqui se resume a uma palavra: contexto. O finding do `Tenant::all()` no checkout não é um padrão reconhecível por regra estática, é uma consequência arquitetural que só faz sentido quando você entende o ambiente multi-tenant com database-per-tenant e endpoint público. Zero falsos positivos em 14 findings é um número que qualquer ferramenta tradicional levaria semanas para atingir num projeto Laravel complexo.

Ao mesmo tempo, a IA não é infalível. O pivô no finding do `$fillable` exigiu supervisão humana. Os riscos aceitos exigiram julgamento humano. A auditoria com IA não substitui o instinto, ela reduz o tempo entre "código escrito" e "código seguro" e te força a documentar cada decisão em vez de empurrar com a barriga.

O resultado mais valioso não são as correções individuais. É o documento vivo que ficou: um relatório com o status de cada finding e o racional por trás de cada escolha. Quando a próxima auditoria acontecer, ela parte de uma baseline documentada, não de um código opaco que ninguém sabe ao certo se é seguro.

O próximo passo é submeter o sistema a um pentester profissional, um humano para tentar encontrar os túneis que a lógica algorítmica ainda não consegue prever.

A pergunta agora não é mais sobre o meu código. É sobre o seu.

---

> - **Nota técnica:** Este post foi escrito durante o processo de auditoria, não depois. Cada seção foi documentada no momento da correção para capturar as decisões em tempo real.
> - **Ferramentas:** Claude Code, Laravel 11, Docker, PostgreSQL 16.
> - **Relatório completo:** [14 findings: evidências, CVSS, status e remediações](/extras/audit-2026-03-19)
> - **Prompt da auditoria:** [Documento estruturado usado no Claude Code](/extras/promp-audit-2026-03-19)
