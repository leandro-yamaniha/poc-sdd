# Memórias do Projeto (Backup Devin)

Este arquivo serve como **backup versionado** do prompt inicial/contexto de sessão usado com o Devin.

Mantenha este arquivo atualizado periodicamente para:

- Garantir que o contexto de projeto não se perca entre sessões.
- Facilitar onboarding de novos desenvolvedores.
- Permitir iniciar sessões rapidamente com contexto completo.

---

## Última atualização

**Data**: _YYYY-MM-DD_  
**Responsável**: _Nome_

---

## Contexto do projeto

- **Nome do projeto**: _Nome do projeto_
- **Stack principal**: _Ex.: Node.js + TypeScript + Fastify_
- **Repositório**: _URL do repositório_

---

## Estrutura do repositório

```text
specs/          # Especificações OpenAPI, AsyncAPI, GraphQL
docs/           # Documentação do projeto
ks/             # Knowledge Snippets (templates)
src/            # Código-fonte
tests/          # Testes automatizados
```

---

## Padrões arquiteturais

- _Ex.: Clean Architecture com camadas domain, application, infrastructure._
- _Ex.: Handlers seguem o padrão de `ks/services/node/rest-service-template/`._
- _Ex.: DTOs gerados a partir de specs._

---

## Decisões de design

- _Ex.: Autenticação via JWT, validação em middleware._
- _Ex.: Rate limiting configurado no API Gateway, não no serviço._
- _Ex.: Logs estruturados em JSON, com correlation ID._

---

## Fluxo SDD

- Toda implementação começa lendo a spec correspondente em `specs/`.
- Mudanças de contrato exigem atualização de spec antes de código.
- Testes de contrato obrigatórios para endpoints públicos.
- Seguir `docs/sdd-security.md` para requisitos de segurança.

---

## Links importantes

| Documento | Descrição |
|-----------|-----------|
| [`docs/sdd-security.md`](sdd-security.md) | Princípios de Security-By-Spec |
| [`docs/ai-usage-policy.md`](ai-usage-policy.md) | Política de uso seguro de IA |
| [`docs/sdd-migration-guide.md`](sdd-migration-guide.md) | Guia de migração orientada a SDD |
| [`docs/appsec-tools.md`](appsec-tools.md) | Ferramentas de AppSec no fluxo SDD |

---

## Prompt inicial para Devin (copiar no início de cada sessão)

```text
# Contexto do projeto

## Stack
- Node.js + TypeScript + Fastify
- Specs: OpenAPI 3.1 em specs/

## Padrões
- Clean Architecture: domain, application, infrastructure
- Handlers seguem ks/services/node/rest-service-template/

## Decisões de design
- JWT para autenticação
- Rate limiting no gateway
- Logs em JSON com correlation ID

## Links importantes
- Segurança: docs/sdd-security.md
- Migração: docs/sdd-migration-guide.md
- Política de IA: docs/ai-usage-policy.md

## Workflow para esta sessão
1. Ler a spec correspondente antes de qualquer implementação.
2. Propor plano de implementação e aguardar aprovação.
3. Implementar em passos pequenos (um endpoint/módulo por vez).
4. Após cada passo, rodar testes e linters.
5. Se detectar mudança de contrato, parar e pedir para atualizar spec.
6. Ao final, gerar PR com descrição detalhada.

## Escopo permitido
- Diretórios: src/, tests/, specs/
- Ambientes: dev, staging (NUNCA produção)

## Regras
- Não executar comandos destrutivos sem aprovação.
- Sempre mostrar diff antes de aplicar mudanças.
- Gerar PRs pequenos e focados.
```

---

## Checkpoints úteis

Use estes comandos para pausar o Devin em pontos específicos:

```text
PARE e me mostre o plano antes de começar.
PARE após implementar o handler e antes de gerar testes.
PARE antes de fazer commit.
```

---

## Como usar

1. Copie o prompt inicial acima.
2. Cole no início de cada sessão com o Devin.
3. Ajuste conforme a tarefa específica.
4. Use os checkpoints para controlar o fluxo.

---

## Histórico de atualizações

| Data | Responsável | Descrição |
|------|-------------|-----------|
| _YYYY-MM-DD_ | _Nome_ | _Criação inicial_ |

---

## Checklist de manutenção

- [ ] Revisar prompt inicial após mudanças de stack ou padrões.
- [ ] Atualizar este arquivo após mudanças importantes.
- [ ] Compartilhar com novos membros do time.
