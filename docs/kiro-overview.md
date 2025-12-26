# Kiro: IDE Agentic para Spec-Driven Development

## Visão Geral

**Kiro** é uma experiência de desenvolvimento *agentic* criada pela AWS, pensada para trabalhar com **Spec‑Driven Development (SDD)**.

Em vez de pedir para a IA “escrever código” direto, o Kiro conduz você por um fluxo estruturado de especificação e planejamento, no qual:

- A **intenção** do que você quer construir é explicitada.
- Essa intenção vira uma **especificação e um design**.
- O trabalho é decomposto em **tarefas pequenas** que agentes de IA podem implementar.

Ele faz parte da mesma família de ideias que o **Spec Kit** (GitHub) e o **Tessl**, todos citados como implementações de Spec‑Driven Development.

---

## Fluxo Kiro em 3 Fases

Embora detalhes possam variar, o fluxo conceitual que aparece nos artigos é aproximadamente:

1. **Requirements (Requisitos)**  
   Você descreve **o que** quer construir e **por que**.  
   Foco em objetivos de negócio, usuários, jornadas, restrições.  
   Saída: um artefato textual/estruturado de requisitos.

2. **Design (Desenho)**  
   A partir dos requisitos, o Kiro (via IA) ajuda a criar um **design mais detalhado**:
   - Componentes principais.
   - Interfaces/contratos.
   - Fluxos de dados.
   
   Saída: uma espécie de *spec de design* que ancora o resto do trabalho.

3. **Tasks (Tarefas)**  
   O sistema decompõe design + requisitos em **tarefas implementáveis**:
   - Cada tarefa é pequena, clara, revisável.
   - Serve como “briefing” para agentes implementarem código.
   
   Saída: backlog de tarefas que podem ser executadas por humanos e/ou IA.

Esse fluxo é **fortemente orientado a especificação**, mesmo que o artefato de “spec” não seja apenas uma OpenAPI, mas uma combinação de requisitos + design + tarefas.

---

## Kiro e Spec-Driven Development

No contexto de SDD:

- O Kiro **força** você a:
  - Especificar intenção (Requirements).
  - Estruturar solução (Design).
  - Quebrar em tarefas (Tasks).
- A IA entra **depois** dessa estruturação, reduzindo chute e *vibe coding*.

Conexão com seus docs de SDD:

- **Requirements** ↔ parte inicial de **Specify** / “intenção e contexto” do SDD.
- **Design** ↔ planos técnicos e contratos antes do código (similar a `Plan` no Spec Kit).
- **Tasks** ↔ decomposição em trabalho granular (como `/tasks` do Spec Kit).

---

## Comparação: Kiro, Spec Kit e SDD “Genérico”

| Aspecto                   | Kiro (AWS)                              | Spec Kit (GitHub)                           | SDD Genérico (Docs deste repo)                 |
|---------------------------|-----------------------------------------|---------------------------------------------|-----------------------------------------------|
| Tipo de ferramenta        | IDE/experiência agentic                 | Toolkit/CLI + prompts                       | Metodologia/abordagem                         |
| Fases principais          | Requirements → Design → Tasks           | Specify → Plan → Tasks → Implement          | Specify/Plan/Implement (agnóstico)            |
| Foco                      | Fluxo completo dentro de um IDE         | Workflow SDD com IA, reusável em vários IDEs| Princípios, não ferramenta específica         |
| Onde roda                 | Ambiente AWS / IDE próprio              | CLI + agentes (Copilot, Claude, etc.)       | Qualquer ambiente                             |
| Nível de opinatividade    | Alto (IDE + UX integrados)              | Médio (workflow 4 fases, mas open source)   | Baixo (conceitos adaptáveis)                  |

---

## Quando faz sentido estudar/usar Kiro

- **Explorar experiências agentic profundas**  
  Se você quer ver um IDE onde agentes planejam e executam trabalho a partir de specs.

- **Comparar abordagens de SDD**  
  Útil para entender como diferentes times (AWS vs GitHub) estão interpretando “Spec‑Driven Development”.

- **Inspirar seu próprio fluxo**  
  Mesmo sem usar Kiro diretamente, o fluxo Requirements → Design → Tasks pode inspirar:
  - Como organizar prompts.
  - Como formalizar “intenção” antes de chamar a IA.

---

## Limitações e pontos de atenção

- **Ferramenta proprietária / dependente de ecossistema AWS**  
  Não é um toolkit open source como o Spec Kit.

- **Abordagem em evolução**  
  Como toda ferramenta nova, práticas e padrões ainda estão se consolidando.

- **Risco de “caixa preta”**  
  Quanto mais o IDE abstrai, mais importante manter **specs claras** e **testes de contrato**, como você já documenta neste repositório.

## Recursos Adicionais

- **Artigo** – [Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- **Guia prático** – [Comprehensive Guide to Spec-Driven Development: Kiro, GitHub Spec Kit, and BMAD method](https://medium.com/@visrow/comprehensive-guide-to-spec-driven-development-kiro-github-spec-kit-and-bmad-method-5d28ff61b9b1)
- **Análise de Spec Kit (com menções ao Kiro)** – [A look at Spec Kit, GitHub's spec-driven software development toolkit](https://ainativedev.io/news/a-look-at-spec-kit-githubs-spec-driven-software-development-toolkit)
- **Prompts comunitários para Kiro** – [Repositório jasonkneen/kiro](https://github.com/jasonkneen/kiro)

---
