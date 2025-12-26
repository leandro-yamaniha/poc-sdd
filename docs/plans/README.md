# Planos de Implementação

Esta pasta armazena **backups de planos** gerados por ferramentas de IA (Copilot Plan, Windsurf, etc.).

## Estrutura sugerida

```
plans/
├── README.md
├── 2025-01-15-orders-endpoint.md
├── 2025-01-20-auth-refactor.md
└── 2025-01-25-migration-v2.md
```

## Nomenclatura

Use o formato: `YYYY-MM-DD-nome-da-tarefa.md`

## Template de plano

```markdown
# Plano: [Nome da tarefa]

**Data**: YYYY-MM-DD  
**Status**: Pendente | Em andamento | Concluído  
**Ferramenta**: Copilot Plan | Windsurf | Cursor | Devin | Claude  
**Autor**: Nome

---

## Resumo

[Resumo de alto nível da tarefa]

---

## Contexto

- **Spec**: specs/[recurso].yaml
- **Docs**: docs/security/sdd-security.md

---

## Passos

1. [ ] Passo 1
2. [ ] Passo 2
3. [ ] Passo 3

---

## Perguntas abertas

- Pergunta 1?
- Pergunta 2?

---

## Notas de execução

[Adicionar notas durante a execução]
```

## Índice de planos

| Data | Nome | Status | Ferramenta |
|------|------|--------|------------|
| _YYYY-MM-DD_ | _Nome do plano_ | _Status_ | _Ferramenta_ |
