# Workflow Spec Kit: SDD com IA em 4 Fases

## Visão Geral

O **[Spec Kit](https://github.com/github/spec-kit)** é um toolkit open source do GitHub que implementa um workflow estruturado de Spec-Driven Development otimizado para trabalhar com agentes de IA (GitHub Copilot, Claude Code, Gemini CLI, etc.).

> **Fonte**: Este documento é baseado no artigo [Spec-Driven Development with AI](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/) do GitHub Blog.

## O Problema que Resolve

Prompts vagos como "adicione compartilhamento de fotos no meu app" forçam o modelo de IA a adivinhar milhares de requisitos não declarados. O resultado são suposições razoáveis, mas algumas estarão erradas (e você só descobrirá tarde na implementação).

**Spec Kit resolve isso** fornecendo um processo estruturado onde a especificação, o plano técnico e as tarefas dão clareza ao agente de IA, melhorando drasticamente sua eficácia.

---

## As 4 Fases do Workflow

### Fase 1: Specify (Especificar)

**Objetivo**: Criar uma especificação detalhada a partir de uma descrição de alto nível.

#### O que você faz:
- Fornece uma descrição de alto nível do que está construindo e por quê
- Foca no **"o quê"** e **"por quê"**, não nos detalhes técnicos
- Descreve jornadas de usuário, experiências e resultados desejados

#### Perguntas-chave a responder:
- Quem vai usar isso?
- Que problema resolve para eles?
- Como eles vão interagir?
- Quais resultados importam?

#### O que o agente de IA faz:
- Gera uma especificação detalhada baseada na sua descrição
- Expande os detalhes mantendo foco na experiência do usuário
- Cria um artefato vivo que evolui conforme você aprende mais

#### Comando:
```bash
/specify
```

**Exemplo de prompt**:
```
Estou construindo um sistema de notificações para um app SaaS.

Usuários: Administradores de equipes que precisam alertar membros sobre eventos importantes.

Problema: Atualmente, notificações são enviadas apenas por email, causando atrasos e perda de informações críticas.

Interação: Usuários devem poder criar templates de notificação, escolher canais (email, push, SMS), agendar envios e ver histórico de entregas.

Resultados: 95% das notificações entregues em menos de 30 segundos, usuários podem optar por não receber certos tipos de notificação.
```

---

### Fase 2: Plan (Planejar)

**Objetivo**: Criar um plano técnico detalhado respeitando arquitetura e restrições.

#### O que você faz:
- Fornece direção técnica de alto nível
- Define stack desejada, arquitetura e constraints
- Especifica padrões da empresa, integrações com sistemas legados, requisitos de compliance

#### Informações a incluir:
- Tecnologias padronizadas na empresa
- Integrações com sistemas existentes
- Requisitos de compliance e segurança
- Metas de performance
- Padrões arquiteturais internos

#### O que o agente de IA faz:
- Gera um plano técnico abrangente
- Integra padrões arquiteturais da sua documentação interna (se disponível)
- Pode gerar múltiplas variações de plano para comparação

#### Comando:
```bash
/plan
```

**Exemplo de prompt**:
```
Stack: Node.js + TypeScript, PostgreSQL, Redis para cache
Arquitetura: Microserviços com API Gateway
Integrações: Twilio (SMS), SendGrid (email), Firebase (push)
Compliance: LGPD - dados sensíveis devem ser criptografados
Performance: Processar 1000 notificações/segundo, p95 < 500ms
Padrões: Usar padrão Repository, eventos via RabbitMQ, logs estruturados com Winston
```

---

### Fase 3: Tasks (Tarefas)

**Objetivo**: Quebrar a especificação e o plano em tarefas implementáveis e testáveis.

#### O que o agente de IA faz:
- Decompõe o trabalho em chunks pequenos e revisáveis
- Cada tarefa resolve uma parte específica do problema
- Tarefas são implementáveis e testáveis isoladamente
- Funciona como TDD para o agente de IA

#### Características de uma boa tarefa:
- **Específica**: "Criar endpoint de registro de usuário que valida formato de email"
- **Testável**: Pode ser validada isoladamente
- **Focada**: Resolve um problema específico
- **Não vaga**: ❌ "Construir autenticação" → ✅ "Implementar middleware JWT que valida tokens no header Authorization"

#### Comando:
```bash
/tasks
```

**Exemplo de saída**:
```markdown
## Tarefas - Sistema de Notificações

### Backend
1. Criar schema PostgreSQL para templates de notificação
2. Implementar endpoint POST /api/v1/templates com validação Zod
3. Criar service NotificationService com método sendNotification()
4. Integrar Twilio SDK para envio de SMS
5. Implementar fila RabbitMQ para processamento assíncrono
6. Criar worker que consome fila e processa notificações
7. Implementar endpoint GET /api/v1/notifications/history com paginação

### Testes
8. Testes unitários para NotificationService
9. Testes de integração para endpoints da API
10. Testes de carga: 1000 notificações/segundo
```

---

### Fase 4: Implement (Implementar)

**Objetivo**: O agente de IA implementa as tarefas uma a uma (ou em paralelo quando aplicável).

#### Como funciona:
- Agente trabalha em tarefas específicas, não em dumps de milhares de linhas
- Você revisa mudanças focadas que resolvem problemas específicos
- Agente sabe **o quê** construir (especificação), **como** construir (plano) e **em que ordem** (tarefas)

#### Seu papel:
- **Verificar**: Revisar código gerado
- **Validar**: Testar funcionalidade implementada
- **Refinar**: Ajustar quando necessário

#### Benefícios:
- Revisões mais fáceis (mudanças focadas)
- Menos surpresas (tudo estava especificado)
- Rastreabilidade (cada tarefa tem propósito claro)
- Qualidade maior (validação em cada etapa)

---

## Checkpoints de Validação

Em cada fase, você deve **refletir e refinar** antes de avançar:

### Após Specify:
- ✅ A spec captura o que realmente quero construir?
- ✅ Há casos de uso ou edge cases que a IA perdeu?
- ✅ As jornadas de usuário fazem sentido?

### Após Plan:
- ✅ O plano considera restrições do mundo real?
- ✅ A arquitetura proposta é viável?
- ✅ Integrações e dependências estão mapeadas?

### Após Tasks:
- ✅ As tarefas são granulares o suficiente?
- ✅ Há dependências entre tarefas que precisam ser ordenadas?
- ✅ Todas as tarefas são testáveis?

### Durante Implement:
- ✅ O código implementa corretamente a tarefa?
- ✅ Testes passam?
- ✅ Há débito técnico sendo introduzido?

---

## Por Que Este Workflow Funciona

### 1. Clareza vs Adivinhação
Modelos de linguagem são excelentes em completar padrões, mas não em ler mentes. Especificação clara elimina adivinhação.

### 2. Agnóstico de Tecnologia
O processo funciona em qualquer stack porque o desafio fundamental é o mesmo: traduzir intenção em código funcional.

### 3. Requisitos Organizacionais Centralizados
Políticas de segurança, regras de compliance, constraints de design system - tudo vai na spec e no plano, onde a IA pode usá-los desde o dia 1.

### 4. Iteração Facilitada
Mudar de direção é simples: atualize a spec, regenere o plano, deixe o agente lidar com o resto.

---

## 3 Cenários Onde Este Workflow Brilha

### 1. Greenfield (Zero-to-One)
Começar um novo projeto com spec e plano garante que a IA construa o que você realmente pretende, não apenas uma solução genérica.

### 2. Feature Work em Sistemas Existentes (N-to-N+1)
Adicionar features a codebases complexas é difícil. A spec força clareza sobre como a feature interage com o sistema existente. O plano codifica constraints arquiteturais, garantindo que o novo código pareça nativo.

### 3. Modernização de Legacy
Ao reconstruir sistemas legados, a intenção original geralmente se perdeu. Com SDD, você captura a lógica de negócio essencial em uma spec moderna, projeta uma arquitetura fresca no plano e deixa a IA reconstruir o sistema sem carregar débito técnico herdado.

---

## Como Começar com Spec Kit

### Instalação

```bash
# Instalar ferramenta de linha de comando
uvx --from git+https://github.com/github/spec-kit.git specify init <PROJECT_NAME>
```

### Workflow Básico

```bash
# 1. Inicializar projeto
specify init meu-projeto

# 2. No seu agente de IA (Copilot, Claude, etc.)
/specify
# [Fornecer descrição de alto nível]

# 3. Gerar plano técnico
/plan
# [Fornecer stack e constraints]

# 4. Quebrar em tarefas
/tasks

# 5. Implementar
# [Agente implementa tarefa por tarefa]
```

---

## Integração com Nossa Documentação SDD

Este workflow do Spec Kit é uma **implementação específica** dos princípios SDD que documentamos:

| Conceito SDD | Fase Spec Kit |
|--------------|---------------|
| **Design-First** | Specify + Plan |
| **Single Source of Truth** | Spec como artefato central |
| **Automação e Codegen** | Tasks + Implement |
| **Desenvolvimento Paralelo** | Tasks permitem paralelização |
| **Validação** | Checkpoints em cada fase |

### Diferenças de Abordagem

**Nossa Documentação (Genérica)**:
- Agnóstica de ferramenta
- Foca em princípios e conceitos
- Aplicável a qualquer stack e processo

**Spec Kit (Específica)**:
- Ferramenta concreta com comandos específicos
- Workflow estruturado em 4 fases
- Otimizado para agentes de IA

**Recomendação**: Use os princípios da nossa documentação como base conceitual e o Spec Kit como ferramenta prática de implementação.

---

## Recursos Adicionais

- **[Repositório Spec Kit](https://github.com/github/spec-kit)**: Código-fonte e documentação
- **[Artigo GitHub Blog](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)**: Artigo completo sobre SDD com IA
- **[Issues do Spec Kit](https://github.com/github/spec-kit/issues)**: Compartilhe feedback e experiências

---

## Próximos Passos

1. **Experimente**: Inicie um projeto pequeno com Spec Kit
2. **Documente**: Registre o que funciona e o que não funciona
3. **Itere**: Refine seu processo baseado em aprendizados
4. **Compartilhe**: Contribua com a comunidade Spec Kit

O futuro do desenvolvimento é **"intenção como fonte da verdade"**, e ferramentas como Spec Kit tornam isso realidade.
