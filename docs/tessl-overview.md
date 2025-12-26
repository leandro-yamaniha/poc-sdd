# Tessl: Plataforma AI-Native orientada a Especificações

## Visão Geral

**Tessl** é uma plataforma de desenvolvimento **AI‑native** baseada em **Spec‑Driven Development**.

A ideia central:

> Em vez de definir software principalmente pela implementação, você o define por **especificações claras**, e usa IA para manter código e sistemas alinhados a essas specs.

Tessl busca tornar possível:

- Construir sistemas complexos com apoio pesado de IA.
- Sem abrir mão de **confiabilidade, rastreabilidade e segurança**.
- Usando specs, testes e *registries* como âncoras.

---

## Conceitos Centrais do Tessl

Os textos públicos sobre Tessl destacam alguns pilares:

### 1. Especificações como fonte de verdade

- A spec descreve:
  - Componentes da aplicação.
  - Contratos de APIs/eventos.
  - Regras de negócio e restrições.
- O código gerado ou modificado pela IA deve **passar nos mesmos testes** que o sistema existente.

### 2. Framework “spec-driven” para IA

- Tessl fornece uma estrutura para:
  - Representar a aplicação em termos de specs.
  - Orientar agentes de IA a trabalhar **sempre referenciando essas specs**.
  - Evitar que o modelo “saia do trilho” ao fazer refactors ou novas features.

### 3. Registry / Catálogo de Especificações

- A plataforma tende a incluir a ideia de um **registry** (catálogo) de especificações:
  - Armazena specs versionadas de aplicações e componentes.
  - Permite reuso e governança (quem mudou o quê, quando e por quê).
  - Ajuda a IA a encontrar o contexto correto para cada modificação.

### 4. Confiabilidade e Testes

- Um dos objetivos explícitos do Tessl é enfrentar o problema de:
  - IA que “parece funcionar” mas quebra invariantes sutis.
- Para isso, a plataforma foca em:
  - **Testes automatizados** ligados à spec.
  - Ferramentas para garantir que o código gerado:
    - Respeita contratos.
    - Passa em suites de teste.
    - Não introduz regressões silenciosas.

---

## Tessl e Spec-Driven Development

No contexto de SDD:

- Tessl aposta que o futuro do desenvolvimento é:
  - **“Software definido por especificações”**  
    (e não apenas por código imperativo/ad hoc).
- Ele propõe um framework onde:
  - A IA atua *sobre* essas specs.
  - As specs são **centrais** na evolução e manutenção autônoma de sistemas.

Conexão com seus docs:

- Seções como:
  - `docs/concepts.md` (Design-First, SSOT, Codegen)
  - `docs/sdd-security.md` (Security-By-Spec)
  - `docs/spec-kit-workflow.md` (fluxo em 4 fases)
- Estão totalmente alinhadas com a forma como Tessl enxerga:
  - **Intenção → Spec → Testes → Implementação assistida por IA**.

---

## Comparação: Tessl, Spec Kit e SDD “Genérico”

| Aspecto                   | Tessl                                   | Spec Kit (GitHub)                           | SDD Genérico (Docs deste repo)                 |
|---------------------------|-----------------------------------------|---------------------------------------------|-----------------------------------------------|
| Tipo de ferramenta        | Plataforma AI-native / framework        | Toolkit/CLI + prompts                       | Metodologia/abordagem                         |
| Foco                      | Confiabilidade de sistemas AI-driven    | Workflow SDD com IA para features/código    | Princípios de spec-first + automação          |
| Papel da spec             | Modelo central da aplicação             | Fonte de verdade para features/planos       | SSOT genérica (APIs, eventos, contratos)      |
| Ênfase                    | Testes, registro e manutenção contínua  | Fluxo Specify → Plan → Tasks → Implement    | Design‑First, SSOT, Codegen, governança       |

---

## Quando vale estudar/usar Tessl

- **Sistemas complexos “AI-native”**  
  Onde muito do código é gerado/alterado por IA e você precisa de:
  - Alta confiabilidade.
  - Registro sólido de specs e testes.

- **Plataformas que evoluem continuamente**  
  Quando você quer que agentes de IA façam:
  - Manutenções recorrentes.
  - Refactors guiados por especificações e testes.

- **Governança forte sobre aplicações AI-driven**  
  Uso corporativo onde:
  - Compliance, auditoria e previsibilidade são críticos.
  - Não basta “aceitar o que a IA gerou”.

---

## Relação com seu repositório SDD PoC

Você pode enxergar Tessl como:

- Um **exemplo concreto** de plataforma que tenta implementar:
  - O que você descreve em `SDD + IA`.
  - Os princípios de `Security-By-Spec`, SAST, contract testing, etc.
- Uma **fonte de inspiração** para:
  - Como organizar um “registry” de specs.
  - Como acoplar testes e governança diretamente às especificações.
  - Como pensar em SDD não só em nível de API, mas de **aplicação inteira**.

---

## Limitações / Considerações

- Produto ainda em evolução; detalhes concretos podem mudar rápido.
- Pode ser mais adequado para times com:
  - Organização pronta para **apostar forte em IA**.
  - Maturidade em testes, observabilidade e DevSecOps.

## Recursos Adicionais

- **Site oficial** – [Tessl - AI Native Development Platform](https://tessl.io/)
- **Artigo** – [Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- **Notícia** – [Tessl launches spec-driven development tools for reliable AI coding](https://ainativedev.io/news/tessl-launches-spec-driven-framework-and-registry)
- **Análise** – [How Tessl is re-imagining AI-driven development - and why](https://diginomica.com/how-tessl-re-imagining-ai-driven-development-and-why)
- **Contexto geral de SDD e IA** – [AI Week: Is Spec-Driven Development the Future of AI Coding?](https://zuplo.com/blog/spec-driven-ai-development)

---
