# Visão geral: SDD + IA em IDEs e Ferramentas

## Objetivo

Este guia reúne os **princípios comuns** de uso de **Spec-Driven Development (SDD)** com ferramentas de IA em IDEs/editores (Windsurf, Cursor, GitHub Copilot, etc.), respondendo a três perguntas:

- Como **extrair o máximo** dessas ferramentas nesse contexto?
- O que **precisa ser customizado** (instruções, prompts, estrutura do repo)?
- Como garantir que a **spec continue sendo a fonte de verdade**?

Os detalhes específicos por ferramenta estão em:

- [Guia: SDD com Windsurf](windsurf-sdd-guide.md)
- [Guia: SDD com Cursor](cursor-sdd-guide.md)
- [Guia: SDD com GitHub Copilot](github-copilot-sdd-guide.md)
 - [Guia: SDD com Devin](devin-sdd-guide.md)
 - [Guia: SDD com Claude Sonnet](claude-sonnet-sdd-guide.md)

---

## Princípios básicos (independente da IDE)

1. **Spec como trilho, IA como motor**
   - A spec (OpenAPI/AsyncAPI/GraphQL/etc.) é a fonte única da verdade.
   - A IA acelera a escrita de código, testes, docs e migrações **em torno** dessa spec.
   - Quando houver conflito entre código sugerido e spec, a spec vence.

2. **Docs e KS como “golden path”**
   - `docs/` concentra o *como fazemos aqui* (workflow, segurança, migração, prompts).
   - `ks/` (Knowledge Snippets) guarda templates de serviços, adapters, testes, CI, AppSec e observabilidade.
   - A IA deve ser usada para **adaptar** esses KS a novas specs e contextos, não para inventar padrões novos.

3. **Pequenas iterações, diffs revisáveis**
   - Pedidos grandes e genéricos tendem a gerar vibe coding.
   - Prefira:
     - Escopo pequeno (um endpoint, um fluxo, um módulo). 
     - Diffs curtos, fáceis de revisar.
     - Ciclo rápido: pedir → revisar → testar → ajustar.

4. **Testes, CI e AppSec sempre no loop**
   - Toda geração maior deve ser seguida de:
     - Testes (unitários, integração, contrato).
     - Linters/formatters.
     - SAST/SCA e outras ferramentas de AppSec, quando aplicável.
   - Isso é crucial em migrações de stack (ver [Guia de Migração Orientada a SDD](sdd-migration-guide.md)).

5. **Transparência e rastreabilidade**
   - Preferir mudanças em que seja claro:
     - Qual spec/trecho de doc guiou a decisão.
     - Qual KS foi usado como base.
   - Sempre que possível, mencionar isso nos PRs/commits.

---

## Preparando o repositório para trabalhar bem com IA

Antes de tentar “tunear” a IDE, organize o repositório:

- **Specs**
  - Centralize specs em `specs/` (ex.: `specs/openapi.yaml`, `specs/*.yaml`).
  - Garanta que elas estejam razoavelmente completas e versionadas.

- **Documentação** (`docs/`)
  - Conceitos: `concepts.md`, `workflow.md`, `ai-enhanced-sdd.md`.
  - Segurança: `sdd-security.md`, `appsec-tools.md`, `sast-tools.md`.
  - Migração: `sdd-migration-guide.md`.
  - IDEs/IA: este doc + guias específicos (Windsurf, Cursor, Copilot).

- **KS (Knowledge Snippets)** (`ks/`)
  - Specs de exemplo (`ks/specs/`).
  - Services/templates por stack (`ks/services/...`).
  - Pipelines (`ks/cicd/`), AppSec (`ks/appsec/`), observabilidade (`ks/observability/`).

- **Prompts**
  - Centralizar prompts em `docs/prompts-library.md`, com seções por ferramenta.

Quanto mais explícito estiver o “jeito certo de fazer”, melhor as IAs conseguem seguir o padrão.

---

## Padrão de fluxo SDD + IA em qualquer IDE

Podemos enxergar o uso de IA em IDEs mapeado para o fluxo SDD:

### 1. Specify (entender e definir o que deve ser feito)

- Usar IA para:
  - Resumir e explicar specs (`specs/*.yaml`).
  - Apontar ambiguidades na spec.
  - Sugerir cenários de uso e casos de teste a partir da spec.
- **Nunca** pular direto para código sem alinhar specs e requisitos.

### 2. Plan (planejar a mudança ou migração)

- Usar IA para:
  - Ajudar a quebrar trabalho em tasks pequenas, ligadas a trechos de spec.
  - Sugerir estratégias de migração (side-by-side, Strangler Fig, canary, etc.).
- Sempre relacionar tasks a:
  - Endpoints/eventos específicos.
  - Objetivos de performance/segurança.

### 3. Tasks (especificar tarefas concretas)

- Tornar tasks bem definidas para a IA:
  - "Implementar handler de `/orders/{id}` conforme spec X".
  - "Criar contract tests para endpoints Y/Z".
  - "Adaptar template de KS A para spec B".
- Pedir para a IA **só o suficiente** para cumprir cada task.

### 4. Implement (gerar/adaptar código, testes, pipelines)

- Usar IA para:
  - Gerar código base a partir de spec + KS.
  - Criar/adaptar testes.
  - Sugerir ajustes em CI/CD ligados a quality gates.
- Sempre revisar, testar e ajustar manualmente.

---

## Customizações comuns em diferentes ferramentas

Alguns tipos de customização se repetem entre Windsurf, Cursor e Copilot:

1. **Instruções globais / de projeto**
   - Deixar claro que o repo usa SDD:
     - "Sempre consulte `specs/` e `docs/` antes de sugerir código".
     - "A spec é a fonte de verdade; não invente endpoints/campos".
     - "Considere também `docs/sdd-security.md` e `docs/appsec-tools.md` em código sensível".

2. **Snippets de prompts padrão**
   - Manter em `docs/prompts-library.md`:
     - Prompts para implementar endpoint a partir de spec.
     - Prompts para migração de stack (usando `sdd-migration-guide.md`).
     - Prompts para criação de contract tests e testes de regressão.

3. **Integração com a estrutura do repo**
   - Ensinar a IA a olhar para:
     - `ks/services/...` como base para novos serviços.
     - `ks/cicd/...` como referência para pipelines.
     - `ks/appsec/...` como referência de AppSec.

---

## Checklist rápido: “estamos usando IA de forma SDD-friendly?”

- [ ] Specs estão em `specs/` e são tratadas como SSOT?
- [ ] Os docs principais de SDD/segurança/migração existem em `docs/`?
- [ ] Há pelo menos alguns KS em `ks/` cobrindo serviços, testes e CI?
- [ ] Os prompts (por IDE/ferramenta) estão versionados em `docs/prompts-library.md`?
- [ ] As instruções globais das ferramentas mencionam explicitamente `specs/` e `docs/`?
- [ ] Grandes gerações/refactors passam por testes + CI + ferramentas de AppSec?

Se a maior parte dessas respostas for "sim", você já está usando suas IDEs e AIs de forma bem alinhada ao SDD.

---

## Leituras relacionadas

- [AI-Enhanced SDD](ai-enhanced-sdd.md)
- [Guia de Migração Orientada a SDD](../migration/sdd-migration-guide.md)
- [Ferramentas de AppSec no Fluxo SDD](../security/appsec-tools.md)
- [SDD e Segurança: Security-By-Spec](../security/sdd-security.md)
- [Estrutura de um repositório de KS + IA + SDD](../fundamentals/ks-repo-structure.md)
- [Biblioteca de Prompts](../advanced/prompts-library.md)
- [Guia: SDD com Windsurf](windsurf/windsurf-sdd-guide.md)
- [Guia: SDD com Cursor](cursor/cursor-sdd-guide.md)
- [Guia: SDD com GitHub Copilot](copilot/github-copilot-sdd-guide.md)
