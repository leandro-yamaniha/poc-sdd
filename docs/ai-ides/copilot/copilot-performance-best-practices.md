# GitHub Copilot: Performance, Boas Práticas e Limitações

## Introdução

Este documento aborda **performance**, **boas práticas** e **limitações** ao usar **GitHub Copilot** e **GitHub Copilot Chat** com custom instructions no contexto de Spec-Driven Development.

> **Contexto**: GitHub Copilot tem limites de contexto, custos de API e restrições que impactam diretamente a eficácia em projetos SDD.

---

## Limitações do GitHub Copilot

### 1. Limites de Contexto (Token Limits)

#### GitHub Copilot (Autocomplete)
- **Contexto**: ~2-4KB de código ao redor do cursor
- **Janela**: Arquivos abertos + snippets próximos
- **Limitação**: Não vê todo o projeto, apenas contexto local

#### GitHub Copilot Chat
- **Contexto por mensagem**: ~8K tokens (GPT-4 Turbo)
- **Histórico de conversa**: ~20-30 mensagens
- **Limitação**: Conversas longas perdem contexto inicial

#### Implicações para SDD
```
❌ Problema: Spec grande (5000+ linhas) não cabe no contexto
❌ Problema: Histórico de decisões arquiteturais se perde
❌ Problema: Múltiplas specs relacionadas não são consideradas juntas
```

### 2. Limites de Taxa (Rate Limits)

#### Copilot Autocomplete
- **Sugestões por minuto**: ~100-150
- **Throttling**: Após uso intenso, sugestões desaceleram

#### Copilot Chat
- **Mensagens por hora**: ~50-100 (varia por plano)
- **Mensagens longas**: Contam mais no limite

#### Implicações para SDD
```
❌ Problema: Gerar múltiplas specs em sequência pode atingir limite
❌ Problema: Workflows automatizados podem ser throttled
```

### 3. Limitações de Conhecimento

#### Conhecimento Cortado
- **Data de corte**: Outubro 2023 (pode variar)
- **Specs internas**: Copilot não conhece padrões da sua empresa
- **APIs proprietárias**: Sem conhecimento de sistemas internos

#### Implicações para SDD
```
❌ Problema: Não conhece padrões arquiteturais da empresa
❌ Problema: Não sabe sobre políticas de segurança internas
❌ Problema: Pode sugerir bibliotecas desatualizadas
```

### 4. Limitações de Capacidades

#### O Que Copilot NÃO Faz
- ❌ Não executa código
- ❌ Não acessa arquivos fora do workspace (sem MCP)
- ❌ Não persiste memória entre sessões
- ❌ Não valida specs automaticamente
- ❌ Não faz deploy ou CI/CD

#### O Que Copilot FAZ
- ✅ Gera código baseado em contexto
- ✅ Responde perguntas sobre código
- ✅ Refatora código existente
- ✅ Explica código complexo
- ✅ Sugere melhorias

---

## Boas Práticas de Performance

### 1. Otimizar Custom Instructions

#### ❌ Ruim: Instructions Muito Longas

```markdown
# .github/copilot-instructions.md (5000+ palavras)

Você é um assistente especializado em desenvolvimento de software...
[3000 linhas de instruções detalhadas sobre tudo]
...incluindo como fazer café e organizar reuniões...
```

**Problema**: 
- Consome muito do contexto disponível
- Dilui informações importantes
- Copilot ignora partes das instruções

#### ✅ Bom: Instructions Concisas e Focadas

```markdown
# .github/copilot-instructions.md

## Metodologia
Seguimos Spec-Driven Development (SDD):
1. Spec OpenAPI primeiro
2. Validar com Spectral
3. Gerar código da spec
4. Testes de contrato obrigatórios

## Padrões de Código
- TypeScript strict mode
- Arquitetura: Repository pattern
- Testes: Jest + Supertest
- Docs: JSDoc para funções públicas

## Segurança
- Nunca hardcode secrets
- Sempre validar input
- Use bcrypt (12 rounds) para senhas
- JWT expira em 1h, refresh em 7d

## Proibido
- console.log em produção
- any type
- Commits sem testes
```

**Benefícios**:
- ~200 palavras vs 5000
- Informação densa e acionável
- Copilot processa tudo

#### 📏 Tamanho Ideal de Instructions

```
Recomendado: 150-300 palavras (~1000-2000 tokens)
Máximo aceitável: 500 palavras (~3000 tokens)
Evitar: 1000+ palavras (consome muito contexto)
```

### 2. Estruturar Instructions por Prioridade

#### ✅ Template Otimizado

```markdown
# .github/copilot-instructions.md

## 🎯 Regras Críticas (SEMPRE seguir)
- Spec OpenAPI antes de código
- Validar spec com Spectral
- Testes de contrato obrigatórios

## 📋 Padrões de Projeto
- Stack: Node.js + TypeScript + PostgreSQL
- Arquitetura: Clean Architecture
- ORM: Prisma

## 🔒 Segurança
- Bcrypt para senhas (12 rounds)
- JWT: 1h access, 7d refresh
- Rate limiting: 100 req/15min

## 🚫 Proibições
- Nunca: console.log, any, secrets hardcoded
```

**Estrutura**:
1. **Regras críticas** (topo): O que Copilot DEVE fazer
2. **Padrões**: Como fazer
3. **Segurança**: Requisitos não-negociáveis
4. **Proibições**: O que NÃO fazer

### 3. Usar Contexto Eficientemente

#### ❌ Ruim: Contexto Desperdiçado

```typescript
// arquivo-gigante.ts (3000 linhas)
// Copilot vê tudo, mas não consegue processar efetivamente

import { tudo } from 'everywhere';

// 2950 linhas de código...

function novaFuncao() {
  // Copilot aqui tem contexto poluído
}
```

#### ✅ Bom: Arquivos Modulares

```typescript
// specs/auth-spec.ts (50 linhas)
export const authSpec = { /* spec focada */ };

// services/auth.service.ts (100 linhas)
import { authSpec } from '../specs/auth-spec';
// Copilot vê apenas o necessário

export class AuthService {
  // Contexto limpo e focado
}
```

**Benefícios**:
- Copilot processa contexto relevante
- Sugestões mais precisas
- Menos ruído

### 4. Aproveitar Workspace Context

#### Arquivos Que Copilot Prioriza

```
Alta prioridade (sempre no contexto):
1. Arquivo atual
2. Arquivos abertos em tabs
3. .github/copilot-instructions.md
4. README.md
5. package.json / tsconfig.json

Média prioridade:
6. Arquivos importados diretamente
7. Arquivos no mesmo diretório

Baixa prioridade:
8. Outros arquivos do projeto
```

#### ✅ Estratégia: Abrir Arquivos Relevantes

```
Cenário: Criar nova spec para API de pagamentos

Abrir em tabs:
1. specs/payments.yaml (arquivo que vai editar)
2. specs/auth.yaml (spec similar como referência)
3. .spectral.yaml (regras de validação)
4. docs/api-standards.md (padrões da empresa)

Resultado: Copilot vê tudo que precisa
```

### 5. Otimizar Prompts no Chat

#### ❌ Ruim: Prompt Vago

```
Você: Crie uma API
```

**Problema**: Copilot adivinha tudo, resultado genérico.

#### ❌ Ruim: Prompt Excessivamente Detalhado

```
Você: Crie uma API REST completa para gerenciar usuários com CRUD completo, 
autenticação JWT, refresh tokens, validação de email, hash de senha com bcrypt 
usando 12 rounds, rate limiting de 100 requisições por 15 minutos, paginação 
com cursor, filtros por nome e email, ordenação por data de criação, soft delete, 
auditoria de mudanças, logs estruturados, testes unitários, testes de integração, 
testes E2E, documentação OpenAPI 3.0, exemplos de requisição e resposta, 
tratamento de erros com códigos HTTP apropriados, validação de input com Zod...
[continua por 500 palavras]
```

**Problema**: Consome todo o contexto, Copilot não processa tudo.

#### ✅ Bom: Prompt Estruturado e Conciso

```
Você: Crie spec OpenAPI 3.0 para API de usuários

Requisitos:
- CRUD completo
- Auth: JWT (1h) + refresh (7d)
- Segurança: bcrypt (12 rounds), rate limit 100/15min
- Paginação: cursor-based
- Validação: Zod

Siga padrões em specs/auth.yaml
```

**Benefícios**:
- ~50 palavras vs 500
- Estruturado e escaneável
- Referencia contexto existente

#### 📐 Fórmula de Prompt Eficiente

```markdown
[Ação clara] + [Contexto mínimo] + [Requisitos-chave] + [Referência]

Exemplo:
"Implemente endpoint POST /users seguindo spec em specs/users.yaml. 
Use AuthService para validação de token."
```

### 6. Usar Comentários Estratégicos

#### ✅ Comentários Como Contexto

```typescript
// specs/payments.yaml
openapi: 3.0.0
info:
  title: Payments API
  # IMPORTANTE: Seguir PCI-DSS compliance
  # Nunca logar dados de cartão
  # Usar Stripe para processamento
  description: API para processar pagamentos

paths:
  /payments:
    post:
      # Rate limit: 10 req/min por usuário
      # Timeout: 30s (processamento pode ser lento)
      summary: Criar novo pagamento
```

**Copilot usa esses comentários** para gerar código alinhado.

#### ✅ TODOs Como Guias

```typescript
// TODO: Implementar validação de cartão de crédito
// - Validar número com algoritmo de Luhn
// - Verificar data de expiração
// - CVV deve ter 3-4 dígitos
// - Nunca logar dados do cartão

export function validateCard(card: CardData): ValidationResult {
  // Copilot gera implementação baseada nos TODOs
}
```

---

## Estratégias de Performance para SDD

### 1. Dividir Specs Grandes

#### ❌ Problema: Spec Monolítica

```yaml
# specs/api.yaml (10000 linhas)
# Copilot não consegue processar tudo de uma vez
openapi: 3.0.0
paths:
  # 200 endpoints...
components:
  schemas:
    # 500 schemas...
```

#### ✅ Solução: Specs Modulares

```yaml
# specs/main.yaml
openapi: 3.0.0
paths:
  $ref: './paths/index.yaml'
components:
  $ref: './components/index.yaml'

# specs/paths/users.yaml
/users:
  get: { ... }
  post: { ... }

# specs/paths/payments.yaml
/payments:
  post: { ... }

# specs/components/schemas/user.yaml
User:
  type: object
  properties: { ... }
```

**Benefícios**:
- Copilot processa arquivo relevante
- Contexto focado
- Manutenção mais fácil

### 2. Usar Referências Inteligentes

#### ✅ Referenciar em Vez de Duplicar

```typescript
// ❌ Ruim: Duplicar contexto
/**
 * Cria usuário seguindo padrões:
 * - Bcrypt 12 rounds
 * - Email único
 * - Validação com Zod
 * [repete 50 linhas de padrões]
 */
export function createUser() { }

// ✅ Bom: Referenciar
/**
 * Cria usuário.
 * @see docs/auth-standards.md para padrões de segurança
 * @see specs/users.yaml para schema
 */
export function createUser() { }
```

### 3. Cachear Contexto com Snippets

#### Criar Snippets para Padrões Comuns

```json
// .vscode/sdd-snippets.code-snippets
{
  "OpenAPI Endpoint": {
    "prefix": "sdd-endpoint",
    "body": [
      "paths:",
      "  /${1:resource}:",
      "    ${2:get}:",
      "      summary: ${3:description}",
      "      tags: [${4:tag}]",
      "      parameters:",
      "        - $ref: '#/components/parameters/Pagination'",
      "      responses:",
      "        '200':",
      "          $ref: '#/components/responses/Success'",
      "        '401':",
      "          $ref: '#/components/responses/Unauthorized'"
    ]
  }
}
```

**Benefício**: Copilot autocompleta baseado no snippet, economizando contexto.

### 4. Usar Workspace Symbols

#### Aproveitar Indexação do VS Code

```typescript
// Copilot usa símbolos do workspace para sugestões

// Em vez de importar tudo:
import * as everything from './huge-file';

// Importe apenas o necessário:
import { UserService } from './services/user.service';
import { authSpec } from './specs/auth-spec';
```

**Copilot vê símbolos** sem precisar carregar arquivos inteiros.

---

## Limitações Específicas para SDD

### 1. Validação de Specs

#### ❌ Copilot NÃO Valida Automaticamente

```yaml
# Copilot pode gerar spec inválida
openapi: 3.0.0
paths:
  /users:
    get:
      # Faltando 'responses' (obrigatório)
      summary: Get users
```

#### ✅ Solução: Validação Explícita

```json
// .vscode/tasks.json
{
  "label": "Validate Spec",
  "type": "shell",
  "command": "spectral lint ${file}",
  "problemMatcher": "$spectral"
}
```

**Sempre validar** specs geradas pelo Copilot.

### 2. Consistência Entre Specs

#### ❌ Copilot Não Garante Consistência

```yaml
# specs/users.yaml
components:
  schemas:
    User:
      properties:
        id: { type: string, format: uuid }

# specs/posts.yaml (gerado separadamente)
components:
  schemas:
    Post:
      properties:
        userId: { type: integer }  # ❌ Inconsistente!
```

#### ✅ Solução: Schemas Compartilhados

```yaml
# specs/components/schemas/common.yaml
UserId:
  type: string
  format: uuid

# Referenciar em todas as specs
User:
  properties:
    id: { $ref: '#/components/schemas/UserId' }

Post:
  properties:
    userId: { $ref: '#/components/schemas/UserId' }
```

### 3. Geração de Código da Spec

#### ❌ Copilot Não Gera Código Automaticamente

Copilot **não** roda `openapi-generator` automaticamente.

#### ✅ Solução: Workflow Híbrido

```bash
# 1. Copilot gera spec
# 2. Você valida spec
spectral lint specs/users.yaml

# 3. Ferramenta gera código
openapi-generator-cli generate -i specs/users.yaml -g typescript-node

# 4. Copilot ajuda a customizar código gerado
```

---

## Medindo Performance

### Métricas de Eficiência

#### 1. Taxa de Aceitação de Sugestões

```
Alta eficiência: >70% de sugestões aceitas
Média eficiência: 40-70%
Baixa eficiência: <40%
```

**Como melhorar**:
- Refinar custom instructions
- Abrir arquivos relevantes
- Usar prompts mais específicos

#### 2. Tempo de Resposta do Chat

```
Rápido: <2s
Normal: 2-5s
Lento: >5s (contexto muito grande)
```

**Como melhorar**:
- Reduzir tamanho de prompts
- Fechar arquivos irrelevantes
- Limpar histórico de chat

#### 3. Qualidade das Sugestões

```
Excelente: Código pronto para produção
Bom: Requer ajustes mínimos
Ruim: Requer reescrita significativa
```

**Como melhorar**:
- Melhorar custom instructions
- Fornecer mais contexto via comentários
- Usar exemplos no workspace

---

## Checklist de Otimização

### Antes de Começar um Projeto SDD

- [ ] Custom instructions concisas (<300 palavras)
- [ ] Estrutura de specs modular (não monolítica)
- [ ] Snippets para padrões comuns criados
- [ ] Validação automática configurada (Spectral)
- [ ] README com padrões arquiteturais

### Durante o Desenvolvimento

- [ ] Abrir apenas arquivos relevantes (max 5-7 tabs)
- [ ] Usar prompts estruturados e concisos
- [ ] Validar specs geradas antes de gerar código
- [ ] Adicionar comentários estratégicos
- [ ] Limpar histórico de chat quando necessário

### Revisão de Performance

- [ ] Taxa de aceitação >60%?
- [ ] Tempo de resposta <5s?
- [ ] Specs geradas são válidas?
- [ ] Código gerado segue padrões?
- [ ] Equipe está satisfeita com Copilot?

---

## Comparação: Copilot vs Outras Ferramentas

### GitHub Copilot

**Pontos Fortes**:
- ✅ Integração nativa com VS Code
- ✅ Autocomplete extremamente rápido
- ✅ Bom para código boilerplate
- ✅ Aprende com código do workspace

**Limitações**:
- ❌ Contexto limitado (~8K tokens)
- ❌ Não executa código
- ❌ Não acessa arquivos fora do workspace (sem MCP)
- ❌ Memória não persiste entre sessões

### Continue + MCP

**Pontos Fortes**:
- ✅ Suporta MCP (acesso a specs via protocolo)
- ✅ RAG integrado possível
- ✅ Contexto customizável
- ✅ Múltiplos LLMs (GPT-4, Claude, etc)

**Limitações**:
- ❌ Requer configuração manual
- ❌ Autocomplete menos integrado que Copilot
- ❌ Curva de aprendizado maior

### Cline (Claude Dev)

**Pontos Fortes**:
- ✅ Agent autônomo (executa tarefas completas)
- ✅ Suporta MCP
- ✅ Pode executar comandos
- ✅ Memória de contexto maior (Claude 3.5)

**Limitações**:
- ❌ Mais lento que Copilot autocomplete
- ❌ Requer aprovação para ações
- ❌ Custo mais alto (API Claude)

### Recomendação para SDD

```
Ideal: Combinar ferramentas

1. GitHub Copilot: Autocomplete diário
2. Continue + MCP: Geração de specs com contexto
3. Cline: Workflows complexos (Specify → Plan → Tasks → Implement)

Cada ferramenta para seu ponto forte.
```

---

## Recursos Adicionais

### Documentação Oficial
- **[GitHub Copilot Docs](https://docs.github.com/copilot)**: Documentação completa
- **[Copilot Best Practices](https://github.blog/2023-06-20-how-to-write-better-prompts-for-github-copilot/)**: Guia oficial de prompts
- **[Copilot Patterns](https://github.com/copilot-patterns)**: Padrões da comunidade

### Ferramentas Complementares
- **[Spectral](https://stoplight.io/open-source/spectral)**: Validação de specs
- **[OpenAPI Generator](https://openapi-generator.tech/)**: Geração de código
- **[Continue.dev](https://continue.dev)**: MCP + RAG

---

## Conclusão

**Performance com GitHub Copilot** depende de:

1. **Custom instructions concisas** (<300 palavras)
2. **Contexto otimizado** (arquivos modulares, tabs relevantes)
3. **Prompts estruturados** (claros, concisos, com referências)
4. **Validação externa** (Spectral, testes)
5. **Ferramentas complementares** (Continue/Cline para workflows complexos)

**Para SDD especificamente**:
- Use Copilot para autocomplete e geração rápida
- Use Continue/Cline + MCP para workflows estruturados
- Sempre valide specs geradas
- Mantenha specs modulares e focadas

**Lembre-se**: Copilot é uma ferramenta, não uma solução mágica. Performance depende de como você a usa.
