# Política de Uso Seguro de IA em Desenvolvimento

## Objetivo

Definir regras práticas para o **uso de ferramentas de IA** (assistentes de código, agentes, Copilot, Claude, Devin, etc.) em projetos que adotam **Spec-Driven Development (SDD)**, com foco em:

- Proteger **dados pessoais e sensíveis**
- Evitar **apagamento/corrupção de dados**
- Reduzir riscos de **malware, backdoors e dependências maliciosas**

Esta política complementa:

- [sdd-security.md](sdd-security.md)
- [ai-risks-and-sdd-mitigation.md](ai-risks-and-sdd-mitigation.md)

---

## 1. Dados que **não** podem ser enviados para IA

É proibido enviar para prompts, uploads ou contexto de ferramentas de IA:

- Dumps de bases de dados de produção (inteiros ou parciais)
- Logs contendo:
  - Dados pessoais (PII), senhas, tokens, chaves de API
  - Dados de clientes, parceiros ou informações contratuais sensíveis
- Arquivos de configuração com segredos (ex.: `.env`, `credentials`, chaves privadas)

Quando precisar de exemplos:

- Use **dados sintéticos** ou parcialmente anonimizados
- Se necessário, gere pequenos datasets artificiais para teste

---

## 2. Privilégios e ambientes

- Ferramentas de IA **não devem ter acesso direto a produção**:
  - Nada de apontar agentes diretamente para bancos ou clusters de produção
  - Acesso sempre mediado por ambientes de **dev/staging** ou sandboxes
- Tokens/chaves usados por integrações de IA devem ter **escopo mínimo**:
  - Apenas o repositório/projeto necessário
  - Sem permissões de administração ou acesso global

### 2.1 Agentes e MCP servers

- Rodar agentes/servers MCP preferencialmente em **containers ou VMs isoladas**, não diretamente na máquina de desenvolvimento ou em runners críticos de CI.
- Usar apenas **servidores, plugins e handlers aprovados** (whitelist interna), com revisão de origem/código sempre que possível.
- Limitar o que agentes/MCP podem fazer via **APIs e adaptadores definidos em spec**, em vez de dar acesso irrestrito a shell, banco ou filesystem.
- Monitorar logs e métricas desses agentes (comportamentos inesperados, acessos fora do padrão, downloads estranhos).

---

## 3. Scripts, comandos e automações sugeridos por IA

Antes de executar comandos ou scripts sugeridos por IA:

- Leia e entenda o que será executado
- Desconfie de comandos que:
  - Fazem `rm -rf`, `DROP TABLE`, `DELETE` massivo
  - Baixam binários de URLs desconhecidas
  - Alteram SSH, sudoers, antivírus ou configurações de segurança
- Sempre que possível:
  - Rode primeiro em **ambiente isolado** (container, VM, sandbox)
  - Registre o comando/script em um PR ou doc, com revisão

Para scripts de migração ou infra, siga também:

- [`docs/sdd-migration-guide.md`](sdd-migration-guide.md)

---

## 4. Dependências e código sugeridos por IA

Ao receber sugestões de bibliotecas, frameworks ou trechos de código:

- Verificar:
  - Reputação da biblioteca (GitHub, downloads, issues)
  - Origem (repositório oficial, organização conhecida)
- Passar novas dependências por SCA (Software Composition Analysis), conforme:
  - [`docs/appsec-tools.md`](appsec-tools.md)
- Nunca aceitar código obscuro ou pouco explicado para áreas sensíveis:
  - Autenticação, autorização, criptografia
  - Acesso a sistemas externos ou envio de dados

---

## 5. Relação com a spec (SDD)

- A IA **não define contratos de API sozinha**:
  - Qualquer mudança de contrato deve ser refletida em `specs/` e revisada
- Sempre que a IA sugerir alterações relevantes:
  - Verificar impacto sobre as specs em `specs/`
  - Atualizar documentação e testes de contrato se a mudança for aprovada

Em caso de conflito entre o que a IA sugere e o que a spec define:

- **A spec vence**
- Ajustar o código ou propor alteração formal da spec (com review)

---

## 6. Revisão humana e CI/CD como linha de defesa

- Toda mudança relevante feita com auxílio de IA deve:
  - Passar por **code review**
  - Ser validada em pipeline de **CI/CD** com:
    - Testes automatizados
    - Linters/formatters
    - SAST/SCA e, quando aplicável, secret scanning
- PRs gerados em grande parte por IA devem:
  - Ser revisados com atenção especial a segurança e contratos
  - Evitar mudanças gigantes e pouco focadas

---

## 7. Incidentes e suspeitas

Se houver suspeita de:

- Vazamento de dados por uso indevido de IA
- Execução de comando/script perigoso sugerido por IA
- Inclusão de dependência ou código potencialmente malicioso

Então:

- Interromper o uso da ferramenta naquele contexto
- Notificar o responsável por segurança/DevSecOps do time
- Registrar o ocorrido (PR, comando, conversa com a IA)
- Atuar conforme o processo interno de resposta a incidentes

---

## 8. Conexão com outros guias deste repositório

Esta política deve ser lida em conjunto com:

- `docs/sdd-security.md` – Princípios de Security-By-Spec
- `docs/ai-risks-and-sdd-mitigation.md` – Riscos detalhados de IA e mitigação
- `docs/ides-sdd-overview.md` – Uso de IA em IDEs com SDD
- Guias específicos por ferramenta (Windsurf, Cursor, Copilot, Devin, Claude)
