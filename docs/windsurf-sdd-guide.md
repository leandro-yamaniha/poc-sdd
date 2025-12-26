# Guia: Como usar SDD com Windsurf

## Objetivo

Mostrar como configurar e usar o **Windsurf** em um projeto orientado a especificações (SDD), para:

- Trabalhar sempre guiado por `specs/` e `docs/`
- Usar a IA como acelerador (não como fonte da verdade)
- Ajudar em migrações de stack, melhorias de qualidade e AppSec

---

## Estrutura de projeto recomendada

Certifique-se de que o projeto segue (ou converge para) algo como:

- `docs/` – conceitos, fluxos, segurança, migração, KS etc.
- `specs/` – specs OpenAPI/AsyncAPI/GraphQL, versionadas
- `ks/` – Knowledge Snippets (templates de serviço, adapters, tests, CI)
- Código em pastas claras (`src/`, `services/`, etc.)

Quanto mais organizada a estrutura, melhor o Windsurf consegue usar o contexto.

---

## Princípios de uso com SDD

1. **Sempre referenciar a spec**  
   Em qualquer pedido de geração/refactor, a pergunta deve ser:

   > "Considere `specs/...` como fonte de verdade. Não invente endpoints/campos."

2. **Trazer docs relevantes para o contexto**  
   Antes de pedir algo grande, abra/mostre:

   - A spec (`specs/openapi.yaml` ou similar)
   - O doc relevante (`docs/sdd-migration-guide.md`, `docs/appsec-tools.md`, etc.)

   Use isso como base nos prompts.

3. **Pequenas iterações guiadas**  
   Use o Windsurf para:

   - Adaptar templates de `ks/` para uma nova spec
   - Criar/adaptar testes de contrato
   - Ajudar a migrar trechos de código com base em docs de migração

---

## Fluxos recomendados

### Fluxo 1: Criar novo serviço a partir de uma spec

1. Abra a spec em `specs/` (ex.: `specs/orders-openapi.yaml`).
2. Abra um KS de serviço em `ks/services/...` (ex.: `rest-service-template`).
3. Peça algo como:

   > "Usando `specs/orders-openapi.yaml` como fonte de verdade e o padrão que você vê em `ks/services/node/rest-service-template/`, gere a estrutura inicial de um serviço para o domínio Orders.  
   > Não invente campos, siga estritamente a spec. Crie handlers, validações e erros alinhados ao template."

4. Revise e ajuste manualmente; em seguida, rode testes/linters/contract tests.

### Fluxo 2: Migração de stack (versão de lib, framework, runtime)

1. Leia `docs/sdd-migration-guide.md`.
2. Abra o código antigo e o local onde ficará a nova implementação/adapters.
3. Peça:

   > "Estou migrando de <stack A> para <stack B> mantendo o contrato definido em `specs/...`.  
   > Use o guia em `docs/sdd-migration-guide.md` e os templates em `ks/services/...` para sugerir um plano de migração incremental. Liste tasks pequenas respeitando Specify → Plan → Tasks → Implement."

4. Depois, peça geração de adapters, testes e ajustes de CI passo a passo.

---

## Customizações recomendadas

### Instruções gerais do projeto

Configure o Windsurf/assistente do projeto com instruções como:

- "Este repositório usa Spec-Driven Development. Sempre consulte `specs/` e `docs/` antes de sugerir código."
- "A spec é a fonte de verdade; não invente endpoints/campos, e avise se a spec é ambígua."
- "Prefira adaptar templates de `ks/` em vez de gerar estruturas do zero."

### Snippets de prompts reutilizáveis

Mantenha em `docs/prompts-library.md` uma seção para Windsurf com prompts de:

- Geração de serviço a partir de spec
- Migração de lib
- Criação de contract tests
- Refino de performance e observabilidade

---

## Boas práticas

- Sempre rodar, após grandes gerações/mudanças orientadas por IA:
  - Contract tests
  - Linters
  - SAST/SCA
- Encapsular mudanças: peça para o Windsurf focar **em um endpoint/módulo de cada vez**.
- Rever manualmente decisões arquiteturais e de segurança; IA ajuda, mas não decide sozinha.

---

## Workflows

Para padronizar ainda mais o fluxo SDD no Windsurf, você pode configurar **workflows** — sequências de ações automáticas ou semi-automáticas que guiam o agente.

Exemplos de workflows úteis:

- **Inicialização de projeto**: carregar specs e docs automaticamente.
- **Task de implementação**: abrir spec → propor plano → implementar → validar.
- **Validação antes de commit**: rodar testes, linters, secret scanning.
- **Atualização de contrato**: bloquear código até spec ser atualizada.

Para detalhes completos, templates e exemplos de configuração, veja:

- [`docs/windsurf-workflows.md`](windsurf-workflows.md)
- [`docs/windsurf-memories.md`](windsurf-memories.md) – Template de backup de memórias

---

## Conexão com outros documentos

- [`docs/ides-sdd-overview.md`](ides-sdd-overview.md) – Visão geral de SDD + IA em IDEs
- [`docs/ai-ides-comparison.md`](ai-ides-comparison.md) – Comparativo de ferramentas de IA
- [`docs/ai-usage-policy.md`](ai-usage-policy.md) – Política de uso seguro de IA
- [`docs/sdd-migration-guide.md`](sdd-migration-guide.md) – Guia de migração orientada a SDD
