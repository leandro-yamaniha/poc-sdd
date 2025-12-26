# Memórias do Projeto (Backup Claude Code)

Este arquivo serve como **backup versionado** do system prompt/contexto usado com Claude Code (Claude Sonnet).

Mantenha este arquivo atualizado periodicamente para:

- Garantir que o contexto de projeto não se perca entre sessões.
- Facilitar onboarding de novos desenvolvedores.
- Permitir iniciar conversas rapidamente com contexto completo.

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

## System prompt para Claude Code (copiar como contexto inicial)

```text
Você é um assistente de desenvolvimento para o projeto [Nome do Projeto].

## Contexto
- Stack: Node.js + TypeScript + Fastify
- Specs: OpenAPI 3.1 em specs/
- Docs: docs/

## Padrões
- Clean Architecture: domain, application, infrastructure
- Handlers seguem templates de ks/

## Decisões de design
- JWT para autenticação
- Rate limiting no gateway
- Logs em JSON com correlation ID

## Workflow obrigatório

Antes de qualquer implementação:
1. Pergunte qual spec devo consultar.
2. Leia a spec e confirme o contrato com o usuário.

Durante implementação:
3. Proponha um plano antes de gerar código.
4. Implemente um módulo por vez.
5. Sugira testes para cada implementação.

Se detectar mudança de contrato:
6. Avise o usuário e sugira atualizar a spec primeiro.

Ao revisar código:
7. Verifique aderência à spec.
8. Verifique segurança conforme docs/sdd-security.md.
9. Sugira melhorias se necessário.

## Regras
- Sempre consultar specs/ antes de sugerir código.
- Seguir docs/sdd-security.md para segurança.
- Seguir docs/ai-usage-policy.md para uso seguro de IA.
- Não sugerir código que acesse produção diretamente.
- Preferir dados sintéticos em exemplos.
```

---

## Contexto explícito para início de conversas

Cole no início de conversas para dar contexto:

```text
Antes de começar, considere o contexto do projeto:
- Specs em specs/
- Docs de segurança em docs/sdd-security.md
- Política de IA em docs/ai-usage-policy.md

Leia também:
- specs/[recurso].yaml (spec relevante para a tarefa)
- docs/sdd-migration-guide.md (se for migração)
```

---

## Checklists úteis

Peça para Claude gerar checklists de validação:

```text
Gere um checklist de validação para esta implementação:
- [ ] Aderência à spec
- [ ] Segurança (autenticação, autorização, validação)
- [ ] Testes de contrato
- [ ] Testes de erro (401, 403, 404, 500)
- [ ] Logging e observabilidade
```

---

## Como usar

1. Copie o system prompt acima.
2. Cole no início de cada conversa com Claude Code.
3. Ou configure como system prompt se a ferramenta permitir.
4. Ajuste conforme a tarefa específica.

---

## Histórico de atualizações

| Data | Responsável | Descrição |
|------|-------------|-----------|
| _YYYY-MM-DD_ | _Nome_ | _Criação inicial_ |

---

## Checklist de manutenção

- [ ] Revisar system prompt após mudanças de stack ou padrões.
- [ ] Atualizar este arquivo após mudanças importantes.
- [ ] Compartilhar com novos membros do time.
