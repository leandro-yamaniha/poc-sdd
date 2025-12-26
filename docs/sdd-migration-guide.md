# Como usar SDD em migrações de stack (libs, linguagens e frameworks)

## Objetivo deste guia

Este documento mostra como aplicar **Spec-Driven Development (SDD)** em projetos de **migração de versões de bibliotecas, linguagens e frameworks**, com foco em:

- **Manter compatibilidade funcional** (comportamento externo estável)
- **Controlar riscos** (bugs, regressões, falhas de segurança)
- **Preservar ou melhorar performance** (latência, throughput, consumo de recursos)
- **Tornar o processo repetível e auditável**

Em vez de "sair reescrevendo" código, o SDD coloca a **especificação como trilho da migração**.

---

## Quando faz sentido usar SDD em migrações

Alguns cenários típicos:

- Atualizar **versões major** de bibliotecas críticas (ex.: HTTP client, ORM, framework web)
- Migrar de uma **versão de linguagem** para outra (ex.: Node.js 16 → 20, Java 8 → 17)
- Trocar ou atualizar **frameworks** (ex.: Spring Boot 2 → 3, AngularJS → React, Express → NestJS)
- Dividir ou consolidar serviços mantendo o **mesmo contrato externo** (ex.: monólito → microsserviços)

Em todos esses casos, o SDD ajuda a:

- Tornar explícito **o que não pode quebrar** (contratos e SLAs/SLOs)
- Planejar a migração em **incrementos pequenos**
- Automatizar **validação de compatibilidade, qualidade e performance**

---

## Princípios de migração orientada a especificações

1. **Spec como contrato de compatibilidade**
   - Descreva, em OpenAPI/AsyncAPI/GraphQL schema/etc., **como o sistema se comporta hoje** do ponto de vista de consumidores.
   - Congele essa spec como **linha de base da migração** (contrato atual). Mudanças de contrato devem ser tratadas como projeto à parte.

2. **Migração incremental guiada pela spec**
   - Migre implementação "por trás" da mesma spec, reduzindo a necessidade de coordenar mudanças com todos os consumidores ao mesmo tempo.
   - Use o padrão **Strangler Fig**: novas rotas/fluxos implementados em stack nova, mantendo o contrato público estável.

3. **Compatibilidade verificada por contract tests**
   - Testes de contrato garantem que a nova implementação ainda cumpre a spec (status codes, payloads, erros, headers, etc.).
   - Sempre que possível, use **exemplos reais de tráfego** (logs, traces) para montar cenários de regressão.

4. **Qualidade e performance como parte da spec**
   - Registre objetivos de **latência, throughput, erros e consumo** como SLOs associados aos endpoints/eventos.
   - Trate esses objetivos como parte do contrato da migração: a nova stack não pode degradar esses números além de um limite acordado.

---

## Fluxo SDD aplicado à migração (Specify → Plan → Tasks → Implement)

### 1. Specify: definir o contrato e os objetivos da migração

- **Mapear a superfície pública**:
  - APIs HTTP/REST (OpenAPI), eventos (AsyncAPI), jobs, CLIs.
  - Se ainda não existe spec, use IA + ferramentas de inspeção de código/logs para **extrair uma spec inicial**.
- **Definir a spec canônica**:
  - Escolha uma spec como **verdade oficial** do contrato atual.
  - Documente **regras de compatibilidade** (o que pode ou não mudar) e deprecation policies.
- **Adicionar critérios de performance e qualidade**:
  - Latência p95/p99 aceitável, throughput esperado, error budget.
  - Requisitos não-funcionais importantes (logging, tracing, limites de payload, timeouts padrão, etc.).

### 2. Plan: planejar a migração em ondas

- **Quebrar o escopo por contexto**:
  - Agrupe endpoints/eventos por domínio/bounded context.
  - Planeje migrações módulo a módulo, não "big bang".
- **Definir estratégias por tipo de migração**:
  - Upgrade de libs: plano para substituir gradualmente bibliotecas, com feature flags/rollout controlado.
  - Migração de linguagem/framework: introduzir nova implementação **lado a lado**, atrás do mesmo contrato.
- **Planejar observabilidade e testes**:
  - Quais métricas serão usadas para aprovar cada etapa.
  - Que testes (unitários, integração, contrato, performance) precisam existir antes do rollout.

### 3. Tasks: detalhar o trabalho em tarefas orientadas à spec

- Criar tarefas específicas como:
  - "Extrair spec atual do serviço X" (ou consolidar specs dispersas).
  - "Adicionar exemplos de requests/responses críticos na spec".
  - "Criar suite de contract tests para endpoints Y/Z".
  - "Configurar testes de carga baseados na spec" (cenários principais).
  - "Habilitar SAST/SCA para as novas libs/language/runtime".
- Cada tarefa deve referenciar **seções da spec** e **critérios de aceite** ligados a contrato e SLOs.

### 4. Implement: migrar com segurança e medir resultados

- **Implementar a nova stack atrás da mesma spec**:
  - Adaptadores/anti-corruption layers para lidar com diferenças de modelo de dados interno.
  - Feature flags ou roteamento condicional para ativar a nova implementação para parte do tráfego.
- **Rodar a bateria de validação**:
  - Contract tests: nova implementação aderente à spec.
  - Testes funcionais/regressão.
  - Testes de performance comparando stack antiga vs nova.
- **Rollout progressivo**:
  - Canary releases, blue/green, gradiente de tráfego.
  - Monitorar métricas de latência, erros e consumo; rollback rápido em caso de regressão.

---

## Estratégias específicas por tipo de migração

### Migração de versões de bibliotecas

- **Mapeamento e análise**
  - Use ferramentas de **SCA (Software Composition Analysis)** para mapear dependências atuais.
  - Identifique libs críticas (segurança, IO, serialização, ORM, frameworks web).
- **Spec como contrato de comportamento**
  - Garante que trocas de implementação (ex.: nova versão de ORM) não vazem detalhes internos para a API.
- **Técnicas práticas**
  - Atualizar uma lib por vez (quando possível), validando contra a spec.
  - Criar testes focados em **cenários de fronteira** (erros, timeouts, grandes volumes de dados).
  - Usar **quality gates**: builds falham se testes de contrato ou de performance caírem abaixo do esperado.

### Migração de linguagens e runtimes

- Exemplos: Node.js, Python, Java, .NET, Go em novas versões.
- **Passos**
  - Garantir spec confiável para os serviços que serão migrados.
  - Criar uma implementação compatível na nova versão, mantendo o mesmo contrato.
  - Rodar a mesma suite de testes + contract tests em ambos os runtimes.
  - Usar ambientes paralelos (ex.: staging A = runtime antigo, staging B = runtime novo) com carga sintética ou espelhada.

### Migração de frameworks

- Exemplos: Spring Boot 2 → 3, Express → NestJS, AngularJS → React/Next.js.
- **Aplicando SDD**
  - Trate cada API/back-end/frontend como **fornecedor de contrato** definido em spec.
  - Reimplemente a camada de interface usando o novo framework, validando sempre contra a mesma spec.
  - Se a experiência do usuário mudar (ex.: front-end), documente novos fluxos na spec (ou em docs de UX/API que a complementem).

---

## Qualidade e performance durante a migração

1. **Definir metas de performance como critérios de aceite**
   - Exemplo: "latência p95 do endpoint /orders não pode piorar mais que 10%".
   - Registrar essas metas em documentação próxima à spec (ou anotada na spec, quando fizer sentido).

2. **Testes de performance orientados pela spec**
   - Gerar cenários de carga a partir dos endpoints/eventos descritos na spec.
   - Usar exemplos reais (ou quase reais) de requests para simular tráfego.

3. **Monitoramento comparativo (antes/depois)**
   - Coletar métricas da stack antiga **antes** de migrar (baseline).
   - Comparar com as métricas da stack nova em ambientes paralelos ou durante canary.

4. **Quality gates automáticos**
   - Pipeline de CI/CD falha se:
     - Contract tests não passarem.
     - Métricas de performance/regressão ficarem fora dos limites.
     - SAST/SCA apontarem novas vulnerabilidades críticas ou bibliotecas não aprovadas.

---

## Checklist prático de migração com SDD

### Antes de começar

- [ ] Mapear APIs/eventos públicos e gerar/atualizar as specs (OpenAPI/AsyncAPI/etc.).
- [ ] Definir a **spec canônica** que será o contrato de compatibilidade.
- [ ] Registrar requisitos de performance e confiabilidade (SLOs) relevantes.
- [ ] Garantir que existam **testes de contrato** mínimos para os fluxos críticos.
- [ ] Configurar SAST/SCA e outras ferramentas de AppSec para a stack atual.

### Durante a migração

- [ ] Migrar por contexto/módulo, não tudo de uma vez.
- [ ] Implementar a nova stack atrás da mesma spec (Strangler Fig / blue-green / canary).
- [ ] Rodar continuamente:
  - [ ] Contract tests
  - [ ] Testes funcionais/regressão
  - [ ] Testes de performance comparativos
- [ ] Monitorar métricas e preparar rollback rápido.

### Depois da migração

- [ ] Remover código morto/adapters que não forem mais necessários.
- [ ] Atualizar documentação para refletir a nova stack, mantendo a spec como SSOT.
- [ ] Revisar se os SLOs estão sendo cumpridos ou podem ser apertados.
- [ ] Registrar lições aprendidas para futuras migrações.

---

## Uso de IA em migrações orientadas por SDD

A IA pode ajudar bastante neste contexto, desde que **a spec continue sendo o trilho**:

- Gerar rascunhos de specs a partir de código legado ou logs.
- Sugerir planos de migração incremental a partir da spec e do inventário de dependências.
- Criar testes de contrato e casos de regressão baseados em exemplos reais.
- Ajudar em refactors pontuais para adaptar código à nova versão de libs/frameworks.

Veja também o documento [AI-Enhanced SDD](ai-enhanced-sdd.md) para ideias de prompts e fluxos.

---

## Integração com outros documentos deste repositório

- **[Conceitos Fundamentais](concepts.md)**: visão geral dos pilares do SDD.
- **[Workflow Spec Kit](spec-kit-workflow.md)**: fluxo Specify → Plan → Tasks → Implement, que pode ser aplicado diretamente a migrações.
- **[SDD e Segurança: Security-By-Spec](sdd-security.md)**: como levar requisitos de segurança para as specs.
- **[Ferramentas de AppSec no Fluxo SDD](appsec-tools.md)**: visão de SAST, SCA, DAST e outras ferramentas no contexto de SDD.
- **[Ferramentas SAST](sast-tools.md)**: detalhes de SAST e sua integração em pipelines.
- **[Biblioteca de Prompts](prompts-library.md)**: prompts que podem ser adaptados para cenários de migração.
