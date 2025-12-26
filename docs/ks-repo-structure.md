# Estrutura de um repositório de KS + IA + SDD

Este documento descreve uma **estrutura hipotética** de repositório que combina:

- **SDD (Spec-Driven Development)** como trilho principal
- **KS (Knowledge Snippets)** como "golden path" de código e configuração
- **IA** como acelerador para gerar/adaptar templates a partir das specs e dos KS

A ideia é ter um repositório que sirva tanto para **documentar o jeito certo de fazer** quanto para **alimentar ferramentas de IA** (RAG, agentes, prompts) com exemplos de alta qualidade.

---

## Visão geral da estrutura

```text
/
├── docs/
│   ├── concepts.md
│   ├── workflow.md
│   ├── ai-enhanced-sdd.md
│   ├── sdd-migration-guide.md              # Guia de migrações orientadas a SDD
│   └── ks-and-templates-guide.md           # Como usar KS + IA p/ gerar código
│
├── ks/                                     # Knowledge Snippets (golden)
│   ├── specs/                              # Specs de exemplo "bem feitas"
│   │   ├── rest/
│   │   │   ├── orders-v1-openapi.yaml
│   │   │   └── customers-v1-openapi.yaml
│   │   └── events/
│   │       └── billing-events-asyncapi.yaml
│   │
│   ├── services/
│   │   ├── node/
│   │   │   ├── rest-service-template/      # Serviço REST base spec-first
│   │   │   ├── migration-adapter-template/ # Adapter v1→v2, Strangler Fig
│   │   │   └── contract-tests-template/    # Jest + Pact, por ex.
│   │   ├── java/
│   │   │   ├── spring-boot-rest-template/
│   │   │   └── spring-migration-template/  # Ex: Spring Boot 2→3
│   │   └── python/
│   │       └── fastapi-service-template/
│   │
│   ├── cicd/
│   │   ├── github-actions-ci-template.yml
│   │   ├── quality-gates-template.md       # SAST, SCA, coverage, perf
│   │   └── contract-tests-pipeline.yml
│   │
│   ├── appsec/
│   │   ├── sast-config-template.yml
│   │   ├── sca-policy-template.yml
│   │   └── threat-modeling-checklist.md
│   │
│   └── observability/
│       ├── logging-template.md
│       ├── tracing-template.md
│       └── metrics-template.md
│
├── generators/                             # Ferramentas para usar IA em cima dos KS
│   ├── prompts/
│   │   ├── generate-service-from-spec.md   # Prompt base: spec + KS → serviço
│   │   ├── migrate-lib-version.md          # Prompt p/ migração de lib
│   │   └── create-migration-adapter.md     # Prompt p/ adapters v1→v2
│   │
│   └── cli/                                # Opcional: tooling
│       ├── package.json
│       ├── src/
│       │   ├── index.ts                    # ex: `sdd-gen` CLI
│       │   └── commands/
│       │       ├── generate-service.ts     # Lê spec + KS, chama IA
│       │       └── generate-migration.ts
│       └── README.md
│
├── examples/                               # Casos de uso completos
│   ├── orders-migration/
│   │   ├── specs/                          # Spec original + nova versão
│   │   ├── old-service/                    # Implementação antiga
│   │   ├── new-service/                    # Implementação nova gerada/adaptada
│   │   ├── adapters/                       # Roteamento v1/v2, feature flags
│   │   └── tests/                          # Contract + perf + regressão
│   └── payments-migration/
│
├── .github/
│   └── workflows/
│       ├── ci.yml                          # Usa KS/cicd como base
│       └── security.yml                    # SAST, SCA, secret scanning etc.
│
├── tools/                                  # Scripts utilitários
│   ├── validate-specs.sh
│   ├── run-contract-tests.sh
│   └── run-performance-tests.sh
│
├── README.md
└── LICENSE
```

---

## docs/: documentação de conceitos e guias

- **`docs/concepts.md`**
  - Explica os pilares do SDD (Design-First, SSOT, Codegen).
- **`docs/workflow.md`**
  - Detalha o fluxo Specify → Plan → Tasks → Implement.
- **`docs/ai-enhanced-sdd.md`**
  - Mostra como IA entra em cada fase sem quebrar a ideia de spec como fonte da verdade.
- **`docs/sdd-migration-guide.md`**
  - Guia de migrações de libs/linguagens/frameworks orientadas a specs.
- **`docs/ks-and-templates-guide.md`** (hipotético)
  - Explica como usar o diretório `ks/` + IA para gerar código, adapters, testes e pipelines.

Esses docs são a "**camada de narrativa**": explicam o *porquê* da estrutura e dão exemplos de uso.

---

## ks/: Knowledge Snippets como "golden path"

O diretório `ks/` é onde moram os artefatos "modelo" que representam o jeito certo de fazer.

### ks/specs/

- **`ks/specs/rest/`**
  - Contém specs REST de referência em OpenAPI (ex.: `orders-v1-openapi.yaml`).
  - Exemplos com:
    - Bons nomes de recursos e campos
    - Tratamento de erros consistente
    - Regras de paginação, filtros, versionamento
- **`ks/specs/events/`**
  - Exemplos de eventos em AsyncAPI (ex.: `billing-events-asyncapi.yaml`).
  - Define canais, payloads e políticas de retries, idempotência etc.

Essas specs são usadas como **material de treino para humanos e IA** sobre como a organização deseja modelar APIs e eventos.

### ks/services/

Templates de serviços por stack, já alinhados a SDD, AppSec e observabilidade.

- **Node** (`ks/services/node/`)
  - `rest-service-template/`
    - Serviço REST básico orientado a spec (OpenAPI como SSOT).
    - Inclui estrutura de pastas, validação de input/output, erros padronizados.
  - `migration-adapter-template/`
    - Exemplo de adapter para migração v1→v2 usando padrão Strangler Fig.
    - Útil em projetos de migração de stack mantendo o contrato.
  - `contract-tests-template/`
    - Exemplo de contract tests (ex.: Jest + Pact) ligados à spec.
- **Java** (`ks/services/java/`)
  - `spring-boot-rest-template/`: projeto-base com Spring Boot, spec-first.
  - `spring-migration-template/`: exemplo de migração Spring Boot 2→3 com foco em compatibilidade.
- **Python** (`ks/services/python/`)
  - `fastapi-service-template/`: serviço FastAPI gerado a partir de spec.

A ideia é: **não pedir para a IA inventar um serviço do zero**, e sim adaptar esses templates a novas specs.

### ks/cicd/

- Pipelines e quality gates padrão da casa:
  - `github-actions-ci-template.yml`: pipeline CI base.
  - `quality-gates-template.md`: define métricas mínimas (SAST, SCA, cobertura, performance).
  - `contract-tests-pipeline.yml`: exemplo de pipeline só para contract tests.

Esses arquivos são usados como base para novos repositórios e também como contexto para IA ao gerar pipelines.

### ks/appsec/

- **`sast-config-template.yml`**
  - Config padrão de SAST (linguagens, exclusões, severidade mínima etc.).
- **`sca-policy-template.yml`**
  - Política de bibliotecas (listas bloqueadas/permitidas, critérios de upgrade).
- **`threat-modeling-checklist.md`**
  - Checklist de threat modeling para ser aplicado sobre as specs.

Conecta diretamente com os docs `appsec-tools.md`, `sast-tools.md` e `sdd-security.md`.

### ks/observability/

- Templates para logging, tracing e métricas.
- Garante que todo código gerado a partir de KS já venha com observabilidade minimalista, mas padronizada.

---

## generators/: IA em cima dos KS

O diretório `generators/` mostra **como automatizar a aplicação dos KS** usando IA.

### generators/prompts/

- **`generate-service-from-spec.md`**
  - Prompt base do tipo:
  - "Dada esta spec OpenAPI e este template em `ks/services/node/rest-service-template/`, gere uma nova implementação para o domínio X, mantendo padrões de segurança, logging e testes."
- **`migrate-lib-version.md`**
  - Prompt para migração de versão de biblioteca orientada por spec e KS.
- **`create-migration-adapter.md`**
  - Prompt para criar adapters v1→v2 (Strangler Fig) usando a spec como contrato.

### generators/cli/

- Implementação de um CLI (ex.: `sdd-gen`) que:
  - Lê uma spec.
  - Escolhe um KS adequado (stack, tipo de serviço).
  - Monta um prompt estruturado.
  - Chama a IA (manual ou via API) para gerar o código base.

O objetivo é **padronizar o uso da IA**, evitando prompts soltos e não reproduzíveis.

---

## examples/: casos de uso completos

- **`examples/orders-migration/`**
  - `specs/`: spec original + nova versão.
  - `old-service/`: implementação antiga.
  - `new-service/`: implementação nova (gerada/adaptada a partir de KS + IA).
  - `adapters/`: roteamento e compatibilidade entre versões.
  - `tests/`: contract tests, testes funcionais e de performance comparativa.

Esses exemplos contam a história de uma migração real; são ótimos alvos para **demonstrações, workshops e experimentos de IA**.

---

## .github/ e tools/: operacionalizando o padrão

- **`.github/workflows/`**
  - Pipelines CI/CD que reutilizam os templates em `ks/cicd/`.
- **`tools/`**
  - Scripts utilitários para validar specs, rodar contract tests, executar testes de performance etc.

Esses diretórios garantem que o padrão definido em `ks/` **está realmente em uso no dia a dia**.

---

## Como usar essa estrutura na prática

1. **Definir os KS mínimos**
   - Começar pequeno: 1 ou 2 stacks prioritárias, 1 template de serviço, 1 template de migração, 1 pipeline.
2. **Conectar IA aos KS**
   - Criar prompts padrão e (opcionalmente) um CLI que sempre referenciam `ks/` e a spec.
3. **Ensinar o time a usar o repo**
   - Mostrar que o fluxo certo é: *spec → KS → IA → código*, e não *IA solta sem contexto*.
4. **Evoluir o repo com o tempo**
   - Toda melhoria significativa em código/processo vira um novo KS ou uma atualização nos existentes.

Essa estrutura é intencionalmente genérica; você pode adaptá-la ao seu contexto, começando só com as partes que fazem mais sentido agora (por exemplo, apenas `ks/specs`, `ks/services/node` e `generators/prompts`).
