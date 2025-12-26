# Guia: Como usar GitHub Copilot na interface do GitHub com SDD

## Objetivo

Este guia explica como usar o **GitHub Copilot diretamente no GitHub.com** (web), dentro de repositórios que seguem **Spec-Driven Development (SDD)**, para:

- Revisar PRs levando em conta **specs** e **docs**
- Entender código legado em relação às especificações
- Ajudar em migrações de stack e melhorias de segurança
- Sugerir correções para alertas de análise de código (Code Scanning), sempre alinhadas a SDD

Para uso em IDEs/editors (Windsurf, Cursor, VS Code etc.), consulte:

- [Guia: SDD com GitHub Copilot (IDE)](github-copilot-sdd-guide.md)

---

## Pré-requisitos

- **GitHub Copilot habilitado** na sua organização/conta.
- Repositório organizado em torno de SDD, por exemplo:
  - `specs/` – contratos oficiais (OpenAPI/AsyncAPI/GraphQL etc.)
  - `docs/` – guias de SDD, migração, segurança, AppSec, prompts
  - `ks/` – Knowledge Snippets (templates de serviço, testes, CI, AppSec, observabilidade)
- Idealmente, documentação como:
  - `docs/ai-enhanced-sdd.md`
  - `docs/sdd-migration-guide.md`
  - `docs/sdd-security.md`
  - `docs/appsec-tools.md`
  - `docs/ides-sdd-overview.md`

---

## Onde o Copilot aparece no GitHub.com

Dependendo do seu plano e configurações, você pode ver o Copilot em:

- **Pull Requests**
  - Sugerir descrição de PR
  - Explicar um diff
  - Ajudar a escrever/revisar comentários de code review
- **Arquivos individuais** (view de arquivo)
  - Explicar o arquivo
  - Sugerir exemplos de uso ou testes
- **Code Scanning / Alertas** (CodeQL ou outras integrações)
  - Sugerir correções (autofix) para vulnerabilidades ou problemas detectados

A grande diferença em SDD é que você quer que o Copilot **sempre leve em conta**:

- As specs em `specs/`
- Os docs em `docs/`
- (Opcional) Os KS em `ks/`

---

## Fluxos práticos de uso com SDD

### 1. Revisar PRs alinhando com a spec

No contexto de um Pull Request:

1. Abra o PR.
2. Use o Copilot Chat ou botão de explicação (quando disponível).
3. Peça algo como:

> "Explique o que este PR muda em alto nível e verifique se está alinhado com:  
> - `specs/openapi.yaml` (contratos públicos)  
> - `docs/sdd-security.md` (práticas de segurança)  
> - `docs/appsec-tools.md` (ferramentas/limites de segurança).  
> Aponte possíveis desvios de contrato, mudanças de payload/status code e riscos de segurança em bullets."

Use a resposta como **insumo** para o review, nunca como aprovação automática.

#### Variação: foco em compatibilidade

> "Com base em `specs/openapi.yaml`, diga se este PR:  
> - Quebra compatibilidade de contrato com clientes atuais  
> - Introduz novos endpoints/campos que não estão na spec  
> - Altera comportamento esperado de erros."

---

### 2. Gerar descrições de PR com foco em SDD

Ao criar ou editar um PR:

1. Escreva um rascunho da descrição (opcional).
2. Use o Copilot para melhorar/estruturar o texto:

> "Gere uma descrição de PR clara e objetiva, destacando:  
> - Quais endpoints/eventos (segundo `specs/openapi.yaml`) foram impactados  
> - Se há impacto em compatibilidade ou mudança de versão da API  
> - Como isso se alinha ao fluxo Specify → Plan → Tasks → Implement descrito em `docs/workflow.md`  
> - Quais riscos de segurança foram considerados, com base em `docs/sdd-security.md`."

Ajuste o resultado para o seu contexto antes de salvar.

---

### 3. Entender código legado em relação à spec

Ao visualizar um arquivo de código:

1. Abra o arquivo no GitHub.
2. Use a opção do Copilot para explicar o arquivo ou um trecho específico.
3. Peça algo como:

> "Explique este arquivo em relação à spec `specs/openapi.yaml`:  
> - Quais endpoints/rotas ele implementa?  
> - Há divergências óbvias entre o que o código faz e o que a spec descreve?  
> - Existe algum comportamento não documentado na spec que apareça aqui?"

Isso ajuda a identificar:

- Código "fora da spec" que precisa ser trazido para a documentação, ou
- Trechos que deveriam ser ajustados para se alinhar à spec.

---

### 4. Apoiar migrações de stack a partir do GitHub

Durante uma migração (ex.: lib/framework/runtime):

1. No PR de migração, peça ao Copilot:

> "Este PR está migrando a stack de `<stack A>` para `<stack B>`.  
> Use as orientações de `docs/sdd-migration-guide.md` para:  
> - Avaliar se as mudanças estão de fato escondidas atrás do mesmo contrato em `specs/`  
> - Identificar riscos de performance/latência  
> - Sugerir testes adicionais (contrato e regressão) que deveriam existir antes de merge."

2. Use as sugestões para melhorar o plano de migração, adicionar testes e, se necessário, quebrar o PR em partes menores.

---

### 5. Sugerir correções para alertas de segurança (Code Scanning)

Com alertas de segurança ativos (ex.: CodeQL, SAST integrado):

1. Abra um alerta específico.
2. Use o Copilot (quando disponível) para sugerir autofix.
3. Melhore a qualidade da sugestão com um prompt como:

> "Sugira uma correção para este alerta considerando:  
> - As diretrizes de segurança em `docs/sdd-security.md`  
> - As ferramentas e políticas descritas em `docs/appsec-tools.md`  
> - A necessidade de manter compatibilidade com o contrato em `specs/openapi.yaml`."

4. Revise o patch sugerido com atenção, especialmente em relação a:

- Manter o contrato da API estável
- Não introduzir novos riscos de segurança
- Preservar performance razoável

---

## Padrões de prompts recomendados

Você pode guardar estes padrões em `docs/prompts-library.md` (seção GitHub Web / Copilot Web):

- **Revisão guiada por spec**

  > "Explique este PR em alto nível e verifique aderência à spec `specs/openapi.yaml`. Liste possíveis quebras de contrato."  

- **Revisão com foco em segurança**

  > "Analise este PR com foco em segurança, usando `docs/sdd-security.md` e `docs/appsec-tools.md` como referência. Aponte riscos em autenticação, autorização, validação de entrada, exposição de dados e uso de libs."  

- **Migração de stack conforme SDD**

  > "Este PR é parte de uma migração descrita em `docs/sdd-migration-guide.md`. Avalie se as alterações:  
  > - Mantêm o contrato de APIs/eventos  
  > - São pequenas o suficiente para rollback fácil  
  > - Têm cobertura de testes adequada para os fluxos críticos."  

- **Correção de vulnerabilidade**

  > "Sugira uma correção para este alerta de segurança, respeitando as diretrizes de `docs/sdd-security.md` e sem quebrar o contrato descrito em `specs/openapi.yaml`."

---

## Boas práticas ao usar Copilot no GitHub com SDD

- **Tratar respostas como insumo, não como decisão final**
  - Copilot pode apontar riscos e sugerir correções, mas a decisão é sempre humana.

- **Sempre relacionar análise a spec + docs**
  - Evite usar Copilot para revisar código "no vazio"; mencione quais arquivos/documentos guiam a revisão.

- **Evitar PRs gigantes para uso de IA**
  - PRs muito grandes geram respostas superficiais.
  - Prefira migrações e refactors em PRs menores e temáticos.

- **Documentar no PR quando a spec foi usada**
  - Ex.: "Este PR garante compatibilidade com `specs/openapi.yaml` (v1.2)."  
  - Ajuda reviewers (humanos + IA) a entender o contexto esperado.

---

## Integração com outros documentos

Este guia funciona em conjunto com:

- [Guia: SDD com GitHub Copilot (IDE)](github-copilot-sdd-guide.md)
- [SDD + IA em IDEs](ides-sdd-overview.md)
- [Guia de Migração Orientada a SDD](sdd-migration-guide.md)
- [Ferramentas de AppSec no Fluxo SDD](appsec-tools.md)
- [SDD e Segurança: Security-By-Spec](sdd-security.md)
- [Biblioteca de Prompts](prompts-library.md)
