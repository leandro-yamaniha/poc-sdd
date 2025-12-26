# Guia: Como usar Devin com Spec-Driven Development (SDD)

## Objetivo

Este guia discute **como usar o Devin** (ferramenta de desenvolvimento assistido por IA/agentic) em conjunto com **Spec-Driven Development (SDD)**, de forma segura e previsível:

- Encaixando Devin no fluxo **Specify → Plan → Tasks → Implement**
- Mantendo **specs, docs e KS** como trilho
- Usando Devin em tarefas de migração, manutenção e melhoria de qualidade
- Evitando que o agente "saia dos trilhos" (vibe coding agentic)

---

## Princípios básicos: Devin como executor, SDD como orquestrador

1. **Spec como fonte de verdade**
   - Mesmo com um agente como Devin, a regra continua:
     - A verdade está em `specs/` (OpenAPI/AsyncAPI/GraphQL etc.) e `docs/`, não no código que o agente gera.
   - Devin deve ser instruído a **consultar specs e docs** antes de propor ou aplicar mudanças grandes.

2. **SDD define o fluxo, Devin preenche as tarefas**
   - SDD organiza o trabalho em:
     - Specify → Plan → Tasks → Implement.
   - Devin atua principalmente em **Tasks/Implement**:
     - Implementar handlers/endpoints com base na spec.
     - Criar/adaptar testes de contrato.
     - Ajustar pipelines, migrações e refactors pontuais.

3. **KS como "guard-rails" de implementação**
   - `ks/` (Knowledge Snippets) deve ser visto pelo Devin como:
     - Base para serviços, adapters, testes, CI, AppSec, observabilidade.
   - Ao invés de deixar Devin inventar estrutura, peça que **adapte templates de `ks/`** à spec e ao problema.

4. **CI/CD e AppSec como linha de defesa**
   - Tudo que Devin faz deve passar por:
     - Testes (unitários, integração, contrato).
     - Linters/formatters.
     - SAST/SCA e demais checks.
   - Isso garante que mesmo que o agente erre, o pipeline segure.

---

## Como encaixar Devin no fluxo SDD

### 1. Specify: alinhar Devin com a spec e o contexto

Antes de deixar Devin "codar", garanta que:

- A spec relevante está clara:
  - Ex.: `specs/openapi.yaml`, ou uma spec específica por domínio.
- Os docs de apoio estão visíveis:
  - `docs/concepts.md`, `docs/workflow.md`, `docs/sdd-security.md`, `docs/appsec-tools.md`, `docs/sdd-migration-guide.md`.
- Há KS adequados em `ks/` para o tipo de tarefa.

Quando criar uma tarefa para Devin, sempre inclua referências explícitas, por exemplo:

> "Implemente o endpoint `/orders/{id}` conforme descrito em `specs/openapi.yaml`, usando o padrão de serviço em `ks/services/node/rest-service-template/` e respeitando as diretrizes de segurança em `docs/sdd-security.md`."

### 2. Plan: usar Devin para ajudar a detalhar o plano, não para mudar o alvo

- Use Devin para:
  - Sugerir **quebra de trabalho** em tasks menores.
  - Identificar dependências e riscos técnicos.
  - Mapear onde a spec ainda está ambígua ou incompleta.
- Não deixe Devin **alterar a spec sozinho**:
  - Mudanças de spec devem passar por revisão humana e, idealmente, por um fluxo de governança definido em SDD.

Exemplo de instrução:

> "Com base em `specs/openapi.yaml` e em `docs/sdd-migration-guide.md`, proponha um plano de migração para atualizar a biblioteca X de v1 para v2, em etapas pequenas, mantendo o contrato atual. Não altere a spec, apenas sugira tasks e pontos de atenção."

### 3. Tasks: transformar plano em tarefas SDD-friendly

- Cada task que você delega a Devin deve ser:
  - Pequena, focada e ligada a um trecho de spec.
  - Com critérios de aceite claros (ex.: quais testes precisam passar).

Exemplos de tasks boas para Devin:

- "Criar contract tests para os endpoints `/orders` e `/orders/{id}` com base em `specs/openapi.yaml`."
- "Adaptar o serviço atual para seguir o template `ks/services/java/spring-boot-rest-template/` sem alterar o contrato da API."
- "Configurar pipeline de contract tests em CI seguindo `ks/cicd/contract-tests-pipeline.yml`."

### 4. Implement: deixar Devin executar sob supervisão

- Devin pode:
  - Gerar código base para handlers, services, adapters.
  - Criar testes (unit, integration, contract) a partir da spec.
  - Ajustar pipelines de CI/CD, inserir linters e scanners.
- Você deve:
  - Revisar diffs.
  - Garantir que testes e pipelines foram atualizados/rodados.
  - Validar se o resultado continua aderente à spec.

---

## Casos de uso ideais de Devin em SDD

### 1. Migração de stack (libs, frameworks, runtimes)

Combine:

- `docs/sdd-migration-guide.md`
- `specs/` (como contrato de compatibilidade)
- Templates em `ks/services/` e `ks/cicd/`

E peça a Devin para:

- Propor planos de migração incremental.
- Implementar adapters/strangler fig mantendo o contrato.
- Gerar/atualizar testes de contrato e regressão.

### 2. Fortalecer testes e qualidade em volta da spec

- Criar suites de contract tests faltantes.
- Gerar casos de teste para cenários críticos documentados na spec.
- Cobrir fluxos de erro, borda e segurança (ex.: auth, rate limiting, validação).

### 3. Aplicar padrões de segurança e observabilidade

- Ler `docs/sdd-security.md` e `docs/appsec-tools.md`.
- Adaptar serviços/templates para incluir:
  - Autenticação/autorização padronizadas.
  - Logging estruturado.
  - Traços e métricas básicas.

---

## Riscos e armadilhas ao usar Devin sem SDD

- **Vibe coding agentic**
  - Devin pode sair refatorando, criando endpoints e ajustando contratos sem uma fonte de verdade clara.
- **Divergência entre spec e implementação**
  - Sem SDD, é fácil o agente "consertar" o código e esquecer de atualizar a spec.
- **Segurança improvisada**
  - Correções de vulnerabilidades sem seguir padrões podem quebrar contrato ou introduzir novos riscos.

SDD reduz esses riscos ao:

- Impossibilitar (ou desincentivar fortemente) mudança de contrato sem alterar a spec primeiro.
- Forçar que mudanças passem por uma camada de revisão da spec + docs.
- Manter AppSec no fluxo (via `docs/sdd-security.md`, `docs/appsec-tools.md` e ferramentas integradas).

---

## Boas práticas específicas ao usar Devin

1. **Sempre referenciar arquivos-chave nas instruções**
   - Especifique quais specs e docs Devin deve ler antes de agir.

2. **Desconfiar de mudanças grandes e automáticas**
   - Prefira que Devin proponha o plano e gere patches menores.
   - Nunca faça merge automático só porque "foi o agente" que sugeriu.

3. **Tratar Devin como parte do time, não como oráculo**
   - O agente ajuda a executar, mas a responsabilidade de arquitetura, segurança e contrato continua humana.

4. **Registrar aprendizados em KS e docs**
   - Sempre que uma solução gerada por Devin for validada como boa prática, considere extrair para:
     - Um novo template em `ks/`
     - Um trecho de `docs/` ou `docs/prompts-library.md`

---

## Integração com outros documentos

- [SDD + IA em IDEs](../ides-sdd-overview.md)
- [Guia de Migração Orientada a SDD](../../migration/sdd-migration-guide.md)
- [Ferramentas de AppSec no Fluxo SDD](../../security/appsec-tools.md)
- [SDD e Segurança: Security-By-Spec](../../security/sdd-security.md)
- [Estrutura de um repositório de KS + IA + SDD](../../fundamentals/ks-repo-structure.md)
- [Biblioteca de Prompts](../../advanced/prompts-library.md)
