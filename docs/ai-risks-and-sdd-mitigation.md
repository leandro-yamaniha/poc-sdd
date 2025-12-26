# Riscos de IA em Desenvolvimento de Software e Como SDD Ajuda a Mitigar

## Objetivo

Este documento resume **riscos práticos** do uso de IA (assistentes, agentes, ferramentas integradas a IDE/CI) e como **Spec-Driven Development (SDD)**, combinado com práticas de AppSec, ajuda a mitigá-los.

Foca em riscos como:

- Acesso indevido e vazamento de dados pessoais/sensíveis
- Apagamento ou corrupção de dados
- Instalação de malware/backdoors ou dependências maliciosas

---

## Visão geral dos riscos

### 1. Vazamento ou acesso indevido a dados pessoais

**Cenários típicos**

- Enviar para a IA, em prompts ou uploads:
  - Dumps de banco com dados reais
  - Logs com informações pessoais ou segredos
  - Arquivos de configuração com chaves, tokens, credenciais
- Modelos/serviços de IA que armazenam prompts e respostas para treino ou diagnóstico sem política clara de privacidade.

**Impactos potenciais**

- Violação de LGPD/GDPR e outras regulações.
- Exposição de segredos de negócio ou credenciais reutilizáveis.
- Dados sensíveis tornando-se parte de datasets de terceiros.

---

### 2. Apagamento, corrupção ou uso indevido de dados

**Cenários típicos**

- Agentes com acesso direto a:
  - Banco de dados de produção
  - Ambientes de produção (CLI, scripts, APIs internas)
- Execução de scripts gerados pela IA contendo:
  - `DELETE` massivos
  - Migrações perigosas
  - Alterações em massa sem rollback fácil

**Impactos potenciais**

- Perda de dados
- Inconsistências graves entre sistemas
- Incidentes de disponibilidade e confiabilidade

---

### 3. Instalação de malware, backdoors ou dependências maliciosas

**Cenários típicos**

- Copiar/colar comandos sugeridos pela IA que:
  - Fazem download de binários de fontes não confiáveis
  - Alteram configuração de SSH, sudoers, antivírus, logging
- Aceitar sugestões de:
  - Pacotes NPM/PyPI pouco conhecidos ou maliciosos
  - Bibliotecas de terceiros sem reputação verificada
- Código gerado com comportamentos escondidos em helpers, scripts de deploy, pipelines de CI.
 - Agentes ou servidores MCP (Model Context Protocol) com plugins/handlers de terceiros executando código com permissões amplas na máquina de desenvolvimento ou no CI, sem revisão de origem/configuração

**Impactos potenciais**

- Comprometimento da máquina de desenvolvimento ou pipelines CI/CD
- Portas de entrada para invasores
- Exfiltração de dados por canais ocultos

---

## Como SDD e práticas associadas ajudam a mitigar

A tabela abaixo mostra como SDD + AppSec + CI/CD podem atuar em cada risco.

| Risco principal                                       | Como SDD ajuda                                      | Outras mitigações complementares |
|------------------------------------------------------|------------------------------------------------------|-----------------------------------|
| Vazamento de dados pessoais para a IA                | Specs e docs definem **quais dados cada endpoint pode manipular**, limites de logging e anonimização | Políticas de uso de IA, mascaramento de dados, ambientes dedicados para treino/teste |
| Acesso indevido além do necessário                   | Contratos claros em specs reduzem necessidade de a IA "vasculhar" sistema inteiro para entender comportamento | Privilégios mínimos, segregação de ambientes, tokens de acesso com escopo restrito |
| Apagamento/corrupção de dados via scripts/agents     | Fluxos de migração e mudanças descritos em `docs/sdd-migration-guide.md`, com steps pequenos e rollback explícito | Revisão humana obrigatória, uso de staging, backups e mecanismos de rollback/feature flags |
| Instalação de dependências maliciosas               | Padrões de dependências e políticas em `docs/appsec-tools.md` e `ks/appsec/` servem como referência para IA | SCA (Software Composition Analysis), allow/deny lists de libs, validação manual antes de instalar |
| Código com backdoors ou padrões suspeitos            | Especificação clara limita o espaço para comportamentos "fora da spec"; testes de contrato e segurança aumentam chance de detectar anomalias | SAST/Code Scanning, revisão de código manual, rotação de chaves e monitoração de tráfego |
| Agentes ou MCP servers comprometidos (malware/vírus) | Arquiteturas orientadas a spec delimitam claramente quais operações de automação são permitidas e através de quais adaptadores/endpoints, reduzindo a superfície de ações perigosas | Execução de agentes/MCP em ambientes isolados (containers/VMs), whitelist de servidores/plugins aprovados, revisão de código/origem, monitoração de logs e tráfego |

---

## Princípios de uso seguro de IA em um fluxo SDD

1. **IA sempre subordinada à spec**
   - A IA não decide contratos nem formatos de dados sozinha.
   - Qualquer proposta de mudança de contrato deve ser refletida em `specs/` e passar por revisão.

2. **Nada de dados reais em prompts**
   - Não enviar dumps de produção, dados pessoais reais ou segredos para assistentes de código.
   - Usar dados sintéticos ou minimamente anonimizados em exemplos.

3. **Privilégios mínimos para agentes**
   - Agentes/assistentes não devem ter acesso direto a produção.
   - Acesso a infra e dados sempre mediado por:
     - Ambientes de dev/staging
     - APIs de automação com escopos bem controlados

4. **Revisão humana obrigatória para ações destrutivas**
   - Scripts de migração, comandos de infra e alterações sensíveis sempre:
     - Revisados por humanos
     - Aplicados primeiro em ambientes de teste

5. **CI/CD como guard-rail**
   - Pipelines de CI/CD devem incluir:
     - Contract tests (aderência à spec)
     - Linters/formatters
     - SAST e SCA
     - Testes de regressão mínimos para fluxos críticos

---

## Como encaixar isso nos documentos existentes

Você pode combinar este guia com:

- **`docs/sdd-security.md`**
  - Para detalhar "Security-By-Spec" e políticas de acesso a dados.
- **`docs/appsec-tools.md` e `docs/sast-tools.md`**
  - Para conectar riscos a ferramentas específicas (SAST, SCA, DAST, secret scanning etc.).
- **`docs/sdd-migration-guide.md`**
  - Para garantir que migrações (onde muitos riscos se materializam) sejam sempre feitas em pequenos passos, com contrato estável.
- **`docs/ides-sdd-overview.md` e guias por ferramenta (Windsurf, Cursor, Copilot, Devin, Claude)**
  - Para reforçar, em cada ambiente, que a IA deve seguir as regras acima.

---

## Checklist rápido

- [ ] Specs definem claramente quais dados pessoais cada endpoint pode manipular?
- [ ] Existe política explícita sobre o que **não** pode ser enviado para ferramentas de IA?
- [ ] Agentes e ferramentas de IA têm acesso apenas a ambientes de dev/staging, com dados controlados?
- [ ] Pipelines de CI/CD incluem SAST, SCA e, quando possível, secret scanning?
- [ ] Mudanças de contrato sempre passam por atualização de spec e revisão humana?
- [ ] Migrações e scripts destrutivos nunca são executados diretamente em produção sem validação prévia?
