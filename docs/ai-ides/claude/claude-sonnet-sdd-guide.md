# Guia: Como usar Claude Sonnet com Spec-Driven Development (SDD)

## Objetivo

Este guia mostra como usar o **Claude Sonnet** (modelo da família Claude, focado em raciocínio/código) em um contexto de **Spec-Driven Development (SDD)**:

- Usando Claude para trabalhar sempre **a partir das specs** (`specs/`)
- Conectando Claude com **docs** (`docs/`) e **KS** (`ks/`)
- Aplicando Claude em tarefas de migração, qualidade, segurança e documentação

O foco aqui é o uso de Claude em:

- Chat (Claude Web / Desktop / CLI / integrações)
- Integrações com IDEs/editors que expõem Claude como modelo (ex.: Windsurf, Cursor, extensões customizadas)

---

## Princípios gerais ao usar Claude com SDD

1. **Spec primeiro, pergunta depois**
   - Antes de pedir qualquer geração de código maior, deixe claro para Claude:
     - Qual spec usar (`specs/openapi.yaml`, `specs/*.yaml`).
     - Quais docs complementam (`docs/concepts.md`, `docs/workflow.md`, `docs/sdd-security.md`, etc.).

2. **Contexto explícito de repositório**
   - Quando possível, use ferramentas de contexto (RAG, upload de arquivos, integrações com repo) para que Claude possa:
     - Ler `specs/`, `docs/`, `ks/` relevantes.
   - Se estiver só no chat, **cole trechos essenciais** (ou resuma) e informe o caminho dos arquivos.

3. **Claude como parceiro de raciocínio, não de chute**
   - Use Claude para:
     - Explicar specs e código.
     - Ajudar a desenhar planos de migração (Specify/Plan).
     - Gerar/ajustar código e testes em Tasks/Implement.
   - Peça sempre para Claude **justificar** decisões quando mudar algo sensível (contrato, segurança, perf).

4. **Sempre fechar o loop com testes e CI**
   - Tudo que Claude gerar deve ser validado por:
     - Testes automatizados.
     - Linters/formatters.
     - Ferramentas de AppSec (SAST/SCA/etc.), quando aplicável.

---

## Fluxos recomendados de uso

### Fluxo 1: Entender e refinar a spec

Use Claude para:

- Resumir uma spec extensa.
- Sugerir melhorias de clareza, consistência ou padronização.
- Identificar ambiguidades que podem prejudicar migrações.

Exemplo de prompt:

> "Aqui está a nossa spec OpenAPI em `specs/openapi.yaml` (ou trecho colado abaixo).  
> - Resuma os principais recursos e endpoints.  
> - Aponte inconsistências de nomenclatura, tipos ou status codes.  
> - Sugira melhorias mantendo compatibilidade com clientes atuais."

### Fluxo 2: Implementar código a partir da spec + KS

1. Mostre a spec relevante (ou o trecho da spec).
2. Mostre o template de KS que representa o "jeito certo" de implementar (ex.: `ks/services/node/rest-service-template/`).
3. Peça algo como:

> "Usando a spec abaixo como contrato e o padrão de código que você vê neste template de serviço, gere a implementação do endpoint `/orders/{id}`.  
> - Não invente campos ou status codes que não estejam na spec.  
> - Respeite também as recomendações de segurança em `docs/sdd-security.md`."

Depois, revise e adapte manualmente; não copie/cole sem olhar.

### Fluxo 3: Criar e fortalecer testes de contrato

1. Forneça a spec e, se possível, trechos de código.
2. Peça para Claude sugerir/gerar testes:

> "Com base na spec abaixo (`specs/orders-openapi.yaml`) e no código atual de `/orders`, gere:  
> - Testes de contrato para os principais cenários felizes  
> - Testes para parâmetros inválidos  
> - Testes para erros de autenticação/autorização  
> Use um estilo de teste compatível com o template em `ks/services/node/contract-tests-template/`."

### Fluxo 4: Apoiar migrações de stack

Combine com `docs/sdd-migration-guide.md`:

> "Estou migrando de `<stack A>` para `<stack B>` mantendo o contrato em `specs/openapi.yaml`.  
> Leia o resumo de boas práticas de migração em `docs/sdd-migration-guide.md` (trechos colados abaixo).  
> - Proponha um plano de migração incremental (Specify → Plan → Tasks → Implement)  
> - Destaque riscos de compatibilidade e performance  
> - Sugira quais testes de contrato e regressão são obrigatórios antes de cada etapa."

---

## Uso de Claude para segurança e AppSec em SDD

Claude pode ajudar a:

- Revisar specs com foco em segurança (authN/authZ, exposição de dados, rate limiting etc.).
- Analisar mudanças de código sob a ótica de `docs/sdd-security.md` e `docs/appsec-tools.md`.
- Sugerir reforços de validação, logging de segurança e mitigação de vulnerabilidades.

Exemplo de prompt:

> "Analise este trecho de código e sua spec correspondente em `specs/openapi.yaml`, usando `docs/sdd-security.md` como referência de boas práticas:  
> - A autenticação e autorização estão claras e consistentes com a spec?  
> - Há risco de exposição de dados sensíveis?  
> - Há validação suficiente de inputs?  
> Sugira melhorias concretas, sem alterar o contrato da API."

---

## Boas práticas específicas com Claude Sonnet

1. **Pedir raciocínio antes do código**
   - Aproveite a força de raciocínio de Sonnet pedindo primeiro: análise, plano, checklist.
   - Só depois peça geração de código.

2. **Usar poucas mensagens, mas bem contextuais**
   - Em vez de mandar o repo inteiro, selecione:
     - A spec relevante.
     - Os docs mais importantes para o problema.
     - O template de KS adequado.

3. **Reaproveitar prompts em `docs/prompts-library.md`**
   - Crie uma seção específica para Claude, com variações de prompts para:
     - Entender/refinar specs.  
     - Gerar código a partir de spec + KS.  
     - Fortalecer testes e migração.

4. **Sempre manter o humano no laço**
   - Claude pode gerar propostas excelentes, mas a decisão final de arquitetura, segurança e contrato é sua.

---

## Integração com outros documentos

- [SDD + IA em IDEs](../ides-sdd-overview.md)
- [AI-Enhanced SDD](../ai-enhanced-sdd.md)
- [Guia de Migração Orientada a SDD](../../migration/sdd-migration-guide.md)
- [Ferramentas de AppSec no Fluxo SDD](../../security/appsec-tools.md)
- [SDD e Segurança: Security-By-Spec](../../security/sdd-security.md)
- [Estrutura de um repositório de KS + IA + SDD](../../fundamentals/ks-repo-structure.md)
- [Biblioteca de Prompts](../../advanced/prompts-library.md)
