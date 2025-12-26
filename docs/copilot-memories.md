# Memórias do Projeto (Backup GitHub Copilot)

Este arquivo serve como **backup versionado** das instruções de projeto configuradas para o GitHub Copilot.

Mantenha este arquivo atualizado periodicamente para:

- Garantir que instruções importantes não se percam.
- Facilitar onboarding de novos desenvolvedores.
- Permitir restauração em caso de reset ou novo repositório.

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

## Instruções para Copilot (copiar para `.github/copilot-instructions.md`)

```markdown
# Instruções para Copilot neste repositório

## Contexto do projeto
- Stack: Node.js + TypeScript + Fastify
- Specs: OpenAPI 3.1 em specs/
- Docs: docs/

## Padrões arquiteturais
- Clean Architecture
- Handlers seguem templates de ks/services/

## Decisões de design
- Autenticação: JWT
- Rate limiting: API Gateway
- Logs: JSON estruturado

## Regras de implementação
- Sempre consultar specs/ antes de sugerir endpoints.
- Seguir docs/sdd-security.md para segurança.
- Preferir dados sintéticos em exemplos e testes.
- Sugerir rodar testes após implementações.

## Fluxo de mudança de contrato
- Se a mudança altera o contrato, avisar o desenvolvedor.
- Sugerir atualizar a spec antes do código.

## Fluxo de PR
- Gerar descrição baseada nos diffs.
- Listar specs afetadas.
- Sugerir testes adicionais se necessário.
```

---

## Comentários estruturados (opcional)

Use comentários padronizados no código para dar contexto local ao Copilot:

```typescript
/**
 * @project API de Orders
 * @stack Node.js + TypeScript + Fastify
 * @spec specs/orders.yaml
 * @patterns Clean Architecture, JWT auth, JSON logs
 */
```

---

## Como restaurar no Copilot

1. Copie o bloco de instruções acima.
2. Cole em `.github/copilot-instructions.md` na raiz do projeto.
3. Commit e push para que o Copilot reconheça as instruções.

---

## Histórico de atualizações

| Data | Responsável | Descrição |
|------|-------------|-----------|
| _YYYY-MM-DD_ | _Nome_ | _Criação inicial_ |

---

## Checklist de manutenção

- [ ] Revisar instruções semanalmente.
- [ ] Atualizar este arquivo após mudanças importantes.
- [ ] Sincronizar `.github/copilot-instructions.md` com este backup.
- [ ] Compartilhar com novos membros do time.
