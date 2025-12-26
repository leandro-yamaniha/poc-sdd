# Biblioteca de Prompts para SDD

Esta biblioteca contém prompts testados e otimizados para cada fase do Spec-Driven Development.

> **Nota sobre Adaptabilidade**: Os prompts neste documento usam exemplos em tecnologias específicas (Node.js, React, Python, etc.) para fins ilustrativos. A metodologia SDD é **agnóstica de tecnologia**. Adapte os prompts substituindo as tecnologias mencionadas pela sua stack (Java/Spring, .NET/C#, Go, Ruby, mobile nativo, etc.).

## Índice
- [Fase 1: Design da Especificação](#fase-1-design-da-especificação)
- [Fase 2: Review e Governança](#fase-2-review-e-governança)
- [Fase 3: Implementação](#fase-3-implementação)
- [Fase 4: Testes](#fase-4-testes)
- [Fase 5: Documentação](#fase-5-documentação)

---

## Fase 1: Design da Especificação

### Prompt 1.1: Geração de Spec Completa

```
Você é um arquiteto de APIs especializado em OpenAPI 3.0.

CONTEXTO:
Estou desenvolvendo [DESCRIÇÃO DO SISTEMA/DOMÍNIO].

REQUISITOS:
[LISTAR REQUISITOS FUNCIONAIS]

TAREFA:
Crie uma especificação OpenAPI 3.0 completa que inclua:

1. Metadata (info, servers)
2. Todos os endpoints necessários com:
   - Métodos HTTP apropriados
   - Parâmetros (path, query, header)
   - Request bodies com schemas
   - Responses com todos os códigos de status relevantes
3. Schemas reutilizáveis em components/schemas
4. Validações (required, min/max, patterns, enums)
5. Exemplos realistas para cada schema
6. Tags para organização
7. Security schemes (se aplicável)

RESTRIÇÕES:
- Siga convenções REST
- Use snake_case para propriedades JSON
- Inclua descrições em português
- Adicione x-* extensions para metadados customizados se necessário
```

### Prompt 1.2: Conversão de Requisitos para Spec

```
Tenho os seguintes requisitos de negócio:

"""
[COLAR REQUISITOS EM LINGUAGEM NATURAL]
"""

Converta isso em uma especificação OpenAPI 3.0, identificando:
1. Recursos (substantivos) → endpoints
2. Ações (verbos) → métodos HTTP
3. Regras de negócio → validações nos schemas
4. Fluxos → sequência de operações com operationId

Para cada endpoint, defina:
- Autenticação necessária
- Permissões (roles)
- Rate limiting esperado
- Idempotência
```

### Prompt 1.3: Refinamento de Schema

```
Analise este schema OpenAPI:

"""yaml
[COLAR SCHEMA]
"""

Melhore-o considerando:
1. **Validações**: adicione constraints apropriados (minLength, maxLength, pattern, format)
2. **Reutilização**: identifique sub-schemas que podem ser extraídos
3. **Documentação**: adicione descriptions claras
4. **Exemplos**: forneça 2-3 exemplos diversos
5. **Nullable vs Optional**: corrija uso de nullable e required
6. **Tipos**: verifique se format está correto (email, uuid, date-time, etc)
```

---

## Fase 2: Review e Governança

### Prompt 2.1: Auditoria de Qualidade

```
Você é um revisor técnico especializado em APIs RESTful.

Analise esta especificação OpenAPI:

"""yaml
[COLAR SPEC]
"""

Identifique problemas em:

**Consistência**
- Nomenclatura inconsistente (camelCase vs snake_case)
- Estruturas de resposta diferentes para recursos similares
- Códigos de status HTTP inadequados

**Completude**
- Endpoints sem descrição
- Schemas sem exemplos
- Operações sem tags
- Falta de documentação de erros

**Segurança**
- Endpoints sem autenticação definida
- Dados sensíveis sem marcação
- Falta de rate limiting

**Performance**
- Endpoints sem paginação
- Responses muito grandes sem filtros
- Falta de cache headers

Forneça relatório estruturado com severidade (crítico/alto/médio/baixo).
```

### Prompt 2.2: Geração de Regras Spectral

```
Com base nesta especificação OpenAPI e nos padrões identificados:

"""yaml
[COLAR SPEC]
"""

Gere um arquivo .spectral.yaml com regras customizadas que:

1. Forcem padrões de nomenclatura observados
2. Exijam campos obrigatórios em info (contact, license)
3. Validem estrutura de erros padronizada
4. Garantam que todos os endpoints têm examples
5. Verifiquem versionamento correto nas URLs
6. Assegurem que operações de mutação (POST/PUT/PATCH/DELETE) têm security

Use severity: error para regras críticas e warning para recomendações.
```

### Prompt 2.3: Análise de Breaking Changes

```
Compare estas duas versões da especificação:

**Versão Anterior (v1.0.0)**:
"""yaml
[COLAR SPEC V1]
"""

**Versão Nova (v2.0.0)**:
"""yaml
[COLAR SPEC V2]
"""

Identifique:

**Breaking Changes** (incompatibilidades que quebram clientes existentes):
- Endpoints removidos
- Campos obrigatórios adicionados em requests
- Campos removidos de responses
- Tipos alterados
- Enums com valores removidos

**Non-Breaking Changes**:
- Novos endpoints
- Novos campos opcionais
- Depreciações (ainda funcionais)

**Recomendações**:
- Se há breaking changes, versão deveria ser 2.0.0?
- Estratégia de migração para clientes
```

### Prompt 2.4: Auditoria de Segurança da Spec (Security-By-Spec)

```
Você é um especialista em segurança de APIs e em Security-By-Spec.

Objetivo:
Auditar a segurança desta especificação (OpenAPI/AsyncAPI) com base em um checklist
de segurança orientado a SDD.

Especificação:

"""yaml
[COLAR SPEC]
"""

Tarefa:
Analise a spec e produza um relatório estruturado cobrindo, no mínimo, os itens abaixo:

1. Autenticação e autorização
   - Se existem securitySchemes definidos
   - Se endpoints sensíveis exigem autenticação
   - Se existem escopos/roles claros por operação

2. Códigos de status relacionados à segurança
   - Uso consistente de 401 (credenciais ausentes/inválidas)
   - Uso consistente de 403 (credenciais válidas, mas sem permissão)
   - Presença de 429 para rate limiting (quando aplicável)
   - Schemas de erro documentados para respostas de segurança

3. Dados sensíveis e PII
   - Campos que carregam dados pessoais/sensíveis
   - Riscos de vazamento de dados em responses e mensagens de erro
   - Possíveis necessidades de mascaramento/anonimização

4. Limites e restrições de uso
   - Rate limiting e quotas documentados
   - Tamanho máximo de payload e outras restrições relevantes
   - Headers ou mecanismos de feedback de limites (ex.: X-RateLimit-*)

5. Auditoria e compliance
   - Operações críticas identificadas (ex.: mudanças de permissão, deleção de dados)
   - Metadados de auditoria previstos (createdBy, createdAt, lastModifiedBy etc.)
   - Pontos que podem impactar LGPD/GDPR ou outras regulações

6. Canais assíncronos (se houver AsyncAPI)
   - Quem pode publicar/consumir em cada canal
   - Mensagens que carregam dados sensíveis
   - Estratégia de dead letters e tratamento de erros sem vazamento de dados

Formato do resultado:
- Liste os achados organizados por seção do checklist acima.
- Para cada achado, indique:
  - Severidade (crítico/alto/médio/baixo)
  - Descrição
  - Trechos da spec relacionados (paths, operations, schemas, canais)
  - Recomendações de correção/melhoria

Se apropriado, sugira exemplos concretos de alterações na spec para corrigir
os problemas mais críticos.
```

---

## Fase 3: Implementação

### Prompt 3.1: Geração de Backend (Node.js/Express)

```
A partir desta especificação OpenAPI:

"""yaml
[COLAR SPEC]
"""

Gere implementação completa em Node.js + Express + TypeScript:

**Estrutura**:
```
src/
├── routes/
│   └── [resource].routes.ts
├── controllers/
│   └── [resource].controller.ts
├── services/
│   └── [resource].service.ts
├── models/
│   └── [resource].model.ts
├── types/
│   └── api.types.ts (gerados da spec)
├── middleware/
│   ├── auth.middleware.ts
│   └── validation.middleware.ts
└── app.ts
```

**Requisitos**:
1. Use express-openapi-validator para validação automática
2. Gere interfaces TypeScript para todos os schemas
3. Implemente error handling padronizado
4. Adicione logging estruturado (winston/pino)
5. Configure CORS e helmet
6. Inclua health check endpoint

**Para cada endpoint**:
- Controller com tipagem forte
- Service layer com lógica de negócio (mock inicial)
- Tratamento de erros específicos
```

### Prompt 3.2: Geração de Frontend (React + TypeScript)

```
Da especificação OpenAPI:

"""yaml
[COLAR SPEC]
"""

Gere código React + TypeScript:

**1. Tipos**:
- Interfaces para todos os schemas
- Tipos para request/response de cada operação

**2. API Client**:
- Cliente axios configurado
- Interceptors para auth e error handling
- Funções tipadas para cada operação

**3. React Query Hooks**:
- Custom hooks para cada operação (useGetUsers, useCreateUser, etc)
- Configuração de cache adequada
- Otimistic updates para mutations

**4. Componentes de Formulário**:
- Forms com validação usando react-hook-form + zod
- Schemas zod derivados dos schemas OpenAPI

**Exemplo de estrutura**:
```typescript
// types/api.ts
export interface User { ... }

// api/users.api.ts
export const usersApi = { ... }

// hooks/useUsers.ts
export const useUsers = () => useQuery(...)
export const useCreateUser = () => useMutation(...)

// components/UserForm.tsx
export const UserForm = () => { ... }
```
```

### Prompt 3.3: Configuração de Mock Server

```
Configure Prism mock server para esta spec:

"""yaml
[COLAR SPEC]
"""

**Requisitos**:

1. **docker-compose.yml** para rodar Prism
2. **Script npm** para iniciar mock server
3. **Configuração** para:
   - Retornar exemplos dinâmicos (não estáticos)
   - Simular latência (200-500ms)
   - Validar requests contra schema
   - Logar todas as chamadas
   - Ocasionalmente retornar erros (5% 500, 10% 4xx)

4. **Seed data** realista para responses

5. **README** explicando como usar o mock para desenvolvimento frontend
```

---

## Fase 4: Testes

### Prompt 4.1: Geração de Testes de Contrato

```
Gere suite completa de testes de contrato usando Dredd para:

"""yaml
[COLAR SPEC]
"""

**Requisitos**:

1. **dredd.yml** configurado
2. **Hooks** para:
   - Setup de dados de teste
   - Autenticação (tokens)
   - Cleanup após testes
3. **Testes para**:
   - Todos os endpoints
   - Todos os códigos de status documentados
   - Validação de schemas de response
   - Headers obrigatórios

**Estrutura**:
```
tests/
├── dredd.yml
├── hooks/
│   ├── setup.ts
│   └── auth.ts
└── fixtures/
    └── test-data.json
```

Inclua script npm para rodar: `npm run test:contract`
```

### Prompt 4.2: Testes de Carga (K6)

```
Crie script K6 de teste de carga baseado nesta spec:

"""yaml
[COLAR SPEC]
"""

**Cenário**:
- 100 usuários virtuais
- Duração: 5 minutos
- Ramp-up: 30 segundos

**Mix de Operações**:
- 60% GET (leitura)
- 25% POST (criação)
- 10% PUT (atualização)
- 5% DELETE (remoção)

**Validações**:
- Status codes corretos
- Response time p95 < 500ms
- Schemas de response válidos

**Métricas**:
- Requests por segundo
- Latência (p50, p95, p99)
- Taxa de erro

Gere script k6.js completo e executável.
```

### Prompt 4.3: Análise de Cobertura

```
Analise a cobertura de testes desta API:

**Especificação**:
"""yaml
[COLAR SPEC]
"""

**Testes Existentes**:
"""
[COLAR CÓDIGO DE TESTES]
"""

Identifique:

**Gaps de Cobertura**:
- Endpoints sem testes
- Códigos de status não testados
- Schemas com campos nunca validados
- Cenários de erro não cobertos

**Recomendações**:
- Priorize testes faltantes por criticidade
- Sugira casos de teste adicionais
- Identifique testes redundantes

Gere relatório em formato markdown com checklist.
```

---

## Fase 5: Documentação

### Prompt 5.1: Guia de Início Rápido

```
Crie um guia "Getting Started" para esta API:

"""yaml
[COLAR SPEC]
"""

**Estrutura**:

1. **Introdução**
   - O que a API faz
   - Casos de uso principais

2. **Autenticação**
   - Como obter credenciais
   - Como usar tokens
   - Exemplo de request autenticado

3. **Primeiro Request**
   - Exemplo completo passo a passo
   - cURL, JavaScript e Python

4. **Fluxo Completo**
   - Exemplo de caso de uso real
   - Sequência de chamadas
   - Tratamento de erros

5. **Próximos Passos**
   - Links para referência completa
   - SDKs disponíveis
   - Suporte

Formato: Markdown, tom amigável, exemplos executáveis.
```

### Prompt 5.2: Postman Collection

```
Gere Postman Collection v2.1 completa para:

"""yaml
[COLAR SPEC]
"""

**Requisitos**:

1. **Organização**:
   - Pastas por recurso/tag
   - Requests nomeados claramente

2. **Variáveis**:
   - {{baseUrl}}
   - {{authToken}}
   - IDs de recursos criados

3. **Pre-request Scripts**:
   - Geração de dados dinâmicos
   - Refresh de tokens

4. **Tests**:
   - Validação de status code
   - Validação de schema
   - Extração de IDs para próximas requests

5. **Documentação**:
   - Descrição em cada request
   - Exemplos de responses

Gere JSON da collection pronto para importar.
```

### Prompt 5.3: Changelog Automático

```
Compare estas versões da spec e gere changelog:

**v1.0.0**:
"""yaml
[COLAR SPEC V1]
"""

**v2.0.0**:
"""yaml
[COLAR SPEC V2]
"""

**Formato**:

# Changelog

## [2.0.0] - YYYY-MM-DD

### 🚨 Breaking Changes
- [Listar mudanças incompatíveis]

### ✨ New Features
- [Novos endpoints/funcionalidades]

### 🔧 Changes
- [Modificações não-breaking]

### 🗑️ Deprecated
- [Recursos marcados para remoção]

### 🐛 Fixed
- [Correções na spec]

### 📝 Documentation
- [Melhorias na documentação]

Siga formato Keep a Changelog.
```

---

## Prompts Utilitários

### Conversão: Postman → OpenAPI

```
Converta esta Postman Collection para OpenAPI 3.0:

"""json
[COLAR COLLECTION]
"""

Extraia:
- Endpoints e métodos
- Headers
- Request bodies
- Exemplos de responses
- Variáveis → parameters

Gere spec OpenAPI válida e completa.
```

### Conversão: Swagger 2.0 → OpenAPI 3.0

```
Migre esta spec Swagger 2.0 para OpenAPI 3.0:

"""yaml
[COLAR SWAGGER 2.0]
"""

Atualize:
- host/basePath → servers
- definitions → components/schemas
- securityDefinitions → components/securitySchemes
- Adicione examples onde faltam
- Use oneOf/anyOf onde apropriado
```

### Geração de Diagramas

```
Crie diagrama Mermaid representando esta API:

"""yaml
[COLAR SPEC]
"""

Gere:
1. **Diagrama de Arquitetura** (C4 Level 2)
   - Cliente → API → Recursos

2. **Diagrama de Sequência** para fluxo principal
   - Autenticação
   - Operação CRUD completa

3. **Diagrama de Entidades**
   - Schemas e relacionamentos

Use sintaxe Mermaid válida.
```

---

## Dicas de Uso

### Contexto é Fundamental
Sempre forneça:
- Especificação completa (não trechos)
- Linguagem/framework alvo
- Restrições específicas do projeto

### Iteração
Use prompts em sequência:
1. Geração inicial
2. Refinamento
3. Validação
4. Ajustes finais

### Validação
Sempre valide output da IA:
- Rode linters (Spectral)
- Teste código gerado
- Revise lógica de negócio

### Customização
Adapte prompts para:
- Convenções da sua empresa
- Stack tecnológica específica
- Requisitos de compliance
