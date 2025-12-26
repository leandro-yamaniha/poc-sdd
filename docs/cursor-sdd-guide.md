# Guia: Como usar SDD com Cursor

## Objetivo

Orientar o uso do **Cursor** em um repositório SDD para:

- Manter a spec como SSOT (Single Source of Truth)
- Usar Chat/Edit com contexto (`docs/`, `specs/`, `ks/`)
- Ajudar migrações e refactors sem cair em vibe coding

---

## Organização do projeto

Recomenda-se a seguinte estrutura (ou algo próximo disso):

- `specs/` – contratos oficiais (OpenAPI/AsyncAPI/GraphQL)
- `docs/` – guias de SDD, migração, AppSec, KS, prompts
- `ks/` – templates de código, CI, segurança, observabilidade
- Código em `src/`, `services/`, etc.

---

## Regras gerais de uso no Cursor

1. **Sempre mencionar a spec e os docs** no Chat/Edit

   Exemplo de padrão de prompt:

   > "Considere `specs/orders-openapi.yaml` como fonte de verdade e use também `docs/sdd-migration-guide.md` como referência de boas práticas de migração."

2. **Usar seleção de código**

   - Selecione o código relevante e peça refactors guiados pela spec.
   - Ideal para mudanças locais e controladas.

3. **Referenciar arquivos explicitamente** no chat

   - Ex.: "Veja `specs/orders-openapi.yaml` e `ks/services/node/rest-service-template/` antes de responder".

---

## Fluxos recomendados

### Fluxo 1: Refactor/migração com base na spec

1. Abra a spec e o código a ser migrado/refatorado.
2. Selecione o trecho de código no Cursor.
3. Use um prompt do tipo:

   > "Refatore este código para alinhar 100% com o contrato em `specs/orders-openapi.yaml`.  
   > Não altere campos/status codes sem justificar claramente. Se a spec estiver em conflito com o código atual, siga a spec."

4. Valide com testes/contract tests e revise o diff com cuidado.

### Fluxo 2: Criar novo módulo a partir de KS

1. Abra o KS relevante (ex.: `ks/services/java/spring-boot-rest-template/`).
2. No chat do Cursor, peça:

   > "Quero criar um novo serviço baseado em `specs/customers-openapi.yaml`, seguindo o padrão de `ks/services/java/spring-boot-rest-template/`.  
   > Gere a estrutura inicial de pastas, controllers, DTOs e configuração básica de segurança, sem inventar campos que não estejam na spec."

3. Peça também a geração de testes (unit + contract) e ajuste manualmente.

---

## Customizações recomendadas no Cursor

### Regras/profiling de projeto (quando suportado)

Definir instruções como:

- "Use SDD: sempre consulte `specs/` antes de alterar endpoints."
- "Siga os padrões de segurança descritos em `docs/sdd-security.md`."
- "Prefira adaptar templates em `ks/` em vez de gerar estruturas novas do zero."

### Snippets de prompts

Manter no repositório (ex.: `docs/prompts-library.md`) prompts específicos para Cursor, como:

- Migração de stack com base em `docs/sdd-migration-guide.md`
- Criação de testes de contrato a partir de spec
- Adaptação de templates de `ks/` para novos domínios

---

## Boas práticas

- Usar o Chat do Cursor para **planejar** (Specify/Plan) e o Edit para **executar** (Tasks/Implement).
- Validar sempre com testes em local + pipeline de CI.
- Nunca aceitar grandes refactors sem revisar diffs cuidadosamente.
- Tratar sugestões da IA como propostas, não como verdade absoluta.
