# Fluxo de Trabalho Spec-Driven Development (SDD)

Este guia descreve o ciclo de vida típico de desenvolvimento utilizando a metodologia SDD.

## Visão Geral do Processo

1.  **Definição (Design)**
2.  **Review e Contrato**
3.  **Desenvolvimento Paralelo**
4.  **Validação**
5.  **Publicação**

---

## 1. Definição (Design)

Tudo começa com a necessidade de negócio. Antes de escrever código, o desenvolvedor ou arquiteto desenha a interface da API.

*   **Ferramentas Sugeridas**: Editores de especificação (Swagger Editor, Stoplight, Postman) ou IDEs com extensões apropriadas.
*   **Ação**: Criar ou atualizar o arquivo de especificação (ex: `openapi.yaml`, `asyncapi.yaml`, `schema.graphql`).
*   **Foco**: Definir operações, parâmetros, códigos de status, esquemas de entrada/saída e exemplos.

## 2. Review e Contrato

A especificação proposta é submetida para revisão (Pull Request).

*   **Stakeholders**: Times de Implementação, Consumidores, Qualidade e Produto.
*   **Validação Automática**: CI/CD deve rodar linters (ex: Spectral) para garantir padrões de estilo e boas práticas na spec.
*   **Resultado**: Uma vez aprovado e mergeado, a especificação se torna o **Contrato Imutável** para aquela versão.

## 3. Desenvolvimento Paralelo

Com o contrato firmado, as equipes podem trabalhar de forma independente.

### Time de Implementação (Provedores)
*   Gera interfaces/stubs a partir da spec.
*   Implementa a lógica de negócio para satisfazer as interfaces geradas.
*   *Benefício*: Não precisa se preocupar com validação básica de tipos de entrada (geralmente tratada pelo framework/codegen).

### Time de Consumidores (Clientes)
*   Gera bibliotecas cliente (SDKs) a partir da spec atualizada.
*   Utiliza um **Mock Server** (ex: Prism, Microcks) gerado da spec para desenvolver integrações antes mesmo da implementação real estar pronta.
*   *Benefício*: Integração imediata e tipada.

## 4. Validação (Testing)

Como garantir que a implementação segue a spec?

*   **Contract Testing**: Testes automatizados que verificam se as respostas da implementação real batem com os esquemas definidos na spec (ex: Dredd, Schemathesis, Pact).
*   **Validação em Tempo de Execução**: Middlewares podem interceptar requisições/respostas e validar contra a especificação, logando avisos ou bloqueando tráfego inválido.

## 5. Publicação

Ao finalizar o desenvolvimento:

*   A documentação é atualizada automaticamente no portal de desenvolvedores.
*   Novas versões dos SDKs são publicadas nos gerenciadores de pacotes apropriados.

---

## Exemplo de Pipeline SDD

1.  `git checkout -b feature/nova-rota`
2.  Edita `openapi.yaml` adicionando `GET /users`.
3.  `git commit` e abre Pull Request.
4.  CI roda `spectral lint openapi.yaml`.
5.  Time aprova PR. Merge na `main`.
6.  CI dispara:
    *   Deploy da documentação atualizada.
    *   Publicação dos artefatos gerados (SDKs, tipos, etc.) em versão alpha/beta.
7.  Time de Implementação desenvolve a funcionalidade.
8.  Time de Consumidores atualiza versão do cliente e integra a nova funcionalidade.
