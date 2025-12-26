# Ferramentas de AppSec no Fluxo SDD

## Visão Geral

Este documento apresenta como **ferramentas de AppSec** (Security Testing e segurança aplicada a código, containers e infraestrutura) se encaixam em um fluxo de **Spec-Driven Development (SDD)**.

Enquanto `docs/sast-tools.md` aprofunda especificamente em **SAST** (Static Application Security Testing), aqui o foco é o **ecossistema mais amplo de AppSec**:

- SAST (código e specs)
- DAST (Dynamic Application Security Testing)
- SCA (Software Composition Analysis)
- Detecção de segredos (Secret Scanning)
- Scanners de containers e imagens
- Scanners de IaC (Infrastructure as Code)

A ideia é mostrar **como essas ferramentas podem ser guiadas e potencializadas pelas especificações** usadas no SDD.

---

## Por que AppSec é ainda mais crítica em SDD + IA

Com SDD + IA generativa:

- Mais código é gerado automaticamente (assistentes, agentes, codegen).
- Mudanças podem ser rápidas e frequentes.
- É fácil introduzir vulnerabilidades ou configurações inseguras sem perceber.

**Especificações fortes (SDD)** ajudam a:

- Tornar requisitos de segurança **explícitos** (Security-By-Spec, ver `docs/sdd-security.md`).
- Padronizar autenticação, autorização, tratamento de erros e dados sensíveis.
- Criar um "alvo" claro para ferramentas de AppSec validarem.

Ferramentas de AppSec, por sua vez, garantem que **implementação, infraestrutura e dependências** não se afastem do que foi definido na spec.

---

## Categorias de Ferramentas de AppSec e como se conectam ao SDD

### 1. SAST (Static Application Security Testing)

SAST analisa **código-fonte, bytecode ou binários** em busca de vulnerabilidades.

No contexto SDD:

- Pode ser configurado para prestar atenção especial a partes do código que implementam
  **requisitos de segurança descritos na spec** (por exemplo, autenticação, validação de entrada).
- Combinado com a spec, ajuda a verificar se:
  - Entradas documentadas são devidamente validadas.
  - Dados sensíveis (identificados na spec) não vazam para logs/respostas.
  - Controles de acesso definidos em endpoints estão de fato implementados.

> Para detalhes de ferramentas e práticas SAST, veja `docs/sast-tools.md`.

**Ferramentas SAST (exemplos):**

- [SonarQube](https://www.sonarsource.com/products/sonarqube/)
- [SonarCloud](https://www.sonarsource.com/products/sonarcloud/)
- [Semgrep](https://semgrep.dev/)
- [GitHub Advanced Security – Code Scanning](https://docs.github.com/code-security/code-scanning)
- [Snyk Code](https://snyk.io/product/snyk-code/)

---

### 2. DAST (Dynamic Application Security Testing)

DAST testa a aplicação **em execução**, simulando chamadas externas para encontrar
vulnerabilidades (SQL injection, XSS, problemas de autenticação, etc.).

Como SDD ajuda o DAST:

- A spec (OpenAPI/AsyncAPI) fornece um **mapa estruturado** de endpoints/canais:
  - URLs, métodos, parâmetros, tipos de payload.
  - Isso permite gerar automaticamente **casos de teste dinâmicos** para DAST.
- Ferramentas de DAST que suportam importação de OpenAPI podem:
  - Cobrir mais rotas.
  - Gerar testes dinâmicos mais precisos.

Como DAST ajuda o SDD:

- Detecta inconsistências entre spec e comportamento real em produção/staging.
- Revela endpoints expostos que não estão documentados na spec (rota "shadow").

**Ferramentas DAST (exemplos):**

- [OWASP ZAP](https://www.zaproxy.org/)
- [Burp Suite](https://portswigger.net/burp)
- [StackHawk](https://www.stackhawk.com/)
- [Invicti / Netsparker](https://www.invicti.com/)

---

### 3. SCA (Software Composition Analysis)

SCA analisa **dependências de terceiros** (bibliotecas, frameworks, imagens base) em busca de:

- Vulnerabilidades conhecidas (CVEs).
- Licenças incompatíveis.

Relação com SDD:

- Em specs voltadas a APIs e serviços, você pode documentar **padrões de dependências
  permitidas** ou stacks aprovadas.
- SCA garante que implementações geradas (inclusive por IA) **respeitem essas decisões**
  e não introduzam bibliotecas inseguras ou não aprovadas.

Boas práticas em SDD + SCA:

- Definir em documentação (e, quando possível, na spec) quais stacks são suportadas.
- Rodar SCA em CI/CD para cada serviço que implementa contratos definidos nas specs.

**Ferramentas SCA (exemplos):**

- [Snyk Open Source](https://snyk.io/product/open-source-security-management/)
- [GitHub Dependabot](https://github.com/dependabot)
- [Renovate](https://docs.renovatebot.com/)
- [OWASP Dependency-Check](https://jeremylong.github.io/DependencyCheck/)

---

### 4. Secret Scanning (Detecção de Segredos)

Ferramentas de detecção de segredos procuram:

- Tokens de API, chaves privadas, senhas, strings sensíveis no repositório.

No contexto SDD:

- As specs podem conter **referências a sistemas externos** (gateways, provedores de ID, etc.).
- É comum que, durante experimentos com IA, alguém cole tokens/segredos em exemplos ou configs.

Como integrar:

- Ativar secret scanning em todos os repositórios que guardam specs, código e configs.
- Educar time e IA (via prompts) para **nunca incluir segredos reais** em exemplos
  ou arquivos versionados.
- Usar a spec e documentação (`docs/sdd-security.md`) para reforçar o uso de **secret managers**
  (AWS Secrets Manager, HashiCorp Vault, etc.).

**Ferramentas de Secret Scanning (exemplos):**

- [GitHub Advanced Security – Secret Scanning](https://docs.github.com/code-security/secret-scanning)
- [Gitleaks](https://gitleaks.io/)
- [truffleHog](https://github.com/trufflesecurity/trufflehog)
- [GitGuardian](https://www.gitguardian.com/)

---

### 5. Scanners de Containers e Imagens

Ferramentas que verificam imagens Docker/OCI em busca de:

- Pacotes vulneráveis.
- Configurações inseguras.
- Permissões excessivas.

Conexão com SDD:

- Specs e docs (por exemplo, `docs/docker-security-performance.md`) podem definir
  **requisitos mínimos de segurança para imagens**:
  - Imagens base suportadas.
  - Mínimo de pacotes instalados.
  - Não rodar como root.
- Scanners garantem que as imagens usadas por serviços que implementam as specs
  **respeitam esses requisitos**.

**Ferramentas para containers/imagens (exemplos):**

- [Trivy](https://aquasecurity.github.io/trivy/)
- [Grype](https://github.com/anchore/grype)
- [Anchore Enterprise](https://anchore.com/)
- [Clair](https://github.com/quay/clair)

---

### 6. Scanners de IaC (Infrastructure as Code)

Ferramentas que analisam Terraform, CloudFormation, Kubernetes YAML, etc., para
identificar:

- Exposição indevida de portas/serviços.
- Falta de criptografia em repouso.
- Falta de logs/auditoria.

Como SDD e IaC se conectam:

- Specs podem descrever requisitos de **qualidade de serviço e segurança** (por exemplo,
  criptografia obrigatória para certos dados, isolamento de redes, logs).
- IaC representa **como esses requisitos são implementados** na infraestrutura.
- Scanners de IaC verificam se a infra declarada está alinhada com o que a spec e docs de SDD exigem.

**Ferramentas de IaC scanning (exemplos):**

- [Checkov](https://www.checkov.io/)
- [Terrascan](https://runterrascan.io/)
- [tfsec](https://github.com/aquasecurity/tfsec)
- [KICS](https://www.checkmarx.com/technology/kics/)

---

## Onde encaixar AppSec no Workflow SDD

Conectando com o workflow SDD/Spec Kit (Specify → Plan → Tasks → Implement):

- **Specify (Especificar)**
  - Modelar requisitos de segurança na spec (Security-By-Spec, ver `docs/sdd-security.md`).

- **Plan (Planejar)**
  - Escolher quais ferramentas de AppSec serão usadas (SAST, DAST, SCA, IaC, etc.).
  - Definir quality gates e políticas (por exemplo, zero findings críticos em SAST/SCA).

- **Tasks (Tarefas)**
  - Criar tarefas explícitas para:
    - Configurar SAST/DAST/SCA no CI/CD.
    - Adicionar secret scanning.
    - Integrar scanners de containers e IaC.

- **Implement (Implementar)**
  - Rodar as ferramentas de AppSec como parte do pipeline.
  - Usar resultados para ajustar specs, planos e código.

---

## Prós e Contras: SonarQube (exemplo de ferramenta SAST)

Para tornar mais concreto como escolher e avaliar uma ferramenta, abaixo um resumo
de **prós e contras do SonarQube** no contexto de SDD.

### Prós

- **Amplamente adotado**: ferramenta madura e bem conhecida na indústria.
- **Boa cobertura de linguagens**: suporta uma grande variedade de stacks usadas em APIs
  e microsserviços.
- **Integração com CI/CD**: fácil de integrar em pipelines para aplicar quality gates.
- **Regras customizáveis**: permite ajustar regras de análise para refletir padrões internos
  descritos na documentação e nas especificações.
- **Dashboards e métricas**: visão clara de dívida técnica, vulnerabilidades e hotspots
  por projeto/equipe.

### Contras

- **Curva de configuração**: para tirar máximo proveito, exige tempo para
  ajustar regras, perfis de qualidade e quality gates.
- **Foco em código, não em spec**: não analisa diretamente OpenAPI/AsyncAPI; precisa ser
  combinado com linters de spec e outras ferramentas descritas neste repositório.
- **Custo**: a edição comunitária é limitada; recursos avançados podem exigir edição comercial.
- **Ruído inicial**: em bases legadas, a primeira análise pode gerar muitos findings,
  exigindo uma estratégia de priorização e limpeza gradual.

---

## Integração com Outros Docs Deste Repositório

- **`docs/sdd-security.md`** – Princípios de Security-By-Spec, checklist de segurança em specs
  e integração com SAST e pipeline.
- **`docs/sast-tools.md`** – Detalhes e exemplos concretos de ferramentas SAST.
- **`docs/docker-security-performance.md`** – Foca em segurança e performance em Docker,
  complementando a seção de scanners de containers.
- **`docs/docker-optimization-guide.md`** – Otimização de imagens e workflows de build,
  que podem ser combinados com scanners de segurança.

Use este documento como mapa de **quais tipos de ferramentas de AppSec** fazem sentido
implementar para apoiar seu fluxo SDD, e os outros docs para aprofundar em cada área.

---

## Tabela Resumo de Ferramentas de AppSec

### SAST

| Ranking | Ferramenta           | Link                                                       | Prós (resumo)                                               | Contras (resumo)                                                   |
|---------|----------------------|------------------------------------------------------------|-------------------------------------------------------------|--------------------------------------------------------------------|
| 1       | SonarQube            | [Site](https://www.sonarsource.com/products/sonarqube/)    | Muito adotado, boa cobertura de linguagens, integra com CI/CD | Configuração complexa, não analisa spec, recursos avançados pagos |
| 2       | GitHub Code Scanning | [Docs](https://docs.github.com/code-security/code-scanning)| Integrado ao GitHub, automatiza scans em PRs                | Depende do ecossistema GitHub, tuning de rules                     |
| 3       | SonarCloud           | [Site](https://www.sonarsource.com/products/sonarcloud/)   | SaaS do Sonar, fácil integração com GitHub/GitLab           | Depende de serviço externo, custos por repo/linhas de código       |
| 4       | Semgrep              | [Site](https://semgrep.dev/)                               | Regras YAML customizáveis, rápido, ótimo para políticas     | Cobertura de linguagens menor que suites comerciais                |
| 5       | Snyk Code            | [Site](https://snyk.io/product/snyk-code/)                 | Focado em dev, feedback na IDE, integra com Snyk SCA        | Versão gratuita limitada, plataforma comercial                     |

### Plataformas Enterprise (SAST/SCA/DAST)

| Ranking | Ferramenta        | Link                                      | Prós (resumo)                                                | Contras (resumo)                                        |
|---------|-------------------|-------------------------------------------|--------------------------------------------------------------|---------------------------------------------------------|
| 1       | Veracode         | [Site](https://www.veracode.com/)         | Plataforma integrada (SAST+SCA+DAST), boa governança/relatórios | Solução enterprise, custo alto, integração inicial pesada |
| 2       | Fortify (OpenText)| [Site](https://www.opentext.com/products/fortify) | Suite madura com múltiplos scanners, forte em grandes empresas | Ferramenta pesada, curva de adoção alta, licenciamento corporativo |

### DAST

| Ranking | Ferramenta   | Link                                   | Prós (resumo)                                     | Contras (resumo)                                         |
|---------|--------------|----------------------------------------|---------------------------------------------------|----------------------------------------------------------|
| 1       | OWASP ZAP   | [Site](https://www.zaproxy.org/)       | Open source, muito usado, bom para automação      | Configuração pode ser complexa em cenários grandes       |
| 2       | Burp Suite  | [Site](https://portswigger.net/burp)   | Muito poderoso para testes manuais/interativos    | Versão Pro paga, exige operador especialista            |
| 3       | Invicti     | [Site](https://www.invicti.com/)       | DAST corporativo com boa cobertura                | Licenciamento empresarial                                |
| 4       | StackHawk   | [Site](https://www.stackhawk.com/)     | Focado em CI/CD, bom suporte a OpenAPI            | Produto comercial, ainda em adoção comparado a ZAP/Burp |

### SCA (Software Composition Analysis)

| Ranking | Ferramenta          | Link                                                               | Prós (resumo)                                      | Contras (resumo)                                  |
|---------|---------------------|--------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------|
| 1       | Snyk Open Source    | [Site](https://snyk.io/product/open-source-security-management/)   | Bom para dependências, integra bem com CI/repos    | Versão gratuita limitada, custo em larga escala   |
| 2       | GitHub Dependabot   | [Repo](https://github.com/dependabot)                             | Atualiza dependências via PRs automaticamente      | Focado em upgrade, não em análise profunda de risco |
| 3       | Renovate            | [Docs](https://docs.renovatebot.com/)                             | Altamente configurável, ótimo para monorepos       | Pode gerar muitas PRs sem tuning adequado         |
| 4       | OWASP Dependency-Check | [Site](https://jeremylong.github.io/DependencyCheck/)          | Open source, integra com diversos pipelines        | Exige tuning de banco de CVEs e falsos positivos  |

### Secret Scanning

| Ranking | Ferramenta                 | Link                                                                  | Prós (resumo)                                         | Contras (resumo)                                         |
|---------|----------------------------|-----------------------------------------------------------------------|-------------------------------------------------------|----------------------------------------------------------|
| 1       | GitHub Secret Scanning     | [Docs](https://docs.github.com/code-security/secret-scanning)        | Integrado ao GitHub, detecta segredos em pushes/PRs   | Limitado ao ecossistema GitHub, recursos extras pagos    |
| 2       | Gitleaks                   | [Site](https://gitleaks.io/)                                          | Open source, fácil de rodar em CI/local               | Regras precisam ser mantidas/atualizadas pelo time       |
| 3       | truffleHog                 | [Repo](https://github.com/trufflesecurity/trufflehog)                | Bom para varrer históricos de Git e múltiplos formatos| Pode gerar ruído em grandes históricos                  |
| 4       | GitGuardian                | [Site](https://www.gitguardian.com/)                                  | SaaS gerenciado, cobre múltiplos repositórios provedores | Serviço pago                                           |

### Containers e Imagens

| Ranking | Ferramenta         | Link                                            | Prós (resumo)                                         | Contras (resumo)                                |
|---------|--------------------|-------------------------------------------------|-------------------------------------------------------|-----------------------------------------------|
| 1       | Trivy              | [Docs](https://aquasecurity.github.io/trivy/)   | Único binário para imagens, arquivos e IaC, simples   | Saída verbosa, exige política de triagem       |
| 2       | Anchore Enterprise | [Site](https://anchore.com/)                    | Plataforma corporativa com políticas avançadas        | Licenciamento empresarial                      |
| 3       | Grype              | [Repo](https://github.com/anchore/grype)        | Bom para vulnerabilidades em imagens, integra com Anchore | Focado em imagens, menos opinativo em policies |
| 4       | Clair              | [Repo](https://github.com/quay/clair)           | Engine open source para scanners de imagens           | Geralmente usado como componente, não solução pronta |

### IaC (Infrastructure as Code)

| Ranking | Ferramenta | Link                                      | Prós (resumo)                                          | Contras (resumo)                                      |
|---------|-----------|-------------------------------------------|--------------------------------------------------------|-------------------------------------------------------|
| 1       | Checkov   | [Site](https://www.checkov.io/)           | Suporte amplo a Terraform/Kubernetes, muitas regras    | Pode exigir ajuste de regras ao contexto da empresa   |
| 2       | tfsec     | [Repo](https://github.com/aquasecurity/tfsec) | Leve, focado em Terraform, fácil de integrar em CI  | Escopo apenas Terraform                               |
| 3       | Terrascan | [Site](https://runterrascan.io/)          | Suporta múltiplos tipos de IaC, open source           | Menos popular que Checkov/tfsec, comunidade menor     |
| 4       | KICS      | [Site](https://www.checkmarx.com/technology/kics/) | Bom suporte multi‑cloud, regras prontas           | Mais associado ao ecossistema Checkmarx              |
