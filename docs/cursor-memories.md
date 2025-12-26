# Memórias do Projeto (Backup Cursor)

Este arquivo serve como **backup versionado** das memórias/regras de projeto configuradas no Cursor.

Mantenha este arquivo atualizado periodicamente para:

- Garantir que regras importantes não se percam.
- Facilitar onboarding de novos desenvolvedores.
- Permitir restauração em caso de reset do Cursor.

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

## Regras para o Cursor (copiar para `.cursor/rules`)

```text
# Regras de projeto (SDD)

## Contexto
- Specs em specs/
- Docs em docs/
- Templates em ks/

## Antes de qualquer implementação
1. Abrir e ler a spec correspondente.
2. Confirmar que entendeu o contrato antes de propor código.

## Durante implementação
3. Implementar um endpoint/módulo por vez.
4. Após cada edição, sugerir rodar testes e linters.

## Antes de commit
5. Verificar se a spec está atualizada.
6. Listar arquivos alterados e pedir confirmação.

## Se detectar mudança de contrato
7. Bloquear e pedir para atualizar a spec primeiro.

## Segurança
- Seguir docs/sdd-security.md.
- Seguir docs/ai-usage-policy.md.
- Não acessar dados de produção.
- Não executar comandos destrutivos sem aprovação.
```

---

## Como restaurar no Cursor

1. Copie o bloco de regras acima.
2. Cole em `.cursor/rules` na raiz do projeto.
3. Ou cole no chat do Cursor no início da sessão como contexto.

---

## Histórico de atualizações

| Data | Responsável | Descrição |
|------|-------------|-----------|
| _YYYY-MM-DD_ | _Nome_ | _Criação inicial_ |

---

## Checklist de manutenção

- [ ] Revisar regras semanalmente.
- [ ] Atualizar este arquivo após mudanças importantes.
- [ ] Sincronizar `.cursor/rules` com este backup.
- [ ] Compartilhar com novos membros do time.
