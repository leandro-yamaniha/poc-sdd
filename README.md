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

## 📚 Conteúdo

A documentação detalhada pode ser encontrada na pasta `docs/`:

### Fundamentos
- **[Conceitos Fundamentais](docs/concepts.md)**: Pilares do SDD (Design-First, SSOT, Codegen)
- **[Fluxo de Trabalho](docs/workflow.md)**: Ciclo de vida completo do desenvolvimento

### IA e Automação
- **[AI-Enhanced SDD](docs/ai-enhanced-sdd.md)**: Como usar IA em cada fase do SDD
- **[Workflow Spec Kit](docs/spec-kit-workflow.md)**: Processo estruturado em 4 fases (Specify → Plan → Tasks → Implement)
- **[RAG, MCP e Agents](docs/rag-mcp-agents.md)**: Técnicas avançadas de IA para potencializar SDD
- **[Integração com VS Code](docs/vscode-integration.md)**: Como conectar RAG, MCP e Agents ao VS Code
- **[GitHub Copilot: Performance e Boas Práticas](docs/copilot-performance-best-practices.md)**: Otimização, limitações e custom instructions
- **[As 15 Regras do Vibe Coding](docs/15-vibe-coding-rules.md)**: Regras implícitas do vibe coding e por que estão erradas
- **[Armadilhas do Vibe Coding](docs/vibe-coding-pitfalls.md)**: Consequências práticas e como SDD resolve
- **[Biblioteca de Prompts](docs/prompts-library.md)**: Prompts testados para geração, validação e testes
- **[Casos de Uso](docs/use-cases.md)**: Exemplos práticos de SDD + IA em cenários reais

### Segurança e Qualidade
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
