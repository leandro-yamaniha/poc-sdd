# Guia: Como usar SDD com GitHub Copilot

## Objetivo

Aproveitar o **GitHub Copilot (inline + Chat)** em um projeto SDD, garantindo que:

- A spec e os docs em `docs/` guiem as sugestões
- As gerações sejam pequenas, verificáveis e alinhadas à arquitetura

---

## Setup mínimo

- Repositório com:
  - `specs/` – contratos versionados
  - `docs/` – SDD, segurança, migração, KS, prompts
  - `ks/` – templates de código e CI
- Copilot habilitado no seu editor (VS Code, Neovim, JetBrains etc.)

---

## Padrões de uso

1. **Inline completions para trechos pequenos**

   Bom para:

   - Preencher handlers já esboçados a partir de uma spec
   - Gerar partes repetitivas (DTOs, mapeamentos simples)

   Sempre revisando linha a linha.

2. **Copilot Chat para contexto maior**

   Útil para:

   - Entender uma parte do sistema com base na spec
   - Pedir exemplos de uso, testes, pequenos refactors

---

## Fluxos recomendados

### Fluxo 1: Implementar endpoint a partir de spec

1. Leia `specs/...` do endpoint que você vai implementar.
2. No arquivo de código, escreva o esqueleto (assinatura da função, nome do handler, comentários).
3. Use o Copilot inline ou Chat com algo como:

   > "Implemente este handler seguindo o contrato em `specs/orders-openapi.yaml` para o endpoint `/orders/{id}`.  
   > Não invente campos, siga status codes e formatos definidos na spec.  
   > Respeite também as recomendações de validação e segurança vistas em `docs/sdd-security.md`."

4. Valide com testes e linters.

### Fluxo 2: Geração de testes

1. Abra o arquivo de testes desejado.
2. Peça no Chat:

   > "Com base em `specs/orders-openapi.yaml` e na implementação atual de `/orders`, gere testes de contrato e casos de regressão para:  
   > - sucesso básico  
   > - parâmetros inválidos  
   > - erro de autorização  
   > Siga as boas práticas descritas em `docs/sdd-migration-guide.md` para cenários críticos."

3. Ajuste manualmente, execute a suite e revise.

---

## Customizações recomendadas

### Instruções globais (Custom Instructions / Copilot Settings)

Sugestões de texto:

- "Este repositório usa Spec-Driven Development. Sempre que possível, baseie sugestões em `specs/` e `docs/`."
- "Evite criar endpoints ou campos que não estejam na spec. Se achar necessário, sugira mudanças primeiro."
- "Considere também as práticas descritas em `docs/sdd-security.md` e `docs/appsec-tools.md` ao sugerir código sensível."

### Snippets/comandos reutilizáveis

Guardar no próprio editor (ou em `docs/prompts-library.md`) prompts padrão para:

- Implementação de endpoint a partir de spec
- Criação de testes (unitários, integração, contrato)
- Migração de código (com base em `docs/sdd-migration-guide.md`)

---

## Boas práticas

- Usar Copilot para:
  - Automação de "boilerplate"
  - Esboço de testes
  - Ajuda em refactors locais
- NÃO usar Copilot para:
  - Decidir contratos de API sozinho
  - Alterar spec sem discussão/diff revisado
- Manter a **spec e os docs como autoridade**; Copilot é só o executor acelerado.

---

## Plan agent e Todo list

O Copilot tem um recurso de **Plan agent** que permite criar planos detalhados antes de implementar, similar aos workflows do Windsurf.

Com o Plan agent você pode:

- Descrever uma tarefa de alto nível
- Receber um plano com passos e perguntas abertas
- Iterar no plano antes de aprovar
- Executar passo a passo com acompanhamento via todo list

Para detalhes completos, veja:

- [`docs/copilot-plan-guide.md`](copilot-plan-guide.md) – Guia do Plan agent e Todo list

---

## Conexão com outros documentos

- [`docs/copilot-memories.md`](copilot-memories.md) – Backup de instruções/memórias do Copilot
- [`docs/copilot-plan-guide.md`](copilot-plan-guide.md) – Guia do Plan agent e Todo list
- [`docs/github-copilot-web-guide.md`](github-copilot-web-guide.md) – Uso do Copilot na interface web
- [`docs/ai-ides-comparison.md`](ai-ides-comparison.md) – Comparativo de ferramentas de IA
- [`docs/ai-usage-policy.md`](ai-usage-policy.md) – Política de uso seguro de IA
