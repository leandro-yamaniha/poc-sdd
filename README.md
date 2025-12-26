# Spec-Driven Development (SDD) - PoC

Este repositório serve como uma Prova de Conceito (PoC) e documentação para a metodologia **Spec-Driven Development (SDD)**.

## O que é Spec-Driven Development?

Spec-Driven Development (Desenvolvimento Orientado a Especificações) é uma abordagem de desenvolvimento de software onde a **especificação** (API specs, esquemas de dados, contratos de interface) atua como a fonte única da verdade.

Ao invés de escrever código e depois documentá-lo, ou escrever testes e depois o código (TDD), no SDD você define os contratos de interface primeiro. Isso permite:

- **Desenvolvimento Paralelo**: Times de implementação e consumidores podem trabalhar simultaneamente baseados no mesmo contrato.
- **Validação Automática**: O código pode ser validado contra a especificação.
- **Geração de Código**: Bibliotecas cliente, interfaces de servidor e tipos podem ser gerados automaticamente a partir da spec.
- **Melhor Comunicação**: A especificação serve como um contrato claro entre times e sistemas.

## 🤖 SDD + Inteligência Artificial

Esta metodologia ganha poder exponencial quando combinada com IA. Ferramentas como GPT-4, Claude e GitHub Copilot podem:

- **Gerar especificações** a partir de requisitos em linguagem natural
- **Revisar e validar** specs automaticamente
- **Gerar código** (implementações, clientes, testes) a partir da spec
- **Criar documentação** e exemplos automaticamente

Veja o guia completo em [AI-Enhanced SDD](docs/ai-enhanced-sdd.md).

### Por que SDD na era da IA generativa?

Modelos de IA generativa conseguem produzir código, testes e documentação em grande velocidade, mas nem sempre de forma consistente com a arquitetura, os contratos existentes ou os padrões de qualidade do time. Conforme a quantidade de código gerado por IA aumenta, também aumenta o risco de divergência entre:

- Especificações de APIs e eventos
- Implementações de serviços
- Documentação e contratos de consumo

O SDD responde a esse cenário ao colocar a **spec como fonte única da verdade (SSOT)**. Em vez de deixar que cada interação com a IA crie “a sua própria versão da verdade”, o fluxo de trabalho é guiado pelos contratos:

- A spec define o **modelo de dados, endpoints, mensagens e regras**.
- O código (humano ou gerado por IA) deve **seguir e validar** contra essa spec.
- Mudanças começam pela spec, não por refactors ad hoc no código.

Na era da IA generativa, o SDD funciona como um **trilho**: permite aproveitar a velocidade da IA, sem abrir mão de previsibilidade e coerência arquitetural.

### Relação entre SDD e IA generativa

SDD e IA generativa se complementam de forma natural:

- **Na concepção da spec**: a IA pode ajudar a rascunhar contratos iniciais a partir de requisitos em linguagem natural, casos de uso ou specs existentes.
- **Na evolução da spec**: a IA pode sugerir ajustes, detectar inconsistências e apontar impactos de mudanças em consumidores e provedores.
- **Na geração de artefatos**: a partir de uma spec forte, a IA auxilia a gerar implementações, testes de contrato, clientes e exemplos de uso.
- **Na compreensão de sistemas legados**: combinando SDD com IA, é possível extrair specs de bases legadas e usá-las como novo ponto de verdade.

O ponto central é que **a IA trabalha a serviço da spec**, não o contrário. Quando há conflito entre o que a IA sugere e o que a spec define, a spec vence – e o fluxo de SDD orienta como revisar e alinhar tudo de forma controlada.

### Riscos, limitações e como SDD ajuda a mitigá-los

O uso de IA generativa traz riscos conhecidos para desenvolvimento de software, como:

- **Alucinações**: código ou contratos inventados que não existem no sistema real.
- **Introdução de vulnerabilidades**: dependências inseguras, padrões de código frágeis ou exposição indevida de dados.
- **Inconsistência entre serviços**: cada time usando a IA de forma diferente, gerando contratos incompatíveis entre si.
- **Perda de rastreabilidade**: dificuldade em entender por que certos contratos ou estruturas de dados existem.

O SDD ajuda a mitigar esses riscos ao exigir:

- **Contratos explícitos e versionados**: specs revisadas, versionadas e sujeitas a code review, assim como o código.
- **Validação automatizada**: linters, validadores e testes de contrato que checam se o código (inclusive o gerado por IA) está aderente à spec.
- **Governança de mudanças**: qualquer alteração relevante começa pela spec, com visibilidade clara de impactos.
- **Segurança orientada a contrato**: políticas de segurança e SAST podem ser aplicadas tanto às specs quanto ao código que as implementa.

Em resumo, SDD não impede o uso de IA – ele **torna o uso de IA mais seguro, previsível e alinhado à arquitetura**, reduzindo a chance de o "assistente entusiasmado, mas pouco confiável" sair dos trilhos.

## Boas práticas clássicas e como o SDD as potencializa

SDD não substitui as boas práticas de engenharia que já existiam antes – ele as **organiza em torno da especificação** como fonte única da verdade e cria um trilho claro para conectá-las.

- **SDD como evolução, não ruptura**
  - Requisitos bem escritos continuam essenciais, mas agora se materializam como **specs formais** (OpenAPI/AsyncAPI, contratos de eventos, schemas).
  - Práticas como **API-First / Design-First**, TDD/BDD, code review e CI/CD seguem válidas; o SDD ajuda a amarrá-las à spec.

- **Práticas clássicas que o SDD reforça**
  - **API-First / Design-First**: o design de APIs antes do código passa a ser a regra, não a exceção.
  - **TDD/BDD e contract testing**: cenários e contratos ganham um espelho direto na spec, reduzindo gaps entre testes e implementação.
  - **Segurança (OWASP, ASVS, threat modeling)**: requisitos de segurança são traduzidos em **checklists de spec** e validações automatizadas.
  - **CI/CD e automação**: pipelines passam a validar código e specs (lint, testes de contrato, SAST, SCA, quality gates) de forma integrada.

- **Como encaixar SDD em times que já seguem boas práticas**
  - Se o time já faz **API-First**, o próximo passo é versionar specs, revisá-las como código e plugar CI/CD em cima delas.
  - Se o time já usa **TDD/BDD**, é alinhar casos de teste e contract tests aos cenários explícitos na spec.
  - Se já existe foco em **segurança**, o movimento é levar políticas e requisitos de segurança para dentro das specs e dos checklists de revisão.

- **O que SDD não resolve sozinho**
  - Não substitui comunicação entre times, entendimento de domínio (DDD) ou boa governança de mudanças.
  - Sem disciplina mínima (versionamento, revisão, testes, observabilidade), SDD e IA tendem a amplificar problemas em vez de resolvê-los.

## 📚 Conteúdo

A documentação detalhada pode ser encontrada na pasta `docs/`:

### Fundamentos
- **[Conceitos Fundamentais](docs/concepts.md)**: Pilares do SDD (Design-First, SSOT, Codegen)
- **[Fluxo de Trabalho](docs/workflow.md)**: Ciclo de vida completo do desenvolvimento
 - **[Guia de Migração Orientada a SDD](docs/sdd-migration-guide.md)**: Como usar SDD em migrações de libs, linguagens e frameworks com foco em performance e qualidade
 - **[Estratégia de Adoção de SDD](docs/sdd-adoption-strategy.md)**: Playbook de adoção incremental (núcleo bem especificado + expansão gradual)

### IA e Automação
- **[AI-Enhanced SDD](docs/ai-enhanced-sdd.md)**: Como usar IA em cada fase do SDD
- **[SDD + IA em IDEs](docs/ides-sdd-overview.md)**: Princípios gerais para usar SDD com Windsurf, Cursor, Copilot e outras ferramentas
- **[Spec Kit: Visão Geral](docs/spec-kit-overview.md)**: Introdução ao toolkit de Spec-Driven Development com IA
- **[Workflow Spec Kit](docs/spec-kit-workflow.md)**: Processo estruturado em 4 fases (Specify → Plan → Tasks → Implement)
- **[RAG, MCP e Agents](docs/rag-mcp-agents.md)**: Técnicas avançadas de IA para potencializar SDD
- **[Integração com VS Code](docs/vscode-integration.md)**: Como conectar RAG, MCP e Agents ao VS Code
- **[GitHub Copilot: Performance e Boas Práticas](docs/copilot-performance-best-practices.md)**: Otimização, limitações e custom instructions
- **[Guia: SDD com Windsurf](docs/windsurf-sdd-guide.md)**: Como configurar e usar Windsurf em um fluxo SDD
- **[Guia: SDD com Cursor](docs/cursor-sdd-guide.md)**: Como usar Cursor (Chat/Edit) respeitando specs e KS
- **[Guia: SDD com GitHub Copilot](docs/github-copilot-sdd-guide.md)**: Como alinhar o Copilot à spec e aos docs
 - **[Guia: GitHub Copilot no GitHub.com](docs/github-copilot-web-guide.md)**: Uso do Copilot na interface web (PRs, arquivos, Code Scanning) em um fluxo SDD
- **[As 15 Regras do Vibe Coding](docs/15-vibe-coding-rules.md)**: Regras implícitas do vibe coding e por que estão erradas
- **[Armadilhas do Vibe Coding](docs/vibe-coding-pitfalls.md)**: Consequências práticas e como SDD resolve
- **[Biblioteca de Prompts](docs/prompts-library.md)**: Prompts testados para geração, validação e testes
- **[Casos de Uso](docs/use-cases.md)**: Exemplos práticos de SDD + IA em cenários reais

### Segurança e Qualidade
- **[SDD e Segurança: Security-By-Spec](docs/sdd-security.md)**: Princípios, checklist de specs e integração com SAST
- **[Riscos de IA e como SDD ajuda a mitigá-los](docs/ai-risks-and-sdd-mitigation.md)**: Acesso a dados, deleção, malware e estratégias de mitigação
- **[Ferramentas SAST](docs/sast-tools.md)**: Static Application Security Testing para specs e código
- **[Padronização de Código](docs/code-standardization-tools.md)**: Linters, formatters e ferramentas de qualidade
- **[Docker: Segurança e Performance](docs/docker-security-performance.md)**: Otimização de imagens, scanning e boas práticas
- **[Guia de Otimização Docker](docs/docker-optimization-guide.md)**: Ferramentas práticas, layers, cache e workflows

### Exemplos
- **[specs/openapi.yaml](specs/openapi.yaml)**: Exemplo de especificação OpenAPI 3.0

## 🏗️ Estrutura do Projeto

```
/
├── docs/                      # Documentação detalhada
│   ├── concepts.md           # Conceitos fundamentais do SDD
│   ├── workflow.md           # Fluxo de trabalho passo a passo
│   ├── ai-enhanced-sdd.md    # SDD otimizado com IA
│   ├── spec-kit-overview.md  # Visão geral do Spec Kit
│   ├── spec-kit-workflow.md  # Workflow Spec Kit (4 fases)
│   ├── rag-mcp-agents.md     # RAG, MCP e Agents
│   ├── vscode-integration.md # Integração VS Code
│   ├── copilot-performance-best-practices.md # Performance GitHub Copilot
│   ├── 15-vibe-coding-rules.md # As 15 regras do vibe coding
│   ├── vibe-coding-pitfalls.md # Armadilhas do vibe coding
│   ├── sast-tools.md         # Ferramentas SAST
│   ├── code-standardization-tools.md # Padronização de código
│   ├── docker-security-performance.md # Docker: Segurança e Performance
│   ├── docker-optimization-guide.md # Guia de Otimização Docker
│   ├── prompts-library.md    # Biblioteca de prompts
│   └── use-cases.md          # Casos de uso práticos
├── specs/                     # Especificações de exemplo
│   └── openapi.yaml          # API de exemplo (User API)
└── README.md                  # Este arquivo
```

## 🚀 Quick Start

1. **Explore a documentação**: Comece por [Conceitos Fundamentais](docs/concepts.md)
2. **Veja a spec de exemplo**: Analise [specs/openapi.yaml](specs/openapi.yaml)
3. **Use os prompts**: Aplique os prompts da [biblioteca](docs/prompts-library.md) no seu projeto
4. **Experimente com IA**: Siga o guia [AI-Enhanced SDD](docs/ai-enhanced-sdd.md)

## 🛠️ Ferramentas Recomendadas

### Editores de Spec
- **Swagger Editor**: Editor online para OpenAPI
- **Stoplight Studio**: IDE visual para APIs
- **VS Code** + extensões OpenAPI

### Validação e Linting
- **Spectral**: Linter de specs OpenAPI/AsyncAPI
- **openapi-validator**: Validação de specs

### Mock Servers
- **Prism**: Mock server da Stoplight
- **Microcks**: Mocking e testing

### Geração de Código
- **openapi-generator**: Gera clientes e servidores em múltiplas linguagens
- Geradores específicos por linguagem/framework (consulte documentação da sua stack)

### IA
- **GPT-4 / Claude**: Para geração e análise de specs
- **GitHub Copilot / Cursor**: Para desenvolvimento assistido
- **Windsurf**: IDE com suporte nativo a OpenAPI
- **[Spec Kit](https://github.com/github/spec-kit)**: Toolkit open source do GitHub para SDD com IA (workflow: Specify → Plan → Tasks → Implement)

---

## 📖 Referências e Recursos

### Spec-Driven Development (SDD)

- **[Spec-Driven Development with AI](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)**: GitHub Blog sobre SDD com IA e toolkit open source
- **[Contract-First Development](https://www.infoq.com/articles/contract-first-development/)**: InfoQ sobre desenvolvimento orientado a contratos
- **[Schema-First API Design](https://blog.logrocket.com/schema-first-api-design/)**: LogRocket sobre design schema-first
- **[API-First Development](https://swagger.io/resources/articles/adopting-an-api-first-approach/)**: Swagger sobre abordagem API-First
- **[Design-First API Development](https://apisyouwonthate.com/blog/api-design-first-vs-code-first)**: APIs You Won't Hate - comparação detalhada
- **[Contract-Driven Development](https://martinfowler.com/bliki/ContractTest.html)**: Martin Fowler sobre testes de contrato
- **[Specification by Example](https://gojko.net/books/specification-by-example/)**: Livro de Gojko Adzic sobre especificações executáveis
 - **[Kiro: IDE Agentic para SDD](docs/kiro-overview.md)**: Visão geral do Kiro e relação com Spec-Driven Development
 - **[Tessl: Plataforma AI-Native orientada a Especificações](docs/tessl-overview.md)**: Visão geral do Tessl e sua abordagem spec-driven

### Especificações e Padrões

#### OpenAPI
- **[OpenAPI Specification](https://spec.openapis.org/oas/latest.html)**: Especificação oficial OpenAPI 3.1
- **[OpenAPI Guide](https://oai.github.io/Documentation/)**: Guia oficial de documentação
- **[Swagger.io](https://swagger.io/resources/open-api/)**: Recursos e tutoriais sobre OpenAPI

#### AsyncAPI
- **[AsyncAPI Specification](https://www.asyncapi.com/docs/reference/specification/latest)**: Especificação para APIs assíncronas e orientadas a eventos
- **[AsyncAPI Guide](https://www.asyncapi.com/docs/guides)**: Guias práticos

#### GraphQL
- **[GraphQL Specification](https://spec.graphql.org/)**: Especificação oficial GraphQL
- **[GraphQL Best Practices](https://graphql.org/learn/best-practices/)**: Melhores práticas

### API Design-First

- **[API Design-First vs Code-First](https://swagger.io/blog/api-design/design-first-or-code-first-api-development/)**: Comparação de abordagens
- **[The Design-First Approach to API Development](https://www.postman.com/api-platform/api-design/)**: Artigo da Postman
- **[API Design Patterns](https://microservice-api-patterns.org/)**: Padrões de design de APIs
- **[REST API Design Best Practices](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)**: Melhores práticas REST

### Contract Testing

- **[Pact](https://docs.pact.io/)**: Framework de contract testing para microsserviços
- **[Spring Cloud Contract](https://spring.io/projects/spring-cloud-contract)**: Contract testing para Spring
- **[Consumer-Driven Contracts](https://martinfowler.com/articles/consumerDrivenContracts.html)**: Artigo de Martin Fowler
- **[Contract Testing vs Integration Testing](https://pactflow.io/blog/contract-testing-vs-integration-testing/)**: Diferenças e quando usar

### Ferramentas e Ecossistema

#### Validação e Linting
- **[Spectral Documentation](https://stoplight.io/open-source/spectral)**: Linter OpenAPI/AsyncAPI
- **[Redocly CLI](https://redocly.com/docs/cli/)**: Ferramentas de linha de comando para OpenAPI

#### Mock Servers
- **[Prism Documentation](https://stoplight.io/open-source/prism)**: Mock server baseado em OpenAPI
- **[Microcks](https://microcks.io/)**: Mocking e testing de APIs e microsserviços
- **[WireMock](https://wiremock.org/)**: Simulador de APIs HTTP

#### Geração de Código
- **[OpenAPI Generator](https://openapi-generator.tech/)**: Gerador de código para múltiplas linguagens
- **[Swagger Codegen](https://swagger.io/tools/swagger-codegen/)**: Gerador oficial Swagger
- **[openapi-typescript](https://github.com/drwpow/openapi-typescript)**: Gerador de tipos TypeScript

### Artigos e Livros

- **[API Design Patterns](https://www.manning.com/books/api-design-patterns)**: Livro de JJ Geewax (Google)
- **[Designing Web APIs](https://www.oreilly.com/library/view/designing-web-apis/9781492026914/)**: Livro da O'Reilly
- **[Building Microservices](https://www.oreilly.com/library/view/building-microservices-2nd/9781492034018/)**: Sam Newman - Capítulos sobre contratos de API
- **[API Stylebook](http://apistylebook.com/)**: Coleção de guias de estilo de APIs

### Comunidades e Blogs

- **[API Evangelist](https://apievangelist.com/)**: Blog sobre APIs e estratégias
- **[Nordic APIs](https://nordicapis.com/)**: Artigos e conferências sobre APIs
- **[Postman Blog](https://blog.postman.com/)**: Artigos sobre desenvolvimento de APIs
- **[Stoplight Blog](https://stoplight.io/blog)**: Conteúdo sobre API Design-First

### Vídeos e Cursos

- **[OpenAPI 3.0 Tutorial](https://www.youtube.com/watch?v=6kwmW_p_Tig)**: Tutorial completo no YouTube
- **[API Design Best Practices](https://www.youtube.com/watch?v=_YlYuNMTCc8)**: Palestra Google Cloud
- **[Contract Testing with Pact](https://www.youtube.com/watch?v=h-79QmIV824)**: Workshop prático

### Padrões de Arquitetura

- **[Microservices Patterns](https://microservices.io/patterns/index.html)**: Chris Richardson
- **[API Gateway Pattern](https://microservices.io/patterns/apigateway.html)**: Padrão de gateway
- **[Backend for Frontend (BFF)](https://samnewman.io/patterns/architectural/bff/)**: Padrão BFF

### IA e Automação

- **[GitHub Copilot Documentation](https://docs.github.com/en/copilot)**: Documentação oficial
- **[OpenAI API Documentation](https://platform.openai.com/docs)**: Para integração com GPT
- **[Anthropic Claude](https://www.anthropic.com/claude)**: Documentação Claude
- **[AI-Assisted Development](https://github.blog/2023-06-20-how-to-write-better-prompts-for-github-copilot/)**: Melhores práticas

### Estudos de Caso

- **[How Stripe Designs APIs](https://stripe.com/blog/payment-api-design)**: Case Stripe
- **[Netflix API Architecture](https://netflixtechblog.com/embracing-the-differences-inside-the-netflix-api-redesign-15fd8b3dc49d)**: Case Netflix
- **[Spotify API Design](https://engineering.atspotify.com/2015/03/how-we-build-code-at-spotify/)**: Case Spotify

## Licença

Este projeto está licenciado sob a **MIT License**. Consulte o arquivo [`LICENSE`](LICENSE) na raiz do repositório para mais detalhes.
