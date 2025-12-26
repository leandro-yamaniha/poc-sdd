# Casos de Uso: SDD com IA na Prática

Este documento apresenta cenários reais de aplicação da metodologia Spec-Driven Development otimizada com Inteligência Artificial.

> **Nota sobre Exemplos**: Os casos de uso apresentam tecnologias específicas (PHP, Node.js, Rails, etc.) para contextualização. A metodologia SDD é **agnóstica de tecnologia** e os mesmos princípios se aplicam a qualquer stack. Adapte os exemplos para sua realidade tecnológica.

## Índice
- [Caso 1: Migração de API Legacy](#caso-1-migração-de-api-legacy)
- [Caso 2: Nova Feature em Produto Existente](#caso-2-nova-feature-em-produto-existente)
- [Caso 3: Microserviço do Zero](#caso-3-microserviço-do-zero)
- [Caso 4: Integração com API de Terceiros](#caso-4-integração-com-api-de-terceiros)
- [Caso 5: Refatoração de Monolito para Microserviços](#caso-5-refatoração-de-monolito-para-microserviços)

---

## Caso 1: Migração de API Legacy

### Contexto
Empresa tem API REST sem documentação formal, apenas código PHP legado. Precisa migrar para Node.js com documentação adequada.

### Desafio
- Código sem tipagem
- Comportamento não documentado
- Múltiplos consumidores (mobile, web, parceiros)
- Não pode quebrar compatibilidade

### Solução SDD + IA

#### Fase 1: Engenharia Reversa da Spec

**Passo 1.1**: Extrair rotas do código legado
```bash
# Usar grep para encontrar definições de rotas
grep -r "->get\|->post\|->put\|->delete" src/
```

**Passo 1.2**: Usar IA para gerar spec inicial

**Prompt**:
```
Analise este código PHP e gere uma especificação OpenAPI 3.0:

"""php
[COLAR CÓDIGO DAS ROTAS]
"""

Para cada rota, identifique:
- Path e método HTTP
- Parâmetros (query, path, body)
- Tipo de resposta (inferir do código)
- Códigos de status retornados
- Autenticação necessária

Gere spec completa mesmo que faltem informações (use 'unknown' onde necessário).
```

**Passo 1.3**: Validar spec com tráfego real

Usar proxy para capturar requests/responses reais e comparar com a spec:
```javascript
// middleware de logging
app.use((req, res, next) => {
  const oldSend = res.send;
  res.send = function(data) {
    // Validar contra OpenAPI spec
    validateResponse(req.path, req.method, res.statusCode, data);
    oldSend.apply(res, arguments);
  };
  next();
});
```

**Passo 1.4**: Refinar spec com IA

**Prompt**:
```
Tenho esta spec gerada por engenharia reversa:
"""yaml
[SPEC INICIAL]
"""

E estes exemplos de requests/responses reais capturados:
"""json
[LOGS DE TRÁFEGO]
"""

Refine a spec para:
1. Corrigir tipos inferidos incorretamente
2. Adicionar campos que estavam faltando
3. Documentar comportamentos observados
4. Adicionar validações baseadas nos dados reais
```

#### Fase 2: Implementação da Nova API

**Passo 2.1**: Gerar código Node.js da spec

**Prompt**:
```
Da spec OpenAPI validada, gere API Node.js + Express + TypeScript que:
1. Mantenha 100% de compatibilidade com comportamento legado
2. Use mesmos códigos de status
3. Retorne mesmos campos (mesmo que deprecated)
4. Adicione novos campos como opcionais
5. Implemente feature flags para transição gradual
```

**Passo 2.2**: Testes de compatibilidade

Gerar testes que garantem paridade:
```typescript
// Comparar responses da API legada vs nova
describe('Compatibility Tests', () => {
  it('GET /users retorna mesma estrutura', async () => {
    const legacyResponse = await legacyApi.get('/users');
    const newResponse = await newApi.get('/users');
    
    expect(newResponse.data).toMatchStructure(legacyResponse.data);
  });
});
```

#### Fase 3: Migração Gradual

1. **Canary Release**: 5% do tráfego para nova API
2. **Monitoramento**: Comparar métricas (latência, erros)
3. **Ajustes**: Corrigir discrepâncias encontradas
4. **Rollout**: Aumentar gradualmente até 100%

### Resultados
- ✅ Migração sem downtime
- ✅ Documentação completa gerada
- ✅ Testes automatizados
- ✅ Base para futuras melhorias

---

## Caso 2: Nova Feature em Produto Existente

### Contexto
Produto SaaS precisa adicionar sistema de notificações (email, push, SMS).

### Desafio
- Feature complexa com múltiplos canais
- Precisa integrar com frontend existente
- Time frontend e backend trabalham em paralelo
- Prazo apertado (2 sprints)

### Solução SDD + IA

#### Sprint 1: Design e Desenvolvimento Paralelo

**Dia 1-2: Design da Spec com IA**

**Prompt**:
```
Desenhe API REST para sistema de notificações com:

REQUISITOS:
- Criar templates de notificação (título, corpo, variáveis)
- Enviar notificação (escolher canais: email, push, sms)
- Listar histórico de notificações enviadas
- Configurar preferências do usuário (opt-in/opt-out por canal)
- Agendar notificações futuras
- Suporte a notificações em lote

RESTRIÇÕES:
- Autenticação JWT
- Paginação em listagens
- Rate limiting: 100 req/min
- Webhooks para status de entrega

Gere OpenAPI 3.0 completa com exemplos.
```

**Dia 2: Review em Equipe**
- Product Owner valida fluxos
- Frontend valida estruturas de dados
- Backend valida viabilidade técnica
- Ajustes na spec

**Dia 3: Desenvolvimento Paralelo Inicia**

**Backend**:
```bash
# Gerar código base
npx openapi-generator-cli generate \
  -i specs/notifications.yaml \
  -g nodejs-express-server \
  -o backend/
```

**Frontend**:
```bash
# Gerar cliente tipado
npx openapi-typescript specs/notifications.yaml \
  --output src/types/notifications.ts

# Iniciar mock server
npx @stoplight/prism-cli mock specs/notifications.yaml
```

**Dia 3-10: Implementação**

Frontend desenvolve UI completa usando mock server:
```typescript
// Frontend usa tipos gerados
import { NotificationTemplate } from '@/types/notifications';

const { data, isLoading } = useQuery<NotificationTemplate[]>(
  'templates',
  () => api.notifications.listTemplates()
);
```

Backend implementa lógica real:
```typescript
// Backend implementa interface gerada
class NotificationsController implements INotificationsController {
  async createTemplate(req: Request, res: Response) {
    // Validação automática via spec
    const template = await notificationService.create(req.body);
    res.status(201).json(template);
  }
}
```

#### Sprint 2: Integração e Testes

**Dia 11: Integração**
- Frontend troca mock server por backend real
- Ajustes de última hora (se necessário)

**Dia 12-13: Testes com IA**

**Prompt para gerar testes**:
```
Gere suite completa de testes para esta API de notificações:

"""yaml
[SPEC]
"""

Inclua:
1. Testes unitários para cada endpoint
2. Testes de integração para fluxos completos
3. Testes de carga (1000 notificações/segundo)
4. Testes de falha (rate limiting, validações)
5. Testes de segurança (autenticação, autorização)

Use Jest + Supertest.
```

**Dia 14: Deploy**
- CI/CD valida spec com Spectral
- Contract tests garantem compatibilidade
- Deploy em produção

### Resultados
- ✅ Feature entregue em 2 sprints
- ✅ Zero bloqueios entre times
- ✅ Documentação pronta no dia 1
- ✅ Testes abrangentes

---

## Caso 3: Microserviço do Zero

### Contexto
Startup precisa criar serviço de pagamentos para marketplace.

### Desafio
- Sistema crítico (dinheiro envolvido)
- Compliance (PCI-DSS)
- Alta disponibilidade necessária
- Múltiplos métodos de pagamento

### Solução SDD + IA

#### Fase 1: Design Colaborativo com IA

**Sessão de Design Thinking**:
1. Listar casos de uso (checkout, reembolso, split payment)
2. Identificar entidades (Payment, Transaction, PaymentMethod)
3. Definir fluxos críticos

**Prompt para IA**:
```
Você é arquiteto de sistemas de pagamento.

Desenhe API OpenAPI 3.0 para serviço de pagamentos com:

FUNCIONALIDADES:
- Processar pagamento (cartão, boleto, PIX)
- Webhooks de status (pending, approved, failed)
- Reembolsos parciais e totais
- Split de pagamento (marketplace)
- Tokenização de cartões (PCI compliance)
- Histórico de transações

REQUISITOS NÃO-FUNCIONAIS:
- Idempotência (usar Idempotency-Key header)
- Retry automático com backoff
- Auditoria completa (logs imutáveis)
- Rate limiting agressivo
- Autenticação mútua (mTLS)

SEGURANÇA:
- Nunca retornar número de cartão completo
- Criptografar dados sensíveis
- Validar CVV mas não armazenar
- Compliance PCI-DSS Level 1

Gere spec detalhada com foco em segurança e resiliência.
```

#### Fase 2: Validação de Segurança

**Prompt**:
```
Audite esta spec de API de pagamentos:

"""yaml
[SPEC]
"""

Identifique vulnerabilidades:
1. Dados sensíveis expostos
2. Falta de rate limiting
3. Endpoints sem autenticação
4. Ausência de idempotência
5. Logs que podem vazar informações
6. Validações de entrada insuficientes

Para cada problema, sugira correção na spec.
```

#### Fase 3: Implementação com Foco em Qualidade

**Geração de código**:
```
Gere implementação Golang desta API de pagamentos:

Requisitos:
- Arquitetura hexagonal (ports & adapters)
- Repository pattern para persistência
- Circuit breaker para chamadas externas (Stripe, PagSeguro)
- Distributed tracing (OpenTelemetry)
- Métricas Prometheus
- Health checks (liveness, readiness)
- Graceful shutdown

Inclua:
- Testes unitários (>80% cobertura)
- Testes de integração com testcontainers
- Testes de contrato com Pact
```

#### Fase 4: Testes de Carga e Caos

**Prompt**:
```
Crie cenários de teste de carga e caos para API de pagamentos:

CARGA:
- 10.000 transações/minuto
- 95% aprovadas, 5% recusadas
- Mix: 60% cartão, 30% PIX, 10% boleto

CAOS:
- Simular falha de gateway de pagamento
- Simular latência alta (>5s)
- Simular database connection pool esgotado
- Simular rate limiting ativado

Validar:
- Sistema mantém SLA (99.9% uptime)
- Nenhuma transação perdida
- Retries funcionam corretamente
- Circuit breaker abre quando necessário

Gere scripts K6 + Chaos Mesh.
```

### Resultados
- ✅ Sistema em produção em 6 semanas
- ✅ Zero incidentes de segurança
- ✅ SLA 99.95% no primeiro mês
- ✅ Auditoria PCI-DSS aprovada

---

## Caso 4: Integração com API de Terceiros

### Contexto
Integrar com API de CRM externo (Salesforce, HubSpot) que tem documentação ruim.

### Desafio
- Documentação incompleta/desatualizada
- Comportamento inconsistente
- Precisa abstrair diferenças entre provedores

### Solução SDD + IA

#### Fase 1: Engenharia Reversa com IA

**Prompt**:
```
Analise esta documentação de API e exemplos de uso:

DOCUMENTAÇÃO:
"""
[COLAR DOCS DO SALESFORCE]
"""

EXEMPLOS DE CÓDIGO:
"""python
[COLAR EXEMPLOS DA COMUNIDADE]
"""

Gere especificação OpenAPI 3.0 que:
1. Normalize inconsistências
2. Adicione informações faltantes
3. Documente comportamentos não óbvios
4. Inclua rate limits observados
5. Mapeie códigos de erro

Foque nos endpoints: Contacts, Leads, Opportunities.
```

#### Fase 2: Camada de Abstração

**Prompt**:
```
Crie especificação OpenAPI para API unificada de CRM que abstrai:
- Salesforce
- HubSpot
- Pipedrive

Requisitos:
- Modelo de dados comum (Contact, Deal, Company)
- Mapeamento de campos específicos de cada provedor
- Webhooks padronizados
- Sincronização bidirecional

Gere spec que serve como contrato para nossa aplicação.
```

**Implementação**:
```typescript
// Adapter pattern gerado por IA
interface CRMAdapter {
  createContact(data: Contact): Promise<Contact>;
  updateContact(id: string, data: Partial<Contact>): Promise<Contact>;
}

class SalesforceAdapter implements CRMAdapter {
  async createContact(data: Contact): Promise<Contact> {
    // Mapear Contact → Salesforce Lead
    const sfLead = this.mapToSalesforce(data);
    const result = await this.sfClient.create('Lead', sfLead);
    return this.mapFromSalesforce(result);
  }
}
```

#### Fase 3: Testes de Integração

**Prompt**:
```
Gere testes de integração para adaptadores de CRM:

Para cada provedor (Salesforce, HubSpot):
1. Testes CRUD completos
2. Testes de sincronização
3. Testes de rate limiting
4. Testes de retry em falhas
5. Testes de mapeamento de campos

Use mocks para testes unitários e sandbox real para E2E.
```

### Resultados
- ✅ Integração com 3 CRMs em 4 semanas
- ✅ Camada de abstração reutilizável
- ✅ Fácil adicionar novos provedores
- ✅ Testes garantem compatibilidade

---

## Caso 5: Refatoração de Monolito para Microserviços

### Contexto
Monolito Rails com 200k linhas precisa ser dividido em microserviços.

### Desafio
- Identificar boundaries corretos
- Extrair sem quebrar funcionalidades
- Manter monolito funcionando durante transição
- Evitar "distributed monolith"

### Solução SDD + IA

#### Fase 1: Análise do Monolito com IA

**Prompt**:
```
Analise esta estrutura de código Rails:

MODELS:
"""ruby
[LISTAR MODELS]
"""

CONTROLLERS:
"""ruby
[LISTAR CONTROLLERS]
"""

Sugira decomposição em microserviços:
1. Identifique bounded contexts (DDD)
2. Analise acoplamento entre módulos
3. Sugira ordem de extração (menos acoplado primeiro)
4. Identifique dados compartilhados (problemas potenciais)

Para cada microserviço sugerido:
- Nome
- Responsabilidades
- Entidades
- APIs necessárias (internas e externas)
```

#### Fase 2: Design das APIs dos Microserviços

**Prompt para cada serviço**:
```
Desenhe API OpenAPI 3.0 para microserviço de [NOME]:

CONTEXTO:
Extraído de monolito Rails. Atualmente implementado em:
"""ruby
[CÓDIGO RELEVANTE]
"""

REQUISITOS:
- Manter compatibilidade com monolito (durante transição)
- Expor apenas o necessário (não vazar detalhes internos)
- Suportar comunicação síncrona (REST) e assíncrona (eventos)
- Versionamento desde o início

Gere spec completa incluindo:
- API REST pública
- API REST interna (service-to-service)
- Eventos publicados (AsyncAPI)
```

#### Fase 3: Strangler Fig Pattern

**Implementação gradual**:

1. **Criar microserviço** com API definida
2. **Proxy no monolito** redireciona requests
3. **Dual-write** temporário (monolito e microserviço)
4. **Validação** de consistência
5. **Cutover** completo
6. **Remover código** do monolito

**Prompt para geração de proxy**:
```
Gere proxy em Rails que:
1. Intercepta requests para /api/users/*
2. Encaminha para microserviço de Users
3. Mantém fallback para código legado (feature flag)
4. Loga discrepâncias entre respostas
5. Coleta métricas (latência, taxa de erro)

Use Faraday para HTTP client.
```

#### Fase 4: Testes de Integração E2E

**Prompt**:
```
Gere testes E2E que validam sistema híbrido (monolito + microserviços):

CENÁRIOS:
1. Fluxo completo de checkout (toca múltiplos serviços)
2. Consistência eventual (eventos processados corretamente)
3. Fallback quando microserviço está down
4. Performance não degradou (comparar com baseline)

Use Cypress para testes de UI e Postman para APIs.
```

### Resultados
- ✅ 5 microserviços extraídos em 6 meses
- ✅ Zero downtime durante migração
- ✅ Cada serviço com spec e testes próprios
- ✅ Monolito reduzido em 60%

---

## Lições Aprendidas

### O que Funciona Bem

1. **IA para Boilerplate**: Gerar código repetitivo (controllers, types, testes básicos)
2. **IA para Documentação**: Transformar specs em guias legíveis
3. **IA para Análise**: Identificar inconsistências e problemas de segurança
4. **Specs como Contrato**: Elimina ambiguidade entre times

### Armadilhas Comuns

1. **Confiar Cegamente na IA**: Sempre revisar código gerado
2. **Specs Muito Genéricas**: Falta de exemplos e validações
3. **Ignorar Versionamento**: Specs devem ter versionamento semântico
4. **Over-Engineering**: Começar simples, evoluir conforme necessário

### Métricas de Sucesso

- **Time-to-Market**: 40-60% mais rápido com SDD + IA
- **Bugs em Produção**: 30% menos (validação automática)
- **Tempo de Onboarding**: 50% menos (documentação sempre atualizada)
- **Satisfação do Time**: Menos trabalho manual, mais criatividade

---

## Próximos Passos

Escolha um caso de uso similar ao seu contexto e:

1. Adapte os prompts para sua realidade
2. Comece pequeno (uma feature, não o sistema inteiro)
3. Meça resultados (tempo, qualidade, satisfação)
4. Itere e melhore o processo
5. Compartilhe aprendizados com o time

**Recursos Adicionais**:
- [Biblioteca de Prompts](prompts-library.md)
- [AI-Enhanced SDD](../ai-ides/ai-enhanced-sdd.md)
- [Workflow Detalhado](workflow.md)
