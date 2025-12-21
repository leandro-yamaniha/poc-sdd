# As 15 Regras do Vibe Coding (E Por Que Todas Estão Erradas)

## Introdução

**Vibe Coding** é uma anti-metodologia que emergiu com o advento de ferramentas de IA generativa. Caracteriza-se por desenvolvimento sem estrutura, baseado em "sentir" o que fazer a cada momento, sem planejamento ou especificação prévia.

Este documento cataloga as **15 "regras"** implícitas que praticantes de vibe coding seguem (consciente ou inconscientemente) e explica por que cada uma delas leva a problemas sérios.

> **Nota**: Este documento é um complemento de [Armadilhas do Vibe Coding](vibe-coding-pitfalls.md), que detalha as consequências práticas dessas regras.

---

## As 15 Regras do Vibe Coding

### Regra 1: "Apenas Comece a Codar"

**O que dizem**: "Planejamento é perda de tempo. Apenas comece a escrever código e veja no que dá."

**Por que está errado**:
- Código sem direção clara resulta em retrabalho constante
- Decisões arquiteturais são tomadas por acidente, não por design
- Mudanças de requisitos forçam refatorações completas

**Alternativa SDD**:
```
❌ Vibe: "Vou criar uma API... vamos ver o que acontece"
✅ SDD: "Vou especificar os endpoints, schemas e comportamentos esperados primeiro"
```

**Impacto Real**:
- **Tempo perdido em refatoração**: 60-70%
- **Débito técnico acumulado**: Alto
- **Satisfação do time**: Baixa

---

### Regra 2: "Documentação é para Depois"

**O que dizem**: "Vou documentar quando terminar. Agora preciso codar rápido."

**Por que está errado**:
- "Depois" nunca chega
- Quando chega, você já esqueceu por que fez aquilo
- Documentação desatualizada é pior que nenhuma documentação

**Alternativa SDD**:
```
❌ Vibe: Código sem docs → "Leia o código para entender"
✅ SDD: Spec é a documentação → Sempre atualizada, sempre clara
```

**Estatística**:
- **Projetos com vibe coding**: 85% sem documentação adequada
- **Projetos com SDD**: 95% com documentação atualizada (gerada da spec)

---

### Regra 3: "A IA Sabe o Que Eu Quero"

**O que dizem**: "Só preciso dar um prompt vago, a IA vai entender minha intenção."

**Por que está errado**:
- IA completa padrões, não lê mentes
- Prompts vagos geram código genérico e suposições incorretas
- Você descobre os problemas tarde demais

**Exemplo Real**:
```
❌ Vibe Prompt: "Adicione autenticação"
Resultado: IA gera auth básica com senha em texto plano

✅ SDD Prompt: "Implemente autenticação JWT com:
- Tokens com expiração de 1h
- Refresh tokens com expiração de 7 dias
- Bcrypt para hash de senhas (salt rounds: 12)
- Rate limiting: 5 tentativas/15min
- MFA opcional via TOTP"
Resultado: Exatamente o que você precisa
```

---

### Regra 4: "Testes Podem Esperar"

**O que dizem**: "Vou escrever testes quando o código estiver funcionando."

**Por que está errado**:
- Código "funcionando" sem testes é código não validado
- Refatorações futuras quebram funcionalidades sem você saber
- Bugs chegam em produção

**Comparação**:
```
Vibe Coding:
- Cobertura de testes: 15-30%
- Bugs em produção: Frequentes
- Confiança em fazer mudanças: Baixa

SDD:
- Cobertura de testes: 70-90%
- Bugs em produção: Raros
- Confiança em fazer mudanças: Alta
```

**Alternativa SDD**: Spec define comportamento esperado → Testes gerados automaticamente da spec.

---

### Regra 5: "Refatorar é Normal"

**O que dizem**: "É normal refatorar várias vezes. Faz parte do processo."

**Por que está errado**:
- Refatoração constante indica falta de planejamento
- Cada refatoração introduz risco de novos bugs
- Time preso em ciclo de retrabalho

**Realidade**:
```
Semana 1: Implementa feature X
Semana 2: Refatora X porque requisito mudou
Semana 3: Refatora X novamente porque quebrou Y
Semana 4: Refatora X de novo porque descobriu edge case
```

**Alternativa SDD**: Spec captura requisitos completos → Refatorações mínimas e planejadas.

---

### Regra 6: "Segurança é para o Final"

**O que dizem**: "Vou adicionar segurança quando tudo estiver funcionando."

**Por que está errado**:
- Segurança não é feature, é requisito fundamental
- Adicionar segurança depois exige refatoração completa
- Vulnerabilidades podem já estar em produção

**Vulnerabilidades Comuns no Vibe Coding**:
- SQL Injection (queries não parametrizadas)
- XSS (input não sanitizado)
- CSRF (sem tokens)
- Senhas em texto plano
- Tokens sem expiração
- CORS configurado como `*`
- Logs com dados sensíveis

**Alternativa SDD**: Requisitos de segurança na spec desde o dia 1.

---

### Regra 7: "Cada Prompt é Independente"

**O que dizem**: "Vou dar um prompt novo para cada feature, sem contexto do anterior."

**Por que está errado**:
- Código inconsistente (cada prompt usa padrões diferentes)
- Duplicação de lógica
- Integração entre features quebra

**Exemplo**:
```javascript
// Feature A (Prompt 1)
function getUsers() {
  return db.query('SELECT * FROM users');
}

// Feature B (Prompt 2)
class ProductService {
  async getProducts() {
    return await this.repository.findAll();
  }
}

// Feature C (Prompt 3)
const getOrders = pipe(
  fetchFromDb,
  transformData
);

// Resultado: 3 padrões diferentes no mesmo projeto!
```

**Alternativa SDD**: Plano técnico define padrões arquiteturais únicos para todo o projeto.

---

### Regra 8: "Funciona na Minha Máquina = Sucesso"

**O que dizem**: "Se roda no meu ambiente local, está pronto para produção."

**Por que está errado**:
- Ambientes locais têm configurações específicas
- Produção tem constraints diferentes (rede, recursos, dados)
- Bugs aparecem apenas em produção

**Checklist Ignorado no Vibe Coding**:
- [ ] Testes em ambiente similar a produção
- [ ] Validação de performance sob carga
- [ ] Testes de integração com serviços externos
- [ ] Validação de logs e monitoramento
- [ ] Rollback plan

**Alternativa SDD**: Spec define requisitos não-funcionais (performance, disponibilidade, etc.).

---

### Regra 9: "Scope Creep é Flexibilidade"

**O que dizem**: "Vamos adicionar mais uma coisinha... é rápido!"

**Por que está errado**:
- "Mais uma coisinha" vira 10 coisas
- Projeto nunca termina
- Stakeholders frustrados

**Ciclo Vicioso**:
```
PM: "Podemos adicionar filtro por data?"
Dev: "Claro, é só um prompt"
[2 dias depois]
Dev: "Filtro quebrou a paginação, preciso refatorar"
PM: "Mas você disse que era rápido!"
Dev: "Apareceram edge cases..."
```

**Alternativa SDD**: Spec define escopo claramente. Mudanças exigem atualização formal da spec.

---

### Regra 10: "Git Commit Messages Não Importam"

**O que dizem**: "fix", "update", "changes" são mensagens suficientes.

**Por que está errado**:
- Histórico do projeto é ilegível
- Impossível entender por que mudanças foram feitas
- Rollbacks são um pesadelo

**Vibe Coding Git Log**:
```
fix
update
more changes
fix again
final fix
actually final fix
ok now it works
```

**SDD Git Log**:
```
feat(auth): implement JWT authentication with refresh tokens (closes #123)
test(auth): add contract tests for auth endpoints
docs(api): update OpenAPI spec with new auth flows
```

---

### Regra 11: "Code Review é Opcional"

**O que dizem**: "A IA gerou, deve estar certo. Vou mergear direto."

**Por que está errado**:
- IA comete erros (lógica incorreta, vulnerabilidades)
- Ninguém além de você entende o código
- Qualidade degrada rapidamente

**O Que Code Review Deveria Validar**:
- [ ] Código implementa a spec corretamente
- [ ] Testes cobrem casos principais
- [ ] Não há vulnerabilidades óbvias
- [ ] Padrões arquiteturais são seguidos
- [ ] Documentação está atualizada

**Alternativa SDD**: Review da spec antes da implementação + review do código contra a spec.

---

### Regra 12: "Performance é Otimização Prematura"

**O que dizem**: "Vou me preocupar com performance depois, se for necessário."

**Por que está errado**:
- Decisões arquiteturais ruins são difíceis de reverter
- Performance não é apenas otimização, é design
- Usuários abandonam apps lentos

**Problemas Comuns**:
- N+1 queries (IA gera loops com queries dentro)
- Falta de índices no banco
- Sem cache em operações caras
- Payloads gigantes sem paginação
- Sem rate limiting

**Alternativa SDD**: Spec define requisitos de performance (p95 < 500ms, etc.).

---

### Regra 13: "Monolito Primeiro, Microserviços Depois"

**O que dizem**: "Vou fazer tudo num arquivo só, depois separo."

**Por que está errado**:
- "Depois" nunca chega
- Separar monolito é reescrita completa
- Acoplamento alto dificulta manutenção

**Evolução Típica**:
```
Semana 1: 1 arquivo, 200 linhas
Semana 4: 1 arquivo, 2000 linhas
Semana 8: 1 arquivo, 10000 linhas
Semana 12: "Precisamos refatorar isso..."
Semana 16: Ainda no mesmo arquivo
```

**Alternativa SDD**: Plano define estrutura modular desde o início.

---

### Regra 14: "Logs São para Debug Local"

**O que dizem**: "console.log() é suficiente para debugar."

**Por que está errado**:
- Produção não tem console.log visível
- Sem logs estruturados, debugging é impossível
- Incidentes levam horas para diagnosticar

**Vibe Coding Logs**:
```javascript
console.log('user:', user);
console.log('error!!!');
console.log('here');
```

**SDD Logs**:
```javascript
logger.info('User authenticated', {
  userId: user.id,
  method: 'JWT',
  timestamp: new Date().toISOString()
});

logger.error('Authentication failed', {
  reason: 'Invalid token',
  userId: attemptedUserId,
  ip: req.ip,
  timestamp: new Date().toISOString()
});
```

---

### Regra 15: "Deploys Manuais São Mais Seguros"

**O que dizem**: "Vou fazer deploy manual para ter controle total."

**Por que está errado**:
- Processo manual é propenso a erros
- Sem CI/CD, testes não rodam automaticamente
- Rollbacks são complicados
- Deploys se tornam eventos raros e arriscados

**Vibe Coding Deploy**:
```bash
# Dev às 23h de sexta-feira
scp -r * production:/var/www/
ssh production "sudo systemctl restart app"
# 🤞 Torce para funcionar
```

**SDD Deploy**:
```yaml
# CI/CD automatizado
on: push to main
  - run tests
  - validate spec
  - contract tests
  - build
  - deploy to staging
  - smoke tests
  - deploy to production (blue-green)
  - health checks
  - rollback if fail
```

---

## Resumo: As 15 Regras e Suas Consequências

| # | Regra do Vibe Coding | Consequência Principal | Alternativa SDD |
|---|---------------------|----------------------|-----------------|
| 1 | Apenas comece a codar | Retrabalho constante | Spec primeiro |
| 2 | Documentação é para depois | Docs inexistentes | Spec é a doc |
| 3 | A IA sabe o que eu quero | Código genérico | Prompts estruturados |
| 4 | Testes podem esperar | Bugs em produção | Testes da spec |
| 5 | Refatorar é normal | Ciclo de retrabalho | Planejamento adequado |
| 6 | Segurança é para o final | Vulnerabilidades | Segurança na spec |
| 7 | Cada prompt é independente | Código inconsistente | Plano técnico único |
| 8 | Funciona na minha máquina | Falhas em produção | Requisitos não-funcionais |
| 9 | Scope creep é flexibilidade | Projeto infinito | Escopo definido |
| 10 | Git messages não importam | Histórico ilegível | Commits semânticos |
| 11 | Code review é opcional | Qualidade baixa | Review obrigatório |
| 12 | Performance depois | Apps lentos | Performance na spec |
| 13 | Monolito primeiro | Código acoplado | Estrutura modular |
| 14 | Logs para debug local | Debugging impossível | Logs estruturados |
| 15 | Deploys manuais | Processo frágil | CI/CD automatizado |

---

## A Matemática do Vibe Coding

### Tempo Gasto por Atividade

**Vibe Coding** (100 horas totais):
- Codificação inicial: 20h (20%)
- Refatorações: 40h (40%)
- Correção de bugs: 25h (25%)
- Debugging sem logs: 10h (10%)
- Documentação apressada: 5h (5%)

**SDD** (100 horas totais):
- Especificação: 15h (15%)
- Planejamento: 10h (10%)
- Codificação: 35h (35%)
- Testes: 20h (20%)
- Refatorações planejadas: 10h (10%)
- Correção de bugs: 5h (5%)
- Documentação (gerada): 5h (5%)

### Resultado Final

**Vibe Coding**:
- ✅ Features entregues: 60%
- ❌ Qualidade: Baixa
- ❌ Manutenibilidade: Difícil
- ❌ Satisfação do time: 3/10

**SDD**:
- ✅ Features entregues: 95%
- ✅ Qualidade: Alta
- ✅ Manutenibilidade: Fácil
- ✅ Satisfação do time: 8/10

---

## Como Identificar Vibe Coding no Seu Projeto

### Checklist de Sintomas

Marque quantos se aplicam ao seu projeto:

- [ ] **Regra 1**: Começamos a codar sem spec clara
- [ ] **Regra 2**: Documentação está desatualizada ou não existe
- [ ] **Regra 3**: Usamos prompts vagos tipo "adicione X"
- [ ] **Regra 4**: Cobertura de testes < 50%
- [ ] **Regra 5**: Refatoramos o mesmo código 3+ vezes
- [ ] **Regra 6**: Descobrimos vulnerabilidades em produção
- [ ] **Regra 7**: Código tem múltiplos padrões arquiteturais
- [ ] **Regra 8**: Bugs aparecem apenas em produção
- [ ] **Regra 9**: Escopo muda toda semana
- [ ] **Regra 10**: Git log é incompreensível
- [ ] **Regra 11**: Code reviews são raros ou superficiais
- [ ] **Regra 12**: App é lento mas "funciona"
- [ ] **Regra 13**: Tudo em poucos arquivos gigantes
- [ ] **Regra 14**: Debugging leva horas
- [ ] **Regra 15**: Deploys são eventos estressantes

**Resultado**:
- **0-3 marcados**: Você está no caminho certo
- **4-7 marcados**: Vibe coding moderado, precisa melhorar
- **8-11 marcados**: Vibe coding severo, migre para SDD urgentemente
- **12-15 marcados**: Vibe coding crítico, considere reescrever com SDD

---

## Plano de Ação: Quebrando as 15 Regras

### Semana 1: Conscientização
- [ ] Apresente este documento para o time
- [ ] Identifique quais regras vocês seguem
- [ ] Discuta impactos observados

### Semana 2: Primeiros Passos
- [ ] Escolha 1 feature nova para fazer com SDD
- [ ] Escreva spec antes de codar
- [ ] Compare resultado com vibe coding anterior

### Semana 3: Processos
- [ ] Estabeleça: "Nenhum código sem spec"
- [ ] Configure linters de spec (Spectral)
- [ ] Implemente code review obrigatório

### Semana 4: Automação
- [ ] Configure CI/CD básico
- [ ] Adicione contract testing
- [ ] Automatize geração de docs da spec

### Mês 2: Cultura
- [ ] Celebre specs bem escritas
- [ ] Compartilhe sucessos com SDD
- [ ] Refine processo baseado em feedback

### Mês 3: Consolidação
- [ ] 100% das features novas com SDD
- [ ] Comece a documentar código legado com specs
- [ ] Meça melhorias (velocidade, qualidade, satisfação)

---

## Recursos Relacionados

### Documentação Complementar
- **[Armadilhas do Vibe Coding](vibe-coding-pitfalls.md)**: Consequências detalhadas das 15 regras
- **[Workflow Spec Kit](spec-kit-workflow.md)**: Processo estruturado para evitar vibe coding
- **[AI-Enhanced SDD](ai-enhanced-sdd.md)**: Como usar IA corretamente com specs
- **[Conceitos Fundamentais](concepts.md)**: Pilares do SDD

### Ferramentas
- **[Spec Kit](https://github.com/github/spec-kit)**: Toolkit para SDD com IA
- **[Spectral](https://stoplight.io/open-source/spectral)**: Linter de specs
- **[Dredd](https://dredd.org/)**: Contract testing

---

## Conclusão: A Escolha é Sua

Você pode continuar seguindo as **15 regras do vibe coding** e lidar com:
- ❌ Retrabalho constante
- ❌ Bugs em produção
- ❌ Documentação inexistente
- ❌ Time frustrado
- ❌ Projetos que nunca terminam

Ou pode adotar **SDD** e ter:
- ✅ Código de qualidade
- ✅ Desenvolvimento sustentável
- ✅ Documentação sempre atualizada
- ✅ Time satisfeito
- ✅ Projetos entregues no prazo

**A diferença entre vibe coding e SDD não é velocidade inicial. É velocidade sustentável.**

---

## Citações Memoráveis

> *"Vibe coding é otimizar para o primeiro commit. SDD é otimizar para o centésimo commit."*

> *"Toda regra do vibe coding parece economizar tempo. Todas custam tempo no final."*

> *"A IA não precisa de prompts vagos. Ela precisa de especificações claras."*

> *"Código sem spec é como construir uma casa sem planta: pode até ficar de pé, mas ninguém vai querer morar nela."*

---

**Próximo passo**: Leia [Armadilhas do Vibe Coding](vibe-coding-pitfalls.md) para ver exemplos reais de cada regra em ação.
