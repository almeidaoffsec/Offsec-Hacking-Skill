# Offsec-Hacking-Skill

[English](#english) | [Português](#português)

---

## English

OpenCode skill that acts as an operational copilot for offensive security: authorized pentests, CTFs, reverse engineering and exploit development.

### What makes it different

- **Accumulated state**: each session maintains a structured Target State (hosts, identities, credentials, findings, attack paths). New evidence updates the state automatically — a credential found in an SMB backup is immediately correlated with services, hosts and privileges, without the operator asking.
- **Attack paths as the unit of reasoning**: the skill reasons in chains (SMB guest → backup → credential → login → IDOR), not in isolated vulnerabilities. Findings, observed paths and demonstrated impact are kept separate.
- **Explicit epistemics**: every piece of information holds exactly one state — CONFIRMADO / PROVÁVEL / HIPÓTESE / NÃO TESTADO / DESCARTADO. Hypotheses are never promoted to facts; anomalies (403, 429, timeouts) are treated as observations with competing causes until a controlled retest isolates the variable.
- **Attacker mindset with operational discipline**: credential reuse across services is a first-class test; scope quantification after a finding is a legitimate objective; rate limits and blocks are treated as technical evidence (cost of noise, defensive control hypothesis), not as ethical warnings.

### Architecture

```text
SKILL.md        CORE + OPERATIONAL ENGINE + playbook routing table
playbooks/      decision trees per domain (network, web, ad, pwn, cloud, ...)
TESTS.md        behavioral regression scenarios
```

Three layers:

- **CORE** (`SKILL.md`, Part I): permanent rules — investigation epistemics, pentest phases, uncertainty reduction, operational security/stealth, never inventing results.
- **ENGINE** (`SKILL.md`, Part II): Target State, evidence correlation, hypothesis engine, attack path engine, prioritization by information gain, decision engine, operational modes (PENTEST, CTF, AD, PWN, RE, ...), output contracts, reporting engine.
- **PLAYBOOKS** (`playbooks/*.md`): specialized knowledge as conditional decision trees, read on demand per scenario.

Playbooks are decision trees, not checklists: `observation → question → branch`. Technical content only enters when a model would likely get it wrong without it — the skill avoids prompt bloat by design.

### Usage

- Declare the mode and scope in the first message (e.g., "lab pentest mode, scope 10.10.10.25"); the skill infers the mode from context when not declared.
- During long investigations, the skill renders compact state changes on relevant transitions (credential found, access validated, hypothesis discarded) and preserves origin and conditions of every test.
- Diagnostic retests (one variable at a time, order/headers/body recorded) are standard practice for isolating causes.
- Report generation is on demand — end of session or checkpoints — from the session history and Target State, as a document (`report-<scope>.md`) with executive summary separated from technical detail. Evidence gaps are declared, never reconstructed.

### Design principles

- Decision trees, not encyclopedias: the model already holds the technical knowledge; the playbooks encode the decision structure it would not reliably produce alone.
- Warnings are given once, not repeated every turn; scope limits (e.g., data alteration) are stated once and honored.
- Behavioral regression: any meaningful change to `SKILL.md` or `playbooks/*.md` must be validated against `TESTS.md` (19 scenarios: input, expected observations, state update, priority, next objective, unwanted behavior). There is no CI in this repo — validation is behavioral, in fresh sessions.

---

## Português

Skill OpenCode que atua como copiloto operacional para segurança ofensiva: pentests autorizados, CTFs, engenharia reversa e exploit development.

### O que a diferencia

- **Estado acumulado**: cada sessão mantém um Target State estruturado (hosts, identidades, credenciais, findings, attack paths). Evidência nova atualiza o estado automaticamente — uma credencial encontrada em backup SMB é correlacionada de imediato com serviços, hosts e privilégios, sem o operador pedir.
- **Attack paths como unidade de raciocínio**: a skill raciocina em cadeias (SMB guest → backup → credencial → login → IDOR), não em vulnerabilidades isoladas. Finding, caminho observado e impacto demonstrado são mantidos separados.
- **Epistemia explícita**: cada informação possui exatamente um estado — CONFIRMADO / PROVÁVEL / HIPÓTESE / NÃO TESTADO / DESCARTADO. Hipóteses nunca são promovidas a fatos; anomalias (403, 429, timeouts) são tratadas como observações com causas concorrentes até um reteste controlado isolar a variável.
- **Mentalidade de atacante com disciplina operacional**: reuso de credencial entre serviços é teste de primeira ordem; quantificar alcance após um finding é objetivo legítimo; rate limits e bloqueios são tratados como evidência técnica (custo de ruído, hipótese de controle defensivo), não como avisos éticos.

### Arquitetura

```text
SKILL.md        CORE + OPERATIONAL ENGINE + roteamento de playbooks
playbooks/      árvores de decisão por domínio (network, web, ad, pwn, cloud, ...)
TESTS.md        cenários de regressão comportamental
```

Três camadas:

- **CORE** (`SKILL.md`, Parte I): regras permanentes — epistemia da investigação, fases do pentest, redução de incerteza, operational security/stealth, não inventar resultados.
- **ENGINE** (`SKILL.md`, Parte II): Target State, correlação de evidências, hypothesis engine, attack path engine, priorização por information gain, decision engine, modos operacionais (PENTEST, CTF, AD, PWN, RE, ...), output contracts, reporting engine.
- **PLAYBOOKS** (`playbooks/*.md`): conhecimento especializado em árvores de decisão condicionais, lido sob demanda conforme o cenário.

Playbooks são árvores de decisão, não checklists: `observação → pergunta → ramo`. Conteúdo técnico só entra quando um modelo provavelmente erraria sem ele — a skill evita prompt bloat por design.

### Uso

- Declare modo e escopo na primeira mensagem (ex.: "modo pentest de laboratório, escopo 10.10.10.25"); a skill infere o modo do contexto quando não declarado.
- Durante investigações longas, a skill renderiza mudanças de estado compactas nas transições relevantes (credencial encontrada, acesso validado, hipótese descartada) e preserva origem e condições de cada teste.
- Retestes diagnósticos (uma variável por vez, ordem/headers/corpo registrados) são prática padrão para isolar causas.
- Relatório é gerado sob demanda — final da sessão ou checkpoints — a partir do histórico e do Target State, como documento (`report-<escopo>.md`) com sumário executivo separado do detalhe técnico. Gaps de evidência são declarados, nunca reconstruídos.

### Princípios de design

- Árvores de decisão, não enciclopédias: o modelo já possui o conhecimento técnico; os playbooks codificam a estrutura de decisão que ele não produziria de forma confiável sozinho.
- Avisos são dados uma vez, não repetidos a cada turno; limites de escopo (ex.: alteração de dados) são declarados uma vez e honrados.
- Regressão comportamental: qualquer mudança relevante em `SKILL.md` ou `playbooks/*.md` deve ser validada contra `TESTS.md` (19 cenários: input, observações esperadas, state update, prioridade, próximo objetivo, comportamento indesejado). Não há CI neste repo — validação é comportamental, em sessões novas.
