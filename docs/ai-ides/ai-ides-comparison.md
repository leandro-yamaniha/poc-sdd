# Comparativo: Cursor, Windsurf, GitHub Copilot, Devin e Claude Code

## Objetivo

Este documento compara, de forma prática, cinco ferramentas de IA usadas em desenvolvimento:

- **Cursor**
- **Windsurf**
- **GitHub Copilot** (IDE + interface web)
- **Devin**
- **Claude Code** (por exemplo, Claude Sonnet integrado a IDE ou editor)

O foco é mostrar **como cada uma se encaixa em um fluxo de Spec-Driven Development (SDD)**, seus pontos fortes e cuidados de segurança/uso.

Para visão geral de princípios comuns (independente da ferramenta), veja:

- [`docs/ides-sdd-overview.md`](ides-sdd-overview.md)

---

## Visão geral comparativa

| Ferramenta    | Categoria principal                                | Posição típica no fluxo SDD                          | Pontos fortes                                                         | Cuidados / limitações principais |
|---------------|----------------------------------------------------|------------------------------------------------------|------------------------------------------------------------------------|-----------------------------------|
| **Cursor**    | IDE/editor com assistente de código + agente leve | Implementação, refactors, navegação orientada a spec | Bom para trabalhar por arquivo/módulo, com contexto de projeto e specs | Evitar aceitar mudanças muito amplas sem revisar contratos e testes |
| **Windsurf**  | IDE com forte suporte a contexto de projeto/tasks | Planejamento tático, implementação e refactors       | Fluxos guiados por tasks, boa navegação de specs e docs                | Cuidar para sempre ancorar tarefas nas specs e não em suposições da IA |
| **Copilot**   | Assistente inline (IDE) + chat + sugestões em PR  | Implementação rápida, testes, refino de código       | Excelente para boilerplate, gerar testes a partir de contratos         | Não tem visão total do projeto; pode "inventar" contratos ou endpoints |
| **Devin**     | Agente de desenvolvimento mais autônomo           | Tarefas maiores: spikes, POCs, migrações repetitivas | Capaz de orquestrar várias etapas (editar código, rodar testes, etc.)  | Nunca rodar diretamente em prod; delimitar escopo e ambiente de ação |
| **Claude Code** | Assistente de código orientado a chat/análise   | Design, revisão de código, refactors mais profundos  | Muito bom para explicação, revisão crítica e brainstorming técnico     | Depende da qualidade/limite do contexto; evitar enviar dados sensíveis |

---

## Como cada ferramenta se encaixa no SDD

### Cursor

- **Papel típico no fluxo SDD**
  - Implementar endpoints, use-cases e adapters a partir de specs em `specs/`.
  - Ajudar a refatorar módulos para aderir melhor aos contratos definidos.
  - Navegar rapidamente entre código, specs e testes.
- **Forças no SDD**
  - Bom suporte a contexto de projeto, facilitando a IA entender onde aplicar mudanças.
  - Fluxo iterativo por arquivo/módulo, que combina bem com mudanças guiadas por spec.
- **Cuidados**
  - Evitar aceitar mudanças grandes em múltiplos arquivos sem revisar diffs e contratos.
  - Sempre referenciar explicitamente a spec no prompt (`veja specs/openapi.yaml`, etc.).
  - Usar CI/CD e testes de contrato como guarda-corpo para mudanças sugadas pela IA.

Para guia mais detalhado de uso de SDD em IDEs, ver:

- [`docs/ides-sdd-overview.md`](ides-sdd-overview.md)

---

### Windsurf

- **Papel típico no fluxo SDD**
  - Trabalhar com **tasks** bem definidas a partir de specs (ex.: "implementar endpoint X").
  - Navegar specs, docs de segurança e guias de migração enquanto implementa.
  - Auxiliar em refactors maiores, mas de forma incremental, um módulo por vez.
- **Forças no SDD**
  - Suporte a painéis/visões de projeto que permitem manter specs, docs e código lado a lado.
  - Bom encaixe com a ideia de **"uma task por vez"**, que combina com SDD incremental.
- **Cuidados**
  - Sempre ancorar tasks na spec: mencionar o arquivo de spec e o contrato relevante.
  - Evitar deixar a ferramenta decidir arquitetura/contratos sozinha; specs e arquitetos lideram.
  - Manter os pipelines (teste, SAST/SCA, lint) como autoridade final, não o agente.

Para detalhes específicos de uso com SDD, consulte:

- [`docs/windsurf-sdd-guide.md`](windsurf-sdd-guide.md)

---

### GitHub Copilot (IDE + Web)

- **Papel típico no fluxo SDD**
  - Ajudar na **implementação rápida** de handlers, DTOs, mapeadores, testes, etc.
  - Sugerir trechos a partir de exemplos já existentes que seguem a spec.
  - Ajudar na revisão de PRs (especialmente via interface web).
- **Forças no SDD**
  - Produtividade muito alta para boilerplate e padrões repetitivos.
  - Bom para gerar testes a partir de exemplos de request/response definidos em spec.
  - Integração com GitHub para comentários em PR e navegação de repositório.
- **Cuidados**
  - Não tem visão completa de arquitetura e contratos; pode "inventar" endpoints ou campos.
  - Sempre comparar o que ele gerou com a spec e com a política de segurança.
  - Não usar para colar conteúdo sensível em prompts ou comentários.

Para docs específicos:

- [`docs/github-copilot-sdd-guide.md`](github-copilot-sdd-guide.md)
- [`docs/github-copilot-web-guide.md`](github-copilot-web-guide.md)

---

### Devin

- **Papel típico no fluxo SDD**
  - Executar tarefas mais longas, como:
    - Migrações guiadas por spec (versão de API, libs, frameworks).
    - Spikes/POCs exploratórios, sempre com escopo controlado.
    - Implementar partes de um serviço a partir de um conjunto claro de specs e docs.
- **Forças no SDD**
  - Capacidade de orquestrar várias etapas: editar arquivos, rodar testes, ajustar configs.
  - Bom encaixe com workflows de migração incremental, especialmente se você fornecer:
    - Especificações estáveis em `specs/`
    - Guias como `docs/sdd-migration-guide.md` e `docs/appsec-tools.md`.
- **Cuidados**
  - **Nunca** apontar Devin diretamente para ambientes de produção.
  - Delimitar claramente o escopo (pastas, serviços, ambientes) antes de delegar tarefas.
  - Exigir sempre PRs revisados por humanos e pipeline de CI/CD completo.

Para mais detalhes:

- [`docs/devin-sdd-guide.md`](devin-sdd-guide.md)

---

### Claude Code (Claude Sonnet em IDE/editor)

- **Papel típico no fluxo SDD**
  - Ajudar em **design e revisão** de contratos, modelos e fluxos.
  - Explicar código legado à luz de novas specs.
  - Sugerir refactors para aproximar o código dos contratos definidos.
- **Forças no SDD**
  - Muito forte em raciocínio e explicações, útil para:
    - Entender impactos de mudar um contrato.
    - Discutir trade-offs arquiteturais usando a spec como base.
  - Bom para revisar diffs com foco em segurança, performance ou aderência à spec.
- **Cuidados**
  - Depende da qualidade e do limite do contexto fornecido; sempre indicar specs e docs.
  - Não enviar dados reais/sensíveis em prompts (seguir `docs/ai-usage-policy.md`).
  - Tratar sugestões como parecer técnico, não como verdade absoluta.

Para guia específico:

- [`docs/claude-sonnet-sdd-guide.md`](claude-sonnet-sdd-guide.md)

---

## Como escolher qual usar para cada tipo de tarefa

Algumas regras práticas:

- **Implementar endpoints, handlers e mapeadores a partir de uma spec bem definida**
  - Preferir: **Cursor** ou **Windsurf** (fluxo por arquivo/módulo, contexto de projeto).
  - Usar **Copilot** para acelerar snippets e testes locais.
- **Revisar impactos de mudança de contrato ou de spec**
  - Preferir: **Claude Code** (análise e explicação), usando a spec como verdade.
  - Apoiar-se em ferramentas de AppSec/CI para validar segurança e regressões.
- **Migrações maiores e tarefas orquestradas**
  - Considerar: **Devin**, com escopo bem limitado e specs/dcos claros.
  - Garantir que tudo passe por PR, review humano e pipelines de CI/CD.
- **Refactors localizados e melhorias em código existente**
  - Cursor/Windsurf + Copilot funcionam bem, desde que você:
    - Sempre confira com a spec.
    - Tenha testes automatizados e linting ativos.

---

## Otimizações e customizações técnicas por ferramenta

Esta seção traz **checklists técnicos** para configurar e usar cada ferramenta de forma mais eficiente em um fluxo SDD.

---

### Cursor

#### Configurações recomendadas

- [ ] **Indexação de projeto**: garantir que `specs/` e `docs/` estejam indexados para contexto (Settings → Indexing).
- [ ] **Arquivos pinados**: manter specs principais (`openapi.yaml`, `asyncapi.yaml`) e `docs/sdd-security.md` sempre abertos/pinados.
- [ ] **Regras de projeto** (`.cursor/rules` ou equivalente):
  - Criar um arquivo de regras com instruções como:
    ```
    - Sempre consultar specs/ antes de propor mudanças de contrato.
    - Não alterar endpoints sem atualizar a spec correspondente.
    - Seguir docs/ai-usage-policy.md para dados e comandos.
    ```
- [ ] **Snippets customizados**: criar snippets para padrões recorrentes (ex.: handler base, DTO, teste de contrato).
- [ ] **Integração com terminal**: configurar atalhos para rodar testes e linters após edições.

#### Integrações úteis

- [ ] Conectar ao Git para visualizar diffs antes de aceitar mudanças grandes.
- [ ] Usar extensões de linting (ESLint, Spectral) para feedback imediato.

---

### Windsurf

#### Configurações recomendadas

- [ ] **Layout de painéis**: configurar layout com:
  - Painel de specs (`specs/`)
  - Painel de docs (`docs/`)
  - Painel de chat/agent
  - Terminal integrado
- [ ] **Arquivo de regras globais** (`.windsurfrules` ou equivalente):
  - Definir regras como:
    ```
    - Toda task deve referenciar a spec correspondente.
    - Mudanças de contrato exigem atualização de spec antes de código.
    - Seguir docs/ai-usage-policy.md para segurança.
    ```
- [ ] **Memórias de projeto**: usar o recurso de memórias para guardar:
  - Padrões arquiteturais do projeto
  - Links para docs importantes
  - Decisões de design recorrentes
- [ ] **Templates de task**: criar templates de task para fluxos comuns:
  - "Implementar endpoint a partir de spec"
  - "Refatorar módulo para aderir a contrato"
  - "Migrar dependência seguindo sdd-migration-guide.md"

#### Integrações úteis

- [ ] Configurar execução automática de testes após edições (se suportado).
- [ ] Integrar com CI local (ex.: pre-commit hooks) para validar specs e código.

---

### GitHub Copilot (IDE + Web)

#### Configurações recomendadas (IDE)

- [ ] **Habilitar/desabilitar por linguagem**: ativar Copilot apenas onde é mais útil (ex.: back-end, testes), desativar em arquivos sensíveis (configs, secrets).
- [ ] **Arquivo de instruções** (`.github/copilot-instructions.md`):
  - Criar instruções de projeto:
    ```markdown
    ## Instruções para Copilot neste repositório

    - Specs de API estão em `specs/`. Sempre consultar antes de sugerir endpoints.
    - Seguir padrões de segurança em `docs/sdd-security.md`.
    - Não sugerir código que acesse dados de produção diretamente.
    - Preferir dados sintéticos em exemplos e testes.
    ```
- [ ] **Atalhos de aceitação**: configurar atalhos para aceitar parcialmente (palavra, linha) em vez de aceitar tudo.
- [ ] **Comentários de contexto**: antes de pedir implementação, escrever comentário de alto nível:
  ```typescript
  // Implementar handler GET /orders conforme specs/orders.yaml
  // Retornar 401 se não autenticado, 403 se sem permissão
  ```

#### Configurações recomendadas (Web/PRs)

- [ ] Habilitar Copilot para sugestões em PRs (Settings → Copilot → Pull Requests).
- [ ] Usar Copilot para:
  - Resumir diffs grandes
  - Sugerir testes adicionais
  - Identificar possíveis problemas de segurança

#### Integrações úteis

- [ ] Integrar com GitHub Actions para rodar SAST/SCA e mostrar resultados no PR.
- [ ] Usar Code Scanning (CodeQL) para feedback de segurança junto com sugestões do Copilot.

---

### Devin

#### Configurações recomendadas

- [ ] **Prompt inicial estruturado**: sempre iniciar sessões com um prompt que defina:
  ```
  Contexto:
  - Specs em specs/
  - Docs de segurança em docs/sdd-security.md
  - Política de uso de IA em docs/ai-usage-policy.md
  - Guia de migração em docs/sdd-migration-guide.md

  Escopo permitido:
  - Diretórios: src/, tests/, specs/
  - Ambientes: dev, staging (NUNCA produção)

  Regras:
  - Não executar comandos destrutivos sem aprovação.
  - Sempre propor plano antes de executar.
  - Gerar PRs pequenos e focados.
  ```
- [ ] **Tokens/credenciais com escopo mínimo**: configurar acesso apenas ao repositório/projeto necessário, sem permissões de admin.
- [ ] **Ambiente isolado**: rodar Devin apontando para ambiente de dev/staging, nunca produção.
- [ ] **Revisão obrigatória**: configurar para que toda mudança gere PR (nunca push direto).

#### Integrações úteis

- [ ] Integrar com CI/CD para que PRs do Devin passem por pipeline completo (testes, SAST, SCA).
- [ ] Configurar notificações para revisão humana sempre que Devin abrir PR.

---

### Claude Code (Claude Sonnet em IDE/editor)

#### Configurações recomendadas

- [ ] **System prompt / prompt inicial**: configurar um prompt de sistema ou colar no início de cada sessão:
  ```
  Você está auxiliando em um projeto que segue Spec-Driven Development (SDD).
  - Specs de API estão em specs/.
  - Documentação de segurança em docs/sdd-security.md.
  - Política de uso de IA em docs/ai-usage-policy.md.
  - Sempre que propor mudanças de contrato, referenciar a spec correspondente.
  - Não sugerir código que acesse dados de produção ou envie dados sensíveis.
  ```
- [ ] **Contexto explícito**: no início de conversas, listar arquivos que Claude deve "ler" primeiro:
  ```
  Antes de responder, considere:
  - specs/openapi.yaml
  - docs/sdd-security.md
  - docs/appsec-tools.md
  ```
- [ ] **Limite de escopo**: pedir para Claude focar em um módulo/endpoint por vez, não em refactors globais.

#### Integrações úteis

- [ ] Usar Claude para revisar diffs antes de merge (colar diff e pedir análise de segurança/aderência à spec).
- [ ] Usar Claude para gerar/revisar prompts da `prompts-library.md`.

---

### Equivalentes de workflows nas outras ferramentas

O Windsurf tem suporte nativo a **workflows** (sequências automáticas de ações). As outras ferramentas têm features similares, embora com nomes e implementações diferentes:

| Ferramenta | Feature equivalente | Descrição |
|------------|---------------------|-----------|
| **Windsurf** | Workflows (`.windsurf/workflows.yaml`) | Sequências automáticas de ações (abrir spec, propor plano, validar, etc.) |
| **Cursor** | `.cursor/rules` + Composer | Regras de projeto definem comportamento; Composer orquestra edições multi-arquivo |
| **GitHub Copilot** | `.github/copilot-instructions.md` + Actions | Instruções guiam sugestões; Actions automatizam validações em PRs |
| **Devin** | Prompt inicial estruturado | Contexto, escopo e regras definidos no início da sessão |
| **Claude Code** | System prompt + contexto explícito | Prompt de sistema define comportamento; você lista arquivos para considerar |

#### Detalhes por ferramenta

**Cursor**
- `.cursor/rules`: arquivo de regras que o agente segue automaticamente.
- Composer: modo de edição multi-arquivo para orquestrar mudanças maiores.
- Snippets: templates de código reutilizáveis.
- *Limitação*: não tem workflows automáticos, mas regras + composer + snippets cobrem boa parte.

**GitHub Copilot**
- `.github/copilot-instructions.md`: instruções que o Copilot considera ao sugerir código.
- GitHub Actions: automação de CI/CD (testes, linters, SAST/SCA) em PRs.
- Code Scanning (CodeQL): análise de segurança automática.
- *Limitação*: não tem workflows internos, mas Actions + instruções funcionam como guardrails.

**Devin**
- Prompt inicial estruturado: define contexto (specs, docs), escopo (diretórios, ambientes) e regras.
- Sessões com escopo: cada sessão pode ter um "plano de trabalho" que Devin segue.
- *Limitação*: não tem arquivo de configuração persistente, mas o prompt inicial funciona como workflow da sessão.

**Claude Code**
- System prompt: configuração inicial que define comportamento do modelo.
- Contexto explícito: no início de conversas, você lista arquivos que Claude deve considerar.
- *Limitação*: não tem automação de ações (rodar testes, linters), mas o system prompt guia análise e sugestões.

#### Comparativo de capacidades

| Capacidade | Windsurf | Cursor | Copilot | Devin | Claude Code |
|------------|----------|--------|---------|-------|-------------|
| Arquivo de regras | `.windsurfrules` | `.cursor/rules` | `.github/copilot-instructions.md` | Prompt inicial | System prompt |
| Workflows automáticos | ✅ Sim | ❌ Manual (Composer) | ❌ Via Actions | ❌ Via prompt | ❌ Não |
| Validação automática | ✅ Sim | ⚠️ Parcial (terminal) | ✅ Sim (Actions) | ✅ Pode rodar testes | ❌ Não |
| Memórias de projeto | ✅ Sim | ⚠️ Parcial | ❌ Não | ❌ Não | ❌ Não |
| Orquestração multi-arquivo | ✅ Sim | ✅ Composer | ⚠️ Parcial | ✅ Sim | ❌ Não |

Para detalhes sobre workflows no Windsurf, veja:
- [`docs/windsurf-workflows.md`](windsurf-workflows.md)

---

### Estratégias para simular workflows nas outras ferramentas

Como Cursor, Copilot, Devin e Claude Code não têm workflows nativos, você pode simular comportamentos similares com as estratégias abaixo.

---

#### Cursor: simulando workflows

**1. Arquivo `.cursor/rules` como "workflow implícito"**

Crie um arquivo de regras que force o agente a seguir passos:

```text
# Regras de projeto (SDD)

## Antes de qualquer implementação:
1. Abrir e ler a spec correspondente em specs/.
2. Confirmar que entendeu o contrato antes de propor código.

## Durante implementação:
3. Implementar um endpoint/módulo por vez.
4. Após cada edição, sugerir rodar testes e linters.

## Antes de commit:
5. Verificar se a spec está atualizada.
6. Listar arquivos alterados e pedir confirmação.

## Se detectar mudança de contrato:
7. Bloquear e pedir para atualizar a spec primeiro.
```

**2. Composer como orquestrador manual**

Use o Composer para criar "sessões de workflow":
- Abra os arquivos relevantes (spec, código, testes).
- Peça ao Composer para seguir os passos do seu workflow.
- Exemplo de prompt:

```text
Vamos implementar o endpoint GET /orders.
Siga estes passos:
1. Leia specs/orders.yaml e confirme o contrato.
2. Proponha um plano de implementação.
3. Implemente o handler.
4. Gere testes de contrato.
5. Liste comandos para rodar testes e linters.
```

**3. Snippets como templates de workflow**

Crie snippets que já incluem os passos:

```json
{
  "SDD Implementation Flow": {
    "prefix": "sdd-impl",
    "body": [
      "// Workflow: Implementar endpoint",
      "// 1. Spec: specs/${1:recurso}.yaml",
      "// 2. Contrato: ${2:método} ${3:path}",
      "// 3. Handler: src/handlers/${1}.ts",
      "// 4. Testes: tests/${1}.test.ts",
      "// 5. Validar: npm test && npm run lint"
    ]
  }
}
```

---

#### GitHub Copilot: simulando workflows

**1. Arquivo `.github/copilot-instructions.md` como guia**

```markdown
# Instruções para Copilot neste repositório

## Fluxo de implementação
1. Sempre consultar specs/ antes de sugerir endpoints.
2. Seguir padrões de segurança em docs/sdd-security.md.
3. Preferir dados sintéticos em exemplos e testes.
4. Sugerir rodar testes após implementações.

## Fluxo de mudança de contrato
1. Se a mudança altera o contrato, avisar o desenvolvedor.
2. Sugerir atualizar a spec antes do código.

## Fluxo de PR
1. Gerar descrição baseada nos diffs.
2. Listar specs afetadas.
3. Sugerir testes adicionais se necessário.
```

**2. GitHub Actions como validação automática**

Crie workflows de CI que simulam os passos de validação:

```yaml
# .github/workflows/sdd-validation.yml
name: SDD Validation

on: [pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Lint specs
        run: npx @stoplight/spectral-cli lint specs/*.yaml

      - name: Run tests
        run: npm test

      - name: Run SAST
        run: npm run sast

      - name: Check contract tests
        run: npm run test:contract
```

**3. Comentários estruturados como gatilhos**

Use comentários padronizados para guiar o Copilot:

```typescript
// SDD-WORKFLOW: Implementar endpoint
// SPEC: specs/orders.yaml
// CONTRATO: GET /orders -> 200: Order[], 401, 403
// SEGURANÇA: Requer autenticação, verificar permissões
// TESTES: Gerar testes para 200, 401, 403

export async function getOrders(req: Request, res: Response) {
  // Copilot vai usar o contexto acima para sugerir
}
```

---

#### Devin: simulando workflows

**1. Prompt inicial estruturado como "workflow da sessão"**

Sempre inicie sessões com um prompt que define o fluxo:

```text
# Contexto
- Specs em specs/
- Docs de segurança em docs/sdd-security.md
- Política de uso em docs/ai-usage-policy.md

# Workflow para esta sessão
1. Ler a spec correspondente antes de qualquer implementação.
2. Propor plano de implementação e aguardar aprovação.
3. Implementar em passos pequenos (um endpoint/módulo por vez).
4. Após cada passo, rodar testes e linters.
5. Se detectar mudança de contrato, parar e pedir para atualizar spec.
6. Ao final, gerar PR com descrição detalhada.

# Escopo permitido
- Diretórios: src/, tests/, specs/
- Ambientes: dev, staging (NUNCA produção)

# Regras
- Não executar comandos destrutivos sem aprovação.
- Sempre mostrar diff antes de aplicar mudanças.
```

**2. Checkpoints manuais**

Peça para Devin parar em pontos específicos:

```text
Implemente o endpoint GET /orders seguindo o workflow.
PARE e me mostre o plano antes de começar.
PARE após implementar o handler e antes de gerar testes.
PARE antes de fazer commit.
```

**3. Templates de task reutilizáveis**

Mantenha templates de prompts para tarefas comuns:

```text
# Template: Implementar endpoint

Task: Implementar {{método}} {{path}}

Workflow:
1. [ ] Ler specs/{{recurso}}.yaml
2. [ ] Propor plano (aguardar aprovação)
3. [ ] Implementar handler
4. [ ] Implementar validações
5. [ ] Gerar testes de contrato
6. [ ] Rodar testes e linters
7. [ ] Mostrar diff final
8. [ ] Criar PR
```

---

#### Claude Code: simulando workflows

**1. System prompt como "workflow persistente"**

Configure um system prompt que define o fluxo:

```text
Você é um assistente de desenvolvimento que segue Spec-Driven Development (SDD).

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
```

**2. Prompts estruturados como "passos de workflow"**

Use prompts que forçam o fluxo:

```text
Vamos implementar o endpoint GET /orders.

Siga este workflow:
1. Primeiro, me diga o que você entendeu do contrato em specs/orders.yaml.
2. Depois, proponha um plano de implementação.
3. Aguarde minha aprovação antes de gerar código.
4. Após implementar, sugira testes.
5. Ao final, faça uma revisão de segurança.
```

**3. Checklists como validação manual**

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

### Resumo: como simular workflows em cada ferramenta

| Ferramenta | Estratégia principal | Estratégias complementares |
|------------|----------------------|----------------------------|
| **Cursor** | `.cursor/rules` com passos obrigatórios | Composer como orquestrador, snippets de workflow |
| **Copilot** | `.github/copilot-instructions.md` + Actions | Comentários estruturados, Code Scanning |
| **Devin** | Prompt inicial estruturado | Checkpoints manuais, templates de task |
| **Claude Code** | System prompt com workflow | Prompts estruturados, checklists de validação |

---

### Memórias de projeto: como simular e fazer backup

O Windsurf tem suporte nativo a **memórias de projeto**. As outras ferramentas não têm, mas você pode simular e versionar com as estratégias abaixo.

---

#### Cursor: simulando memórias

**1. Arquivo `.cursor/rules` como memória persistente**

O arquivo de regras funciona como memória que o Cursor carrega automaticamente:

```text
# .cursor/rules

## Contexto do projeto
- Projeto: API de Orders
- Stack: Node.js + TypeScript + Fastify
- Specs: OpenAPI 3.1 em specs/

## Padrões arquiteturais
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
```

**2. Arquivo de backup versionado**

Crie `docs/cursor-memories.md` para backup:

```markdown
# Memórias do Projeto (Backup Cursor)

## Última atualização
Data: YYYY-MM-DD

## Contexto
- Stack: Node.js + TypeScript + Fastify
- Specs: specs/

## Padrões
- Clean Architecture
- Handlers seguem templates de ks/

## Decisões
- JWT, rate limiting no gateway, logs JSON

## Como restaurar
1. Copie o conteúdo para .cursor/rules
2. Ou cole no chat do Cursor no início da sessão
```

---

#### GitHub Copilot: simulando memórias

**1. Arquivo `.github/copilot-instructions.md` como memória**

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
- Preferir dados sintéticos em exemplos.
```

**2. Arquivo de backup versionado**

Crie `docs/copilot-memories.md` com o mesmo conteúdo, para referência e restauração.

**3. Comentários no código como memória local**

```typescript
/**
 * @project API de Orders
 * @stack Node.js + TypeScript + Fastify
 * @spec specs/orders.yaml
 * @patterns Clean Architecture, JWT auth, JSON logs
 */
```

---

#### Devin: simulando memórias

**1. Prompt inicial como memória de sessão**

Sempre inicie sessões com um prompt que define o contexto:

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

## Regras
- Sempre ler a spec antes de implementar.
- Não acessar produção.
- Gerar PRs pequenos e focados.
```

**2. Arquivo de backup versionado**

Crie `docs/devin-memories.md` com o prompt acima, para copiar e colar no início de cada sessão.

**3. Template de sessão reutilizável**

Mantenha templates de prompts em `docs/prompts-library.md` ou em arquivo separado.

---

#### Claude Code: simulando memórias

**1. System prompt como memória persistente**

Configure um system prompt que define o contexto:

```text
Você é um assistente de desenvolvimento para o projeto API de Orders.

## Contexto
- Stack: Node.js + TypeScript + Fastify
- Specs: OpenAPI 3.1 em specs/
- Docs: docs/

## Padrões
- Clean Architecture
- Handlers seguem templates de ks/

## Decisões
- JWT para autenticação
- Rate limiting no gateway
- Logs em JSON

## Regras
- Sempre consultar specs/ antes de sugerir código.
- Seguir docs/sdd-security.md para segurança.
- Não sugerir código que acesse produção.
```

**2. Arquivo de backup versionado**

Crie `docs/claude-memories.md` com o system prompt acima.

**3. Contexto explícito no início de conversas**

```text
Antes de começar, considere o contexto em docs/claude-memories.md.
Leia também:
- specs/orders.yaml
- docs/sdd-security.md
```

---

### Resumo: memórias por ferramenta

| Ferramenta | Onde armazenar | Como fazer backup |
|------------|----------------|-------------------|
| **Windsurf** | Painel de memórias + `.windsurfrules` | `docs/windsurf-memories.md` |
| **Cursor** | `.cursor/rules` | `docs/cursor-memories.md` |
| **Copilot** | `.github/copilot-instructions.md` | `docs/copilot-memories.md` |
| **Devin** | Prompt inicial de sessão | `docs/devin-memories.md` |
| **Claude Code** | System prompt | `docs/claude-memories.md` |

### Estrutura sugerida para backups

```
docs/
├── windsurf-memories.md   # Backup Windsurf
├── cursor-memories.md     # Backup Cursor
├── copilot-memories.md    # Backup Copilot
├── devin-memories.md      # Backup Devin
├── claude-memories.md     # Backup Claude Code
└── prompts-library.md     # Prompts reutilizáveis
```

### Checklist de manutenção de memórias

- [ ] Revisar memórias semanalmente.
- [ ] Atualizar arquivos de backup após mudanças importantes.
- [ ] Sincronizar arquivos de regras (`.cursor/rules`, `.windsurfrules`, etc.) com os backups.
- [ ] Compartilhar backups com novos membros do time.

---

### Checklist geral de setup (para qualquer ferramenta)

- [ ] `specs/` organizado e atualizado (OpenAPI, AsyncAPI, GraphQL).
- [ ] `docs/` com pelo menos:
  - `sdd-security.md`
  - `ai-usage-policy.md`
  - `ai-risks-and-sdd-mitigation.md`
- [ ] Arquivo de regras/instruções específico da ferramenta (`.cursor/rules`, `.windsurfrules`, `.github/copilot-instructions.md`, etc.).
- [ ] CI/CD com:
  - Testes automatizados
  - Linting de specs (Spectral, Redocly)
  - SAST/SCA
- [ ] Tokens/credenciais de IA com escopo mínimo, sem acesso a produção.
- [ ] Revisão humana obrigatória para PRs gerados ou assistidos por IA.

---

## Conexão com outros documentos

Este comparativo complementa:

- [ides-sdd-overview.md](ides-sdd-overview.md) – Princípios comuns de uso de IA em IDEs com SDD
- Guias específicos por ferramenta:
  - [windsurf-sdd-guide.md](windsurf/windsurf-sdd-guide.md)
  - [cursor-sdd-guide.md](cursor/cursor-sdd-guide.md)
  - [github-copilot-sdd-guide.md](copilot/github-copilot-sdd-guide.md)
  - [github-copilot-web-guide.md](copilot/github-copilot-web-guide.md)
  - [devin-sdd-guide.md](devin/devin-sdd-guide.md)
  - [claude-sonnet-sdd-guide.md](claude/claude-sonnet-sdd-guide.md)
- [ai-usage-policy.md](../security/ai-usage-policy.md) – Política de uso seguro de IA
- [ai-risks-and-sdd-mitigation.md](../security/ai-risks-and-sdd-mitigation.md) – Riscos de IA e mitigação
