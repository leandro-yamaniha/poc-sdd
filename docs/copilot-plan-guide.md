# Guia: Plan Agent e Todo List no GitHub Copilot

## Objetivo

Este documento explica como usar o **Plan agent** e a **Todo list** do GitHub Copilot no VS Code para planejar e executar tarefas de desenvolvimento de forma estruturada, especialmente em projetos que seguem **Spec-Driven Development (SDD)**.

Baseado na documentação oficial: [Planning in VS Code chat](https://code.visualstudio.com/docs/copilot/chat/chat-planning)

---

## O que é o Plan agent

O **Plan agent** é um agente integrado ao Copilot Chat que colabora com você para criar **planos de implementação detalhados** antes de executar qualquer código.

### Características principais

- **Pesquisa a tarefa** usando ferramentas read-only e análise do codebase
- **Identifica requisitos e restrições** antes de propor mudanças
- **Quebra a tarefa** em passos gerenciáveis e acionáveis
- **Apresenta um plano** em formato padronizado para revisão
- **Não faz mudanças de código** até o plano ser aprovado por você

---

## Como usar o Plan agent

### 1. Abrir o Plan agent

1. Abra o **Chat view** no VS Code:
   - macOS: `Ctrl+Cmd+I`
   - Windows/Linux: `Ctrl+Alt+I`
2. Selecione **Plan** no dropdown de agentes (no topo do painel)

### 2. Descrever a tarefa

Digite uma tarefa de alto nível, como:

- "Implementar um sistema de autenticação com OAuth2 e JWT"
- "Adicionar testes unitários para todos os endpoints da API"
- "Corrigir bug: o formulário não limpa os campos após salvar"

### 3. Revisar o plano proposto

O Plan agent vai gerar:

- **Resumo de alto nível** da tarefa
- **Breakdown de passos** com ações específicas
- **Perguntas abertas** para esclarecer requisitos ambíguos

### 4. Iterar no plano

Você pode refinar o plano antes de executar:

- "Adicione validação de autenticação no passo 2"
- "Remova o passo 4, não precisamos disso"
- "Esclareça: usamos JWT ou session-based auth?"

> **Dica**: Fique no modo Plan para refinar o plano antes de implementar. Você pode iterar várias vezes para clarificar requisitos, ajustar escopo ou resolver perguntas abertas.

### 5. Executar o plano

Quando o plano estiver finalizado, você pode:

- **Implementar na mesma sessão**: o agente executa passo a passo
- **Iniciar sessão em background**: o agente trabalha autonomamente
- **Abrir no editor**: para revisão adicional ou discussão com o time

Ao iniciar a implementação, você ainda pode dar instruções como:

- "Comece pela UI"
- "Execute apenas os passos 1 e 2"

---

## Todo list: acompanhando o progresso

Quando você trabalha em tarefas complexas, o agente cria uma **todo list** para acompanhar o progresso.

### Como funciona

- A todo list quebra sua solicitação em **tarefas individuais**
- Atualiza **automaticamente** conforme o agente completa cada passo
- Ajuda você a **monitorar o progresso**
- Mantém o agente **focado** durante tarefas longas

### Interagindo com a todo list

Você pode atualizar a lista usando linguagem natural:

- "Revise o passo 1 para fazer X"
- "Adicione outra tarefa"
- "Remova o passo 3"

Se a lista não estiver como esperado, você pode limpá-la e recomeçar.

---

## Usando Plan agent com SDD

Para integrar o Plan agent ao seu fluxo de Spec-Driven Development:

### Prompt estruturado para planejamento SDD

```text
Crie um plano para implementar o endpoint GET /orders.

Contexto:
- Spec: specs/orders.yaml (fonte de verdade)
- Padrões de segurança: docs/sdd-security.md
- Templates: ks/services/node/rest-service-template/

O plano deve incluir:
1. Leitura e validação da spec
2. Implementação do handler seguindo o template
3. Validações de entrada e segurança
4. Testes de contrato
5. Validação final (testes, linters)

Antes de propor código, confirme que entendeu o contrato da spec.
```

### Exemplo de plano gerado

```text
Plano: Implementar GET /orders

Resumo:
Implementar endpoint REST para listar orders conforme spec OpenAPI.

Passos:
1. [ ] Ler specs/orders.yaml e confirmar contrato
2. [ ] Criar handler em src/handlers/orders.ts
3. [ ] Implementar DTO em src/dtos/order.dto.ts
4. [ ] Adicionar validação de autenticação (JWT)
5. [ ] Adicionar rota no router
6. [ ] Criar testes de contrato em tests/orders.test.ts
7. [ ] Rodar testes e linters
8. [ ] Revisar diff final

Perguntas abertas:
- A paginação deve ser obrigatória ou opcional?
- Qual o limite máximo de itens por página?
```

### Checkpoints manuais

Peça para o agente parar em pontos específicos:

```text
Execute o plano, mas:
- PARE após o passo 1 para eu confirmar o contrato
- PARE após o passo 6 para eu revisar os testes
- PARE antes de fazer commit
```

---

## Comparação com outras ferramentas

| Aspecto | Copilot Plan | Windsurf Workflows | Cursor Composer |
|---------|--------------|-------------------|-----------------|
| Planejamento automático | ✅ Nativo | ✅ Nativo | ⚠️ Manual |
| Aprovação antes de executar | ✅ Sim | ✅ Sim | ⚠️ Parcial |
| Todo list integrada | ✅ Sim | ⚠️ Parcial | ❌ Não |
| Execução em background | ✅ Sim | ⚠️ Parcial | ❌ Não |
| Perguntas abertas | ✅ Sim | ⚠️ Parcial | ❌ Não |

---

## Boas práticas

### Antes de usar o Plan agent

- [ ] Ter a spec atualizada em `specs/`
- [ ] Ter docs de segurança e padrões acessíveis
- [ ] Configurar `.github/copilot-instructions.md` com contexto do projeto

### Durante o planejamento

- [ ] Revisar cada passo do plano antes de aprovar
- [ ] Esclarecer perguntas abertas antes de executar
- [ ] Ajustar escopo se o plano estiver muito amplo

### Durante a execução

- [ ] Usar checkpoints para revisar em pontos críticos
- [ ] Monitorar a todo list para acompanhar progresso
- [ ] Intervir se o agente desviar do plano

### Após a execução

- [ ] Revisar diffs gerados
- [ ] Rodar testes e linters
- [ ] Validar aderência à spec

---

## Recursos relacionados

- [Documentação oficial: Planning in VS Code chat](https://code.visualstudio.com/docs/copilot/chat/chat-planning)
- [Configure tools for agents in VS Code chat](https://code.visualstudio.com/docs/copilot/chat/chat-tools)
- [Context engineering user guide](https://code.visualstudio.com/docs/copilot/guides/context-engineering-guide)

---

## Conexão com outros documentos

- [`docs/github-copilot-sdd-guide.md`](github-copilot-sdd-guide.md) – Guia geral de uso do Copilot com SDD
- [`docs/copilot-memories.md`](copilot-memories.md) – Backup de instruções/memórias do Copilot
- [`docs/ai-ides-comparison.md`](ai-ides-comparison.md) – Comparativo de ferramentas de IA
- [`docs/ai-usage-policy.md`](ai-usage-policy.md) – Política de uso seguro de IA
