# Estratégia de Adoção de Spec-Driven Development (SDD)

## Objetivo

Este guia apresenta uma **estratégia prática de adoção de SDD** em times/projetos reais, respondendo à dúvida:

> "É melhor começar com SDD já com todos os detalhes completos do projeto, ou ir adotando gradualmente?"

A abordagem recomendada aqui é:

- **Um núcleo bem especificado desde o início**,
- Com **expansão gradual** para o restante do sistema.

---

## Princípios de adoção

1. **Comece pequeno, mas comece sério**
   - Não tente modelar o sistema inteiro em detalhe na v1 da spec.
   - Mas também não trate a spec como documentação superficial.
   - Escolha um escopo inicial **importante e representativo** e especifique-o bem.

2. **Spec como contrato vivo, não documento morto**
   - A spec deve ser usada ativamente por:
     - Código (implementações).
     - Testes (especialmente contract tests).
     - Ferramentas de IA.
     - Consumidores internos/externos.

3. **Evolução iterativa guiada por feedback real**
   - Use uso real (ou testes bem desenhados) para melhorar a spec.
   - Evite tentar prever todos os detalhes de futuro distante na v1.

4. **Governança leve, mas explícita**
   - Defina quem aprova mudanças de spec.
   - Defina como tratar breaking changes (versionamento, deprecation etc.).

---

## Passo 1: Escolher o núcleo inicial

Critérios para escolher por onde começar:

- [ ] **Domínio crítico** do negócio (ex.: billing, orders, auth).
- [ ] **Superfície bem delimitada** (ex.: 3–10 endpoints principais, alguns eventos).
- [ ] **Impacto claro**: algo que se beneficiar logo de SDD (migração, dívida técnica, múltiplos consumidores).

Defina para esse núcleo:

- [ ] Uma spec inicial em `specs/` (OpenAPI/AsyncAPI/GraphQL etc.).
- [ ] Regras mínimas de segurança (authN/authZ, dados sensíveis, rate limiting).
- [ ] Objetivos não funcionais básicos (latência aceitável, disponibilidade esperada). 

---

## Passo 2: Fortalecer o núcleo com SDD + IA

Para esse núcleo inicial, aplicar de forma mais completa possível:

- [ ] **Fluxo SDD completo** (Specify → Plan → Tasks → Implement), conforme `docs/workflow.md`.
- [ ] **Uso de IA alinhado à spec**, conforme `docs/ai-enhanced-sdd.md` e `docs/ides-sdd-overview.md`.
- [ ] **Contract tests** a partir da spec.
- [ ] **Pipelines de CI/CD** com quality gates (lint, testes, SAST/SCA etc.).
- [ ] **Documentação** conectada (ex.: `docs/use-cases.md`, exemplos de consumo).

A ideia é tornar esse núcleo um **case interno de SDD bem-feito**, que sirva de referência para o restante do sistema.

---

## Passo 3: Expandir por contexto, não por tecnologia

Ao expandir SDD para outras partes do sistema, priorize:

- [ ] **Bounded contexts/domínios** (ex.: shipping, catálogo, notificações), e não apenas "todos os serviços Node" ou "todos os serviços Java".
- [ ] Serviços que:
  - [ ] Tenham muitas integrações/consumidores.
  - [ ] Estejam em migração de stack (ver `docs/sdd-migration-guide.md`).
  - [ ] Tenham histórico de incidentes por falta de contrato claro.

Para cada novo contexto:

- [ ] Criar/ajustar spec em `specs/`.
- [ ] Ligar o contexto às ferramentas de IA e KS existentes (`ks/`).
- [ ] Repetir o reforço de testes, CI/CD e AppSec.

---

## Passo 4: Aproveitar migrações para reforçar SDD

Toda migração de:

- Versão de **biblioteca**,
- **Framework** ou **runtime**,
- Arquitetura (ex.: monólito → microsserviços),

é uma oportunidade de:

- [ ] **Congelar a spec atual** como contrato de compatibilidade.
- [ ] Usar `docs/sdd-migration-guide.md` para planejar uma migração incremental.
- [ ] Introduzir SDD em volta daquele serviço/módulo.

Checklist de migração alinhada a SDD:

- [ ] Existe uma spec confiável do comportamento atual?
- [ ] Existem contract tests cobrindo fluxos críticos?
- [ ] Há plano de rollout progressivo (canary, blue-green etc.)?
- [ ] Métricas de performance e erro estão definidas e sendo monitoradas?

---

## Passo 5: Levar segurança e qualidade junto desde cedo

SDD funciona melhor quando **segurança e qualidade** entram desde o núcleo:

- [ ] Aplicar "Security-By-Spec" (`docs/sdd-security.md`) na spec inicial.
- [ ] Integrar ferramentas de AppSec (`docs/appsec-tools.md`, `docs/sast-tools.md`).
- [ ] Definir padrões de logging/tracing/métricas (ver `ks/observability/`).

Ao expandir SDD para outros módulos, reaproveitar:

- Templates de serviços e testes em `ks/services/`.
- Pipelines e quality gates em `ks/cicd/`.
- Checklists de segurança e threat modeling.

---

## Passo 6: Tornar o "núcleo SDD" o novo padrão da casa

Com o núcleo rodando bem:

- [ ] Usar esse exemplo em **onboarding** de novos devs.
- [ ] Apontar para ele em reviews e arch discussions como "padrão desejado".
- [ ] Evoluir `ks/` e docs com base no que funcionou na prática.

A partir daí, a pergunta deixa de ser "se" vamos usar SDD, e passa a ser **"por que este módulo ainda não está alinhado ao nosso fluxo SDD?"**.

---

## Quando (talvez) faz sentido tentar especificar muito desde o início

Existem cenários onde um esforço maior de spec antecipada pode ser justificado:

- [ ] Interfaces com **terceiros** onde o contrato será assinado formalmente.
- [ ] Domínios com forte **regulação** (PCI, HIPAA, LGPD com exigências contratuais específicas etc.).
- [ ] Projetos onde o custo de mudar o contrato depois é extremamente alto.

Mesmo nesses casos, recomenda-se:

- [ ] Rodar provas de conceito ou pilotos com parte da spec.
- [ ] Validar a spec com exemplos reais, consumidores e testes de contrato.
- [ ] Ajustar antes de "congelar" o contrato.

---

## Resumo rápido

- Não vale a pena tentar começar SDD com **todo o projeto detalhado** em nível máximo.
- É melhor:
  - **Escolher um núcleo crítico**,
  - **Caprichar muito** na spec + testes + CI + AppSec + IA ao redor dele,
  - E então **expandir por contexto**, aproveitando migrações e oportunidades de refactor.

Use este guia em conjunto com:

- `docs/concepts.md` e `docs/workflow.md` (conceitos e fluxo SDD)
- `docs/ai-enhanced-sdd.md` e `docs/ides-sdd-overview.md` (uso de IA)
- `docs/sdd-migration-guide.md` (migrações orientadas a specs)
- `docs/appsec-tools.md` e `docs/sdd-security.md` (segurança e qualidade)
- `docs/ks-repo-structure.md` (como organizar KS e templates)
