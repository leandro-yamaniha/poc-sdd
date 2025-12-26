# Spec Kit: Toolkit de Spec-Driven Development com IA

## Visão Geral

O **[Spec Kit](https://github.com/github/spec-kit)** é um toolkit open source do GitHub criado para colocar em prática o **Spec-Driven Development (SDD)** em conjunto com agentes de IA (GitHub Copilot, Claude Code, Gemini, etc.).

Em vez de pedir para a IA "fazer tudo" a partir de um prompt vago, o Spec Kit incentiva um fluxo de trabalho em que você:

- Esclarece **o que** quer construir e **por que** (intenção).
- Transforma essa intenção em **especificações e planos estruturados**.
- Decompõe o trabalho em **tarefas pequenas e testáveis**.
- Deixa a IA atuar em tarefas bem definidas, com **menos adivinhação e mais contexto**.

Esse fluxo é organizado em quatro fases principais, que detalhamos em profundidade em [Workflow Spec Kit: SDD com IA em 4 Fases](spec-kit-workflow.md).

---

## Objetivo do Spec Kit

O Spec Kit existe para resolver um problema comum ao usar IA generativa em desenvolvimento de software:

> Pedidos genéricos ("adicione compartilhamento de fotos no meu app") fazem a IA adivinhar milhares de detalhes omitidos.

Isso leva a:

- Implementações "razoáveis", mas desalinhadas com contexto, arquitetura ou padrões internos.
- Muito retrabalho quando as suposições da IA não batem com a realidade do sistema.

O Spec Kit propõe um **workflow guiado por artefatos claros**:

1. **Specify**: capturar intenção e experiência desejada em forma de especificação.
2. **Plan**: transformar a spec em um plano técnico compatível com sua arquitetura e constraints.
3. **Tasks**: quebrar o plano em tarefas pequenas, implementáveis e testáveis.
4. **Implement**: usar a IA para implementar cada tarefa de forma iterativa, com revisão humana.

---

## Componentes e Conceitos-Chave

Os principais conceitos do Spec Kit se alinham diretamente com SDD:

- **Especificação (Specify)**  
  Documento vivo onde você descreve o problema, usuários, jornadas e requisitos de alto nível.

- **Plano Técnico (Plan)**  
  Desdobramento da spec em arquitetura, tecnologias, integrações, requisitos não funcionais e constraints reais da sua organização.

- **Lista de Tarefas (Tasks)**  
  Decomposição da spec + plano em tarefas pequenas, revisáveis e testáveis, que servem como "contrato" para a IA gerar código.

- **Implementação Orientada a Tarefas (Implement)**  
  A IA gera código tarefa a tarefa, em vez de despejar grandes blocos de código difíceis de revisar.

- **Checkpoints de Validação**  
  Em cada fase, você para para revisar, refinar e só então avança (evitando efeito cascata de erros).

---

## Como o Spec Kit se conecta a este repositório

Este repositório é uma **PoC de Spec-Driven Development (SDD)** apoiada por IA. O Spec Kit entra como uma **implementação concreta** dos princípios que documentamos aqui:

- **Design-First / API-First**: capturar intenção e contratos antes do código.
- **Single Source of Truth (SSOT)**: specs versionadas como fonte da verdade.
- **Automação e Codegen**: gerar código, testes e documentação a partir de specs e planos.
- **Desenvolvimento paralelo**: tarefas bem definidas permitem distribuição de trabalho entre pessoas e agentes de IA.

Mapeamento direto:

- Nossa doc de **[Conceitos Fundamentais](concepts.md)** explica os princípios SDD que o Spec Kit operacionaliza.
- O doc **[Workflow Spec Kit](spec-kit-workflow.md)** mostra o passo a passo detalhado das 4 fases (Specify → Plan → Tasks → Implement).
- O doc **[AI-Enhanced SDD](ai-enhanced-sdd.md)** cobre padrões de uso de IA em cada fase do SDD, incluindo Spec Kit e outras ferramentas.

---

## Instalação e Primeiros Passos (resumo)

Para detalhes completos, consulte a documentação oficial do projeto no GitHub, mas, em alto nível, o fluxo costuma seguir esta linha:

1. **Inicializar um projeto com Spec Kit**

   ```bash
   # Exemplo de inicialização de projeto com Spec Kit
   uvx --from git+https://github.com/github/spec-kit.git specify init <NOME_DO_PROJETO>
   ```

2. **Rodar o workflow em um agente de IA compatível**  
   Em um ambiente com suporte a agentes (por exemplo, Copilot Agents, Claude Code ou outros):

   - Usar o comando/prompt ` /specify ` para gerar ou evoluir a spec.
   - Em seguida, ` /plan ` para produzir o plano técnico.
   - Depois, ` /tasks ` para quebrar o trabalho em tarefas.
   - Por fim, iterar na fase de implementação tarefa a tarefa.

3. **Revisar continuamente**  
   - Revisar specs, planos e tarefas como se fossem código.
   - Garantir que tudo continue alinhado com os princípios de SDD deste repositório.

> Importante: este repositório não empacota o Spec Kit em si; ele **documenta como usar o Spec Kit** (e ferramentas similares) dentro de um fluxo de SDD com IA.

---

## Quando usar o Spec Kit

Alguns cenários típicos onde o Spec Kit se encaixa bem:

- **Projetos greenfield (zero-to-one)**  
  Começar algo novo com clareza desde o início, sem deixar a IA "inventar" a arquitetura.

- **Novas features em sistemas grandes (n-to-n+1)**  
  Garantir que novas funcionalidades respeitem contratos e padrões existentes.

- **Modernização de legado**  
  Capturar a intenção e as regras de negócio de um sistema antigo em uma spec moderna e deixar a IA ajudar na reconstrução.

---

## Recursos Adicionais

- **Repositório oficial**: https://github.com/github/spec-kit  
- **Artigo de referência**: [Spec-Driven Development with AI](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- **Workflow detalhado neste repositório**: [Workflow Spec Kit: SDD com IA em 4 Fases](spec-kit-workflow.md)
