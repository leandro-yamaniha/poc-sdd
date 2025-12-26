# Workflows no Windsurf para Spec-Driven Development

## Objetivo

Este documento explica como configurar e usar **workflows** no Windsurf para padronizar o fluxo de trabalho em projetos que seguem **Spec-Driven Development (SDD)**.

Workflows são sequências de ações que o Windsurf executa automaticamente ou sob demanda, garantindo consistência, segurança e produtividade.

---

## O que são workflows

Workflows são "scripts de comportamento" que guiam o agente e a IDE para:

- Seguir o fluxo SDD de forma consistente.
- Executar validações automaticamente (testes, linters, SAST).
- Bloquear ações perigosas sem confirmação.
- Reduzir trabalho manual repetitivo.

---

## Ganhos de configurar workflows

| Aspecto | Sem workflow | Com workflow configurado |
|---------|--------------|--------------------------|
| Consistência | Depende da memória do dev | Garantida pelo fluxo |
| Segurança | Risco de pular validações | Guardrails automáticos |
| Produtividade | Overhead manual | Foco no problema |
| Onboarding | Curva de aprendizado | Fluxo guiado desde o início |

---

## Tipos de workflows úteis para SDD

### 1. Workflow de inicialização de projeto

**Gatilho**: ao abrir o projeto no Windsurf.

**Ações**:

- Indexar `specs/` e `docs/`.
- Carregar memórias de projeto (padrões, decisões, links).
- Mostrar painel com specs principais e docs de segurança.

**Ganho**: contexto SDD disponível desde o início da sessão.

---

### 2. Workflow de task de implementação

**Gatilho**: usuário cria task como "implementar GET /orders".

**Ações**:

1. Abrir a spec correspondente (`specs/orders.yaml`).
2. Mostrar o contrato do endpoint.
3. Propor plano de implementação (passos pequenos).
4. Pedir confirmação antes de editar.
5. Implementar em passos incrementais.
6. Rodar testes e linters após cada passo.
7. Mostrar diff final para revisão.

**Ganho**: implementação sempre ancorada na spec, com validação contínua.

---

### 3. Workflow de validação antes de commit

**Gatilho**: usuário pede para commitar ou criar PR.

**Ações**:

1. Rodar testes automatizados.
2. Rodar linters de código (ESLint, Prettier, etc.).
3. Rodar linters de spec (Spectral, Redocly).
4. Rodar secret scanning.
5. Mostrar resumo de validação.
6. Bloquear commit se houver falhas críticas.

**Ganho**: nenhum código quebrado ou inseguro entra no repositório.

---

### 4. Workflow de atualização de contrato

**Gatilho**: agente detecta que a mudança proposta altera o contrato (campos, endpoints, status codes).

**Ações**:

1. Bloquear edição de código.
2. Pedir para atualizar a spec primeiro.
3. Validar a spec atualizada.
4. Liberar edição de código.
5. Atualizar testes de contrato.

**Ganho**: spec sempre é a fonte de verdade, nunca fica desatualizada.

---

### 5. Workflow de criação de PR

**Gatilho**: usuário pede para abrir PR.

**Ações**:

1. Gerar descrição baseada nos diffs.
2. Listar specs afetadas.
3. Sugerir reviewers.
4. Rodar pipeline de CI.
5. Adicionar labels automáticas (ex.: `breaking-change` se contrato mudou).

**Ganho**: PRs bem documentados e prontos para revisão.

---

### 6. Workflow de migração

**Gatilho**: usuário cria task de migração (ex.: "migrar de Express para Fastify").

**Ações**:

1. Carregar `docs/sdd-migration-guide.md`.
2. Identificar endpoints/módulos afetados via specs.
3. Propor plano de migração incremental.
4. Executar migração um módulo por vez.
5. Rodar testes de contrato após cada passo.
6. Validar que a spec continua sendo respeitada.

**Ganho**: migrações seguras, incrementais e rastreáveis.

---

## Como configurar workflows

### Opção 1: Arquivo de configuração

Dependendo da versão do Windsurf, você pode criar um arquivo como `.windsurf/workflows.yaml`:

```yaml
workflows:
  - name: "Inicialização de projeto"
    trigger: "project:open"
    steps:
      - action: "index_directories"
        paths: ["specs/", "docs/"]
      - action: "load_memories"
      - action: "show_panel"
        files: ["specs/openapi.yaml", "docs/sdd-security.md"]

  - name: "Implementar endpoint"
    trigger: "task:create"
    match: "implementar*"
    steps:
      - action: "open_file"
        path: "specs/{{endpoint}}.yaml"
      - action: "show_contract"
      - action: "propose_plan"
      - action: "await_confirmation"
      - action: "implement"
      - action: "run_tests"
      - action: "run_linters"
      - action: "show_diff"

  - name: "Validar antes de commit"
    trigger: "git:pre-commit"
    steps:
      - action: "run_tests"
      - action: "run_linters"
        targets: ["code", "specs"]
      - action: "run_secret_scan"
      - action: "show_summary"
      - action: "block_if_failed"

  - name: "Atualizar contrato"
    trigger: "contract:change_detected"
    steps:
      - action: "block_code_edit"
      - action: "prompt_spec_update"
      - action: "validate_spec"
      - action: "unblock_code_edit"
      - action: "update_contract_tests"

  - name: "Criar PR"
    trigger: "pr:create"
    steps:
      - action: "generate_description"
      - action: "list_affected_specs"
      - action: "suggest_reviewers"
      - action: "run_ci"
      - action: "add_labels"
```

---

### Opção 2: Memórias de projeto

Se o Windsurf não suportar arquivo de workflows, você pode usar **memórias de projeto** para guardar instruções que o agente segue automaticamente:

```
Memória: Fluxo SDD

Ao iniciar qualquer task de implementação:
1. Abrir a spec correspondente em specs/.
2. Mostrar o contrato do endpoint/evento.
3. Propor plano de implementação em passos pequenos.
4. Pedir confirmação antes de editar.
5. Após cada edição, rodar testes e linters.
6. Mostrar diff final para revisão.

Ao detectar mudança de contrato:
1. Bloquear edição de código.
2. Pedir para atualizar a spec primeiro.
3. Validar a spec.
4. Liberar edição de código.

Antes de commit:
1. Rodar testes.
2. Rodar linters de código e spec.
3. Rodar secret scanning.
4. Bloquear se houver falhas.
```

---

### Opção 3: Templates de task

Criar templates de task que já incluem os passos do workflow:

**Template: Implementar endpoint**

```
Task: Implementar {{método}} {{path}}

Passos:
1. [ ] Abrir specs/{{recurso}}.yaml
2. [ ] Revisar contrato do endpoint
3. [ ] Propor plano de implementação
4. [ ] Implementar handler
5. [ ] Implementar DTOs/validações
6. [ ] Implementar testes
7. [ ] Rodar testes e linters
8. [ ] Revisar diff
9. [ ] Commit
```

**Template: Migração de módulo**

```
Task: Migrar {{módulo}} de {{origem}} para {{destino}}

Passos:
1. [ ] Ler docs/sdd-migration-guide.md
2. [ ] Identificar endpoints afetados via specs/
3. [ ] Criar branch de migração
4. [ ] Migrar um endpoint por vez
5. [ ] Rodar testes de contrato após cada passo
6. [ ] Validar aderência à spec
7. [ ] Revisar diff completo
8. [ ] Abrir PR
```

---

## Checklist de configuração de workflows

- [ ] Definir workflows para os fluxos mais comuns (implementação, validação, PR, migração).
- [ ] Configurar via arquivo `.windsurf/workflows.yaml` ou memórias de projeto.
- [ ] Criar templates de task para padronizar o início de trabalhos.
- [ ] Testar workflows em tarefas reais antes de adotar no time.
- [ ] Documentar workflows customizados para onboarding de novos devs.

---

## Conexão com outros documentos

- [`docs/windsurf-sdd-guide.md`](windsurf-sdd-guide.md) – Guia geral de uso do Windsurf com SDD
- [`docs/ai-ides-comparison.md`](ai-ides-comparison.md) – Comparativo de ferramentas de IA
- [`docs/sdd-migration-guide.md`](sdd-migration-guide.md) – Guia de migração orientada a SDD
- [`docs/ai-usage-policy.md`](ai-usage-policy.md) – Política de uso seguro de IA
