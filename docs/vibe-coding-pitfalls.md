# Armadilhas do Vibe Coding: Por Que SDD é a Alternativa

> **📖 Leitura Complementar**: Para entender as regras implícitas que levam a essas armadilhas, veja [As 15 Regras do Vibe Coding](15-vibe-coding-rules.md).

## O que é Vibe Coding?

**Vibe Coding** (ou "programação por intuição") é a prática de desenvolver software baseando-se em prompts vagos, iterações ad-hoc e experimentação sem estrutura clara. É o equivalente a "ir com o flow" sem planejamento, documentação ou contratos definidos.

### Características do Vibe Coding:

- Prompts vagos e não estruturados para ferramentas de IA
- Ausência de especificação prévia
- Iteração por tentativa e erro
- Documentação inexistente ou criada depois (se criada)
- Decisões arquiteturais emergentes do código
- "Funciona na minha máquina" como critério de sucesso

---

## As 10 Principais Armadilhas

### 1. 🎲 **Adivinhação em Escala**

**Problema**: Prompts vagos como "adicione autenticação" forçam a IA a adivinhar milhares de requisitos não declarados.

**Consequências**:
- IA faz suposições razoáveis, mas algumas estarão erradas
- Você só descobre os problemas tarde na implementação
- Retrabalho constante para corrigir suposições incorretas

**Exemplo**:
```
❌ Vibe Coding: "Adicione autenticação ao app"
✅ SDD: Spec define: JWT vs OAuth2, tempo de expiração, refresh tokens, 
        endpoints protegidos, roles/permissions, MFA opcional
```

**Solução SDD**: Especificação clara elimina adivinhação.

---

### 2. 🔄 **Ciclo Infinito de Refatoração**

**Problema**: Sem contrato definido, cada mudança de requisito força refatoração completa.

**Consequências**:
- Código constantemente reescrito
- Débito técnico acumulado
- Time preso em "modo refatoração"
- Features nunca são "finalizadas"

**Exemplo Real**:
```
Semana 1: IA gera API REST simples
Semana 2: "Adicione paginação" → Refatora todos os endpoints
Semana 3: "Adicione filtros" → Refatora novamente
Semana 4: "Adicione ordenação" → Mais refatoração
```

**Solução SDD**: Spec define paginação, filtros e ordenação desde o início.

---

### 3. 🧩 **Inconsistência Arquitetural**

**Problema**: Cada prompt gera código com padrões diferentes.

**Consequências**:
- Codebase com múltiplos estilos arquiteturais
- Difícil manutenção
- Onboarding de novos devs complicado
- Code reviews demorados

**Exemplo**:
```javascript
// Endpoint 1 (gerado no prompt A)
app.get('/users', async (req, res) => {
  const users = await db.query('SELECT * FROM users');
  res.json(users);
});

// Endpoint 2 (gerado no prompt B)
class UserController {
  async getUsers(req, res) {
    const users = await this.userService.findAll();
    return res.json(users);
  }
}

// Endpoint 3 (gerado no prompt C)
const getUsers = pipe(
  validateRequest,
  fetchFromDb,
  transformResponse
);
```

**Solução SDD**: Plano técnico define padrões arquiteturais únicos para todo o projeto.

---

### 4. 🔒 **Segurança como Afterthought**

**Problema**: Requisitos de segurança não são considerados desde o início.

**Consequências**:
- Vulnerabilidades descobertas em produção
- Dados sensíveis expostos
- Compliance violado
- Retrabalho caro para adicionar segurança depois

**Exemplos de Falhas**:
- Senhas armazenadas em texto plano
- SQL injection em queries geradas
- CORS configurado como `*` (allow all)
- Tokens sem expiração
- Logs com informações sensíveis

**Solução SDD**: Spec e plano incluem requisitos de segurança desde o dia 1.

---

### 5. 📚 **Documentação Fantasma**

**Problema**: Documentação nunca é criada ou fica desatualizada imediatamente.

**Consequências**:
- Novos membros do time não sabem como o sistema funciona
- Integrações com outros times são difíceis
- Conhecimento fica na cabeça de poucos
- Bus factor alto (risco se alguém sair)

**Realidade do Vibe Coding**:
```
Dev: "Como funciona a autenticação?"
Resposta: "Olha no código... acho que está no arquivo auth.js... 
          ou talvez no middleware.ts... não lembro"
```

**Solução SDD**: Spec é a documentação viva e sempre atualizada.

---

### 6. 🧪 **Testes? Que Testes?**

**Problema**: Testes são ignorados ou criados superficialmente depois.

**Consequências**:
- Bugs em produção
- Medo de fazer mudanças (quebra algo?)
- Regressões constantes
- Confiança zero no código

**Estatística Típica**:
```
Vibe Coding:
- Cobertura de testes: 15-30%
- Testes escritos: Depois (talvez)
- Tipo: Alguns testes unitários básicos

SDD:
- Cobertura de testes: 70-90%
- Testes escritos: Durante (baseados na spec)
- Tipo: Unitários, integração, contrato, E2E
```

**Solução SDD**: Spec define comportamento esperado, facilitando geração de testes.

---

### 7. 🤝 **Integração Impossível**

**Problema**: Times diferentes desenvolvem com vibe coding e não conseguem integrar.

**Consequências**:
- Frontend e Backend não conversam
- Microserviços incompatíveis
- Integrações com parceiros falham
- Reuniões intermináveis para "alinhar"

**Cenário Real**:
```
Frontend: "A API retorna { user: {...} }"
Backend: "Não, retorna { data: { user: {...} } }"
Frontend: "Mas ontem era diferente!"
Backend: "Mudamos porque fazia mais sentido"
Frontend: "Ninguém me avisou! 😤"
```

**Solução SDD**: Contrato (spec) é acordado antes, todos trabalham baseados nele.

---

### 8. 💸 **Custo Oculto Exponencial**

**Problema**: Vibe coding parece rápido no início, mas o custo explode com o tempo.

**Curva de Custo**:
```
Vibe Coding:
Semana 1-2: 🚀 Rápido (ilusão de produtividade)
Semana 3-4: 🐌 Desacelera (refatorações começam)
Semana 5+:  🐢 Crawling (mais tempo corrigindo do que criando)

SDD:
Semana 1-2: 🏗️ Setup (spec + plano)
Semana 3-4: 🚀 Aceleração (implementação focada)
Semana 5+:  ⚡ Velocidade sustentável (menos surpresas)
```

**Métricas Reais**:
- **Vibe Coding**: 60-70% do tempo em retrabalho
- **SDD**: 15-25% do tempo em ajustes

**Solução SDD**: Investimento inicial em spec compensa exponencialmente.

---

### 9. 🎯 **Scope Creep Descontrolado**

**Problema**: Sem spec clara, requisitos mudam constantemente.

**Consequências**:
- Projeto nunca termina
- Features pela metade
- Stakeholders frustrados
- Time esgotado

**Conversa Típica**:
```
PM: "Podemos adicionar X?"
Dev: "Sim, é só um prompt..."
[3 dias depois]
Dev: "X quebrou Y e Z, precisamos refatorar tudo"
PM: "Mas você disse que era simples!"
```

**Solução SDD**: Spec define escopo claramente. Mudanças exigem atualização formal da spec.

---

### 10. 🧠 **Dependência de "Heróis"**

**Problema**: Apenas quem escreveu os prompts entende o código.

**Consequências**:
- Conhecimento não é transferível
- Dependência de indivíduos específicos
- Difícil escalar o time
- Risco alto se alguém sair

**Realidade**:
```
Dev A: "Só o João sabe como funciona o módulo de pagamentos"
Dev B: "E ele está de férias por 3 semanas"
Dev A: "E agora tem um bug crítico em produção..."
```

**Solução SDD**: Spec documenta intenção, qualquer dev pode entender e contribuir.

---

## Comparação: Vibe Coding vs SDD

| Aspecto | Vibe Coding | SDD |
|---------|-------------|-----|
| **Início** | Rápido (ilusório) | Estruturado |
| **Médio Prazo** | Desacelera | Acelera |
| **Longo Prazo** | Insustentável | Sustentável |
| **Qualidade** | Inconsistente | Consistente |
| **Manutenção** | Difícil | Facilitada |
| **Onboarding** | Lento | Rápido |
| **Documentação** | Inexistente | Sempre atualizada |
| **Testes** | Poucos | Abrangentes |
| **Segurança** | Afterthought | Built-in |
| **Custo Total** | Alto | Otimizado |

---

## Quando Vibe Coding Pode Funcionar

Existem cenários onde vibe coding é aceitável (mas não ideal):

### ✅ Protótipos Descartáveis
- Proof of concept que será jogado fora
- Duração: Algumas horas/dias
- Não vai para produção

### ✅ Scripts Pessoais
- Automações pessoais de uso único
- Não compartilhadas com outros
- Sem requisitos de qualidade

### ✅ Experimentos de Aprendizado
- Estudando uma nova tecnologia
- Objetivo é aprender, não produzir

### ❌ Nunca Use Vibe Coding Para:
- Código de produção
- Sistemas com múltiplos desenvolvedores
- Projetos com prazo definido
- Aplicações críticas de negócio
- Qualquer coisa que precise ser mantida

---

## Como Migrar de Vibe Coding para SDD

### Passo 1: Reconheça o Problema
Sinais de que você está em vibe coding:
- [ ] Refatorações constantes
- [ ] Documentação desatualizada ou inexistente
- [ ] Bugs recorrentes em produção
- [ ] Integrações sempre quebram
- [ ] Time frustrado com retrabalho

### Passo 2: Comece Pequeno
Não precisa migrar tudo de uma vez:
1. **Próxima feature**: Use SDD
2. **Documente**: Crie spec para módulo crítico existente
3. **Aprenda**: Veja a diferença na prática

### Passo 3: Estabeleça Processo
1. Nenhum código novo sem spec
2. Spec deve ser revisada antes da implementação
3. Testes baseados na spec
4. Documentação gerada da spec

### Passo 4: Ferramentas
- Use [Spec Kit](https://github.com/github/spec-kit) para workflow estruturado
- Configure linters de spec (Spectral)
- Implemente contract testing
- Automatize geração de código

### Passo 5: Cultura
- Celebre specs bem escritas
- Code review inclui revisão de spec
- Onboarding ensina SDD desde o início
- Retrospectivas discutem qualidade da spec

---

## Estudos de Caso: Vibe Coding Deu Errado

### Caso 1: Startup de E-commerce

**Situação**: Time usou vibe coding com IA para "ir rápido"

**Resultado**:
- Semana 1-2: MVP "pronto"
- Semana 3: Descobriram que checkout não valida estoque
- Semana 4: Refatoração completa do carrinho
- Semana 5: Pagamentos falhando intermitentemente
- Semana 6: Reescrita de 70% do código
- **Lançamento atrasado em 2 meses**

**Custo**: 3x mais caro que se tivessem usado SDD desde o início.

### Caso 2: Microserviços em Fintech

**Situação**: 3 times desenvolvendo microserviços com vibe coding

**Resultado**:
- Cada time gerou APIs incompatíveis
- Integração levou 4 semanas (planejado: 3 dias)
- Descobriram problemas de segurança em auditoria
- Tiveram que criar "camada de tradução" entre serviços
- **Débito técnico permanente**

**Lição**: Contrato (spec) deve ser definido antes da implementação.

### Caso 3: Modernização de Legacy

**Situação**: Reescrevendo sistema legado com vibe coding + IA

**Resultado**:
- Lógica de negócio crítica foi perdida
- Comportamentos sutis não foram replicados
- Bugs só descobertos em produção
- Rollback forçado após 2 semanas
- **Projeto cancelado**

**Lição**: Spec captura intenção e lógica de negócio antes de jogar código legado fora.

---

## Checklist: Você Está em Vibe Coding?

Responda honestamente:

- [ ] Você escreve prompts vagos e espera que a IA "entenda"?
- [ ] Sua documentação está desatualizada ou não existe?
- [ ] Você refatora o mesmo código múltiplas vezes?
- [ ] Testes são escritos depois (quando escritos)?
- [ ] Integrações entre times sempre têm problemas?
- [ ] Você tem medo de fazer mudanças (pode quebrar algo)?
- [ ] Onboarding de novos devs leva semanas?
- [ ] Você descobre requisitos de segurança tarde demais?
- [ ] Stakeholders reclamam de "surpresas" constantes?
- [ ] Você passa mais tempo corrigindo do que criando?

**Se marcou 3+ itens**: Você está em vibe coding e precisa migrar para SDD.

---

## Conclusão: A Ilusão da Velocidade

Vibe coding **parece** rápido porque você vê código sendo gerado rapidamente. Mas essa é uma **ilusão de produtividade**.

### A Verdade:
- **Velocidade real** = Código funcionando em produção, sem bugs, mantível
- **Velocidade falsa** = Código gerado rapidamente, mas cheio de problemas

### A Matemática:
```
Vibe Coding:
- Geração: 2 horas
- Correções: 20 horas
- Total: 22 horas

SDD:
- Spec: 3 horas
- Geração: 2 horas
- Correções: 3 horas
- Total: 8 horas
```

**SDD é 2.75x mais rápido no total**, mesmo "perdendo tempo" com spec.

---

## Recursos Adicionais

- **[Workflow Spec Kit](spec-kit-workflow.md)**: Processo estruturado para evitar vibe coding
- **[AI-Enhanced SDD](ai-enhanced-sdd.md)**: Como usar IA corretamente com specs
- **[Biblioteca de Prompts](prompts-library.md)**: Prompts estruturados vs vagos
- **[Casos de Uso](use-cases.md)**: Exemplos reais de SDD funcionando

---

## Mensagem Final

**Vibe coding com IA é como dirigir um carro de Fórmula 1 sem mapa**: você vai rápido, mas provavelmente na direção errada.

**SDD com IA é como ter GPS + carro potente**: você sabe para onde vai e chega lá rapidamente.

A escolha é sua. Mas lembre-se: **código rápido e errado é mais lento que código planejado e correto**.

---

*"Weeks of coding can save you hours of planning."* — Anônimo (aprendeu da forma difícil)
