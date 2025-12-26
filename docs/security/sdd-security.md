# SDD e Segurança: Security-By-Spec

## Visão Geral

Este documento descreve como aplicar **Spec-Driven Development (SDD)** com foco em **segurança**, seguindo o princípio de **Security-By-Spec**:

> Requisitos de segurança são modelados e versionados **na especificação** (spec) – não apenas no código ou na infraestrutura.

Com isso, a spec passa a ser a fonte única da verdade (SSOT) também para:

- Autenticação e autorização
- Tratamento de dados sensíveis (PII, segredos, tokens)
- Restrições de uso (rate limiting, quotas)
- Requisitos de auditoria e logging
- Requisitos de compliance e privacidade (LGPD/GDPR, PCI, etc.)

---

## Princípios: Security-By-Spec

### 1. Segurança explícita na spec

Requisitos de segurança devem ser **explícitos** e **descobertos na leitura da spec**, não deduzidos lendo código ou infraestrutura.

- A spec descreve **quem** pode chamar cada operação.
- A spec define **quais dados** são sensíveis e como devem ser tratados.
- A spec deixa claro **quais erros** e códigos de status surgem por motivos de segurança.

### 2. Contrato de segurança versionado

Mudanças de segurança (por exemplo, exigir novos escopos ou roles) são tratadas como **mudanças de contrato**:

- Devem ser versionadas junto com a API (ex.: `/v1` → `/v2`).
- Devem aparecer no changelog e em notas de migração.
- Não devem ser "hotfixes" silenciosos apenas em código.

### 3. Validação automatizada contra a spec

Ferramentas de linting, geração de código e testes de contrato validam se a implementação está aderente aos requisitos de segurança modelados na spec.

- Se a spec exige autenticação, endpoints sem autenticação devem ser bloqueados.
- Se um campo é marcado como sensível, ferramentas SAST e de revisão de código devem dar atenção especial a esse fluxo.

### 4. Segurança como parte do fluxo SDD

Segurança não é uma etapa separada, mas algo que aparece em **todas as fases** do SDD:

- **Specify**: modelar requisitos de segurança funcionais e não funcionais.
- **Plan**: definir arquitetura, componentes e controles de segurança.
- **Tasks**: criar tarefas específicas de segurança (middleware, políticas, testes).
- **Implement**: implementar com validações, testes e SAST integrados.

---

## Checklist de Segurança em Specs OpenAPI

Use esta checklist ao escrever ou revisar uma spec **OpenAPI** em contexto SDD.

### 1. Autenticação e Autorização

- [ ] Definir **mecanismo de autenticação** em `components.securitySchemes`:
  - [ ] API Key (header/query)
  - [ ] HTTP (Basic, Bearer/JWT)
  - [ ] OAuth2 (flows, scopes)
  - [ ] mTLS (client certificates)
- [ ] Declarar `security` global (no topo da spec) para o padrão da API.
- [ ] Sobrescrever `security` em operações que **não exigem** autenticação (ex.: `POST /auth/login`).
- [ ] Modelar **escopos e roles** como parte dos `securitySchemes` (ex.: `read:users`, `admin`).

### 2. Códigos de Status Relacionados à Segurança

Para cada operação sensível, garantir a presença e documentação de:

- [ ] `401 Unauthorized` – ausência ou invalidez de credenciais.
- [ ] `403 Forbidden` – credencial válida, porém escopo/role insuficiente.
- [ ] `429 Too Many Requests` – rate limiting/quota excedida.
- [ ] Mensagens de erro modeladas em `components.schemas` para respostas de erro.

### 3. Dados Sensíveis e PII

- [ ] Identificar campos que carregam **dados pessoais/sensíveis** (PII).
- [ ] Documentar campos sensíveis com descrições claras (ex.: "campo PII, armazenado cifrado").
- [ ] Evitar que respostas de erro retornem dados sensíveis (ex.: tokens, detalhes de stack).
- [ ] Considerar schemata específicos para **mascarar** dados em logs ou respostas.

### 4. Limites e Restrições de Uso

- [ ] Documentar limites de uso relevantes:
  - [ ] **Rate limiting** por IP/usuário/chave.
  - [ ] **Quotas** diárias/mensais.
  - [ ] Tamanho máximo de payload (`max request size`).
- [ ] Incluir campos ou headers de controle (ex.: `X-RateLimit-Remaining`).

### 5. Auditoria e Compliance

- [ ] Especificar operações críticas com marcações de auditoria (ex.: `tag: audit`, descrição explicitando rastreabilidade).
- [ ] Descrever requisitos de trilhas de auditoria em descrições de endpoints (ex.: "esta operação deve gerar evento de auditoria").
- [ ] Incluir campos de metadata em recursos críticos (ex.: `createdBy`, `createdAt`, `lastModifiedBy`).

---

## Checklist de Segurança em Specs AsyncAPI

Para integrações orientadas a eventos, use AsyncAPI com foco em segurança.

### 1. Autenticação e Autorização em Canais

- [ ] Documentar **mecanismo de autenticação** por servidor/canal (ex.: OAuth2, API key, mTLS).
- [ ] Explicitar **quem pode publicar** e **quem pode consumir** em cada canal.
- [ ] Modelar escopos/permissions por canal (ex.: `notifications:publish`, `notifications:read`).

### 2. Segurança de Payloads

- [ ] Identificar mensagens que contêm **dados sensíveis**.
- [ ] Garantir que payloads de eventos não exponham informações além do necessário.
- [ ] Padronizar campos de correlação (`correlationId`) para rastreabilidade segura.

### 3. Política de Retenção e Reprocessamento

- [ ] Documentar política de retenção de mensagens (ex.: tempo de retenção, número máximo).
- [ ] Descrever como reprocessamentos são feitos sem violar privacidade (ex.: remoção/máscara de dados antigos).

### 4. Erros, Dead Letters e Monitoramento

- [ ] Definir canais ou padrões para **dead-letter queues**.
- [ ] Descrever como erros são representados (eventos de erro, metadata extra).
- [ ] Garantir que mensagens de erro não vazem dados sensíveis.

---

## Integração com `docs/sast-tools.md` e Pipeline

O documento [`docs/sast-tools.md`](sast-tools.md) descreve ferramentas de **Static Application Security Testing (SAST)** e práticas relacionadas. Abaixo, como conectar isso ao fluxo SDD e às specs.

### 1. SAST orientado por Spec

- Configure suas ferramentas SAST para:
  - Levar em conta **paths e contratos** definidos na spec (OpenAPI/AsyncAPI).
  - Verificar se endpoints/documentos marcados como sensíveis têm **tratamento adequado** (ex.: criptografia, mascaramento, validação).
- Sempre que a spec mudar (especialmente em partes de segurança), reexecute SAST e testes de contrato.

### 2. Linting de Spec + SAST de Código

Combine dois níveis de checagem:

- **Linting de spec** (ex.: Spectral, Redocly CLI):
  - Regras que exigem `securitySchemes`, `security` global, códigos `401/403/429`, etc.
  - Regras para evitar exposição de campos sensíveis em respostas públicas.

- **SAST de código** (ver detalhes em `sast-tools.md`):
  - Detecção de padrões inseguros na implementação.
  - Verificação de uso seguro de bibliotecas de criptografia, validação de entrada, etc.

### 3. Checks de Segurança no Pipeline CI/CD

Integre segurança ao pipeline em estágios, sempre atrelados à spec:

1. **Validação da spec**
   - Rodar linting (ex.: Spectral) com regras de segurança.
   - Bloquear merge se specs críticas não tiverem `security` configurado.

2. **Geração e validação de código**
   - Se usar codegen, verificar se o código gerado respeita constraints de segurança da spec.

3. **SAST + Testes de Contrato**
   - Rodar ferramentas SAST descritas em `sast-tools.md`.
   - Rodar testes de contrato (ex.: Pact, Spring Cloud Contract) para garantir que respostas de erro e códigos de status estão corretos.

4. **Gate de Segurança para Produção**
   - Definir critérios mínimos (ex.: sem findings críticos em SAST, spec com lint de segurança passando) para promover builds para ambientes superiores.

### 4. Papel da IA nesse fluxo

Ferramentas de IA podem ajudar a:

- Sugerir **regras de linting** de segurança baseadas em políticas internas.
- Gerar ou revisar **checklists de segurança** para cada spec.
- Explicar achados de SAST em linguagem natural, conectando-os a trechos da spec.
- Propor patches iniciais para findings de segurança (sempre com revisão humana).

> A combinação de **SDD + Security-By-Spec + SAST** cria uma linha de defesa onde
> a intenção segura é modelada na spec, validada automaticamente e refletida
> de forma consistente no código e na infraestrutura.

---

## Riscos específicos de IA e como SDD ajuda

O uso de IA (assistentes de código, agentes, ferramentas integradas a IDE/CI) traz riscos adicionais, como:

- Acesso ou vazamento de dados pessoais/sensíveis
- Apagamento ou corrupção de dados por agentes/scripts gerados por IA
- Instalação de malware, backdoors ou dependências maliciosas

Uma análise detalhada desses riscos e como **SDD + AppSec + CI/CD** ajudam a mitigá-los pode ser encontrada em:

- [`docs/ai-risks-and-sdd-mitigation.md`](ai-risks-and-sdd-mitigation.md)
- [`docs/ai-usage-policy.md`](ai-usage-policy.md): política de uso seguro de IA em desenvolvimento (dados, comandos, dependências)
