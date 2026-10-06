# Roadmap — Offensive Security Skill

## Pentest, Reverse Engineering & Exploit Development

**Documento:** Plano de evolução da skill  
**Objetivo:** Evoluir a skill atual de uma base técnica abrangente para um copiloto operacional capaz de manter estado, formular hipóteses, correlacionar evidências, construir attack paths e conduzir investigações ofensivas metodicamente.

---

# 1. Visão

A versão atual da skill já possui boa cobertura técnica sobre:

- reconhecimento;
- enumeração;
- network pentesting;
- web hacking;
- API security;
- Linux privilege escalation;
- Windows privilege escalation;
- Active Directory;
- credential analysis;
- lateral movement;
- pivoting;
- engenharia reversa;
- vulnerability research;
- fuzzing;
- exploit development;
- malware analysis;
- criptografia;
- documentação de findings.

A próxima evolução não deve simplesmente adicionar mais ferramentas e técnicas.

O objetivo principal será melhorar a capacidade da skill de:

1. manter contexto;
2. estruturar informações;
3. formular hipóteses;
4. correlacionar descobertas;
5. construir attack paths;
6. priorizar caminhos;
7. selecionar próximos testes;
8. interpretar resultados;
9. adaptar a estratégia;
10. produzir evidências e documentação.

A evolução deve transformar:

```text
KNOWLEDGE BASE
```

em:

```text
OPERATIONAL SECURITY COPILOT
```

---

# 2. Princípio da Evolução

A skill deve operar continuamente segundo:

```text
EVIDENCE
    ↓
INTERPRETATION
    ↓
HYPOTHESIS
    ↓
TEST
    ↓
RESULT
    ↓
STATE UPDATE
    ↓
ATTACK PATH UPDATE
    ↓
NEXT DECISION
```

Cada interação deve aproximar a investigação de um novo estado verificável.

---

# 3. Arquitetura Proposta

A skill será reorganizada em três camadas principais:

```text
OFFENSIVE SECURITY SKILL
│
├── CORE
│
├── OPERATIONAL ENGINE
│
└── SPECIALIZED PLAYBOOKS
```

---

# 4. Core

O Core contém regras permanentes e independentes do tipo de alvo.

Estrutura:

```text
CORE
│
├── Operational Context
├── Interaction Rules
├── Evidence Rules
├── Investigation Principles
├── Tool Selection Principles
├── State Management Rules
├── Output Principles
└── Documentation Principles
```

O Core deve ser pequeno, estável e aplicado durante toda investigação.

---

# 5. Operational Engine

O Operational Engine será responsável por transformar informações em decisões.

Componentes:

```text
OPERATIONAL ENGINE
│
├── Target State
├── Evidence Engine
├── Hypothesis Engine
├── Attack Path Engine
├── Prioritization Engine
├── Decision Engine
├── Mode Engine
└── Output Engine
```

Essa será a principal evolução da versão 2.

---

# 6. Specialized Playbooks

Conhecimento específico será organizado em playbooks.

```text
PLAYBOOKS
│
├── Network
├── Web
├── API
├── Linux
├── Windows
├── Active Directory
├── AD CS
├── Pivoting
├── Reverse Engineering
├── Pwn
├── Fuzzing
├── Malware
├── Cryptography
├── Containers
├── Kubernetes
├── Cloud
├── CI/CD
├── Source Review
├── Mobile
└── Thick Client
```

Playbooks não devem funcionar como simples checklists.

Eles devem possuir decisões condicionais.

---

# 7. Fase 1 — Reestruturação Arquitetural

## Objetivo

Separar claramente:

- regras globais;
- inteligência operacional;
- conhecimento especializado.

## Estrutura alvo

```text
SKILL
│
├── CORE
│
├── ENGINE
│
└── PLAYBOOKS
```

## Benefícios

- menor repetição;
- regras críticas mais visíveis;
- manutenção simplificada;
- expansão modular;
- melhor consistência;
- menor chance de instruções conflitantes.

## Prioridade

**CRÍTICA**

---

# 8. Fase 2 — Target State

## Objetivo

Criar um modelo estruturado do ambiente investigado.

A skill deverá atualizar esse estado conforme novas evidências forem fornecidas.

---

# 9. Estrutura do Target State

```text
TARGET STATE
│
├── Scope
│   ├── Domains
│   ├── Networks
│   └── Assets
│
├── Hosts
│   ├── Addresses
│   ├── Hostnames
│   ├── OS
│   ├── Ports
│   ├── Services
│   └── Technologies
│
├── Applications
│   ├── URLs
│   ├── Technologies
│   ├── Authentication
│   ├── Endpoints
│   └── Roles
│
├── Identities
│   ├── Users
│   ├── Groups
│   ├── Service Accounts
│   └── Sessions
│
├── Credentials
│   ├── Passwords
│   ├── Hashes
│   ├── Tokens
│   ├── Keys
│   └── Cookies
│
├── Access
│   ├── Current Sessions
│   ├── Privileges
│   ├── Compromised Hosts
│   └── Reachable Networks
│
├── Findings
│   ├── Confirmed
│   ├── Suspected
│   └── Informational
│
├── Attack Paths
│   ├── Active
│   ├── Blocked
│   ├── Completed
│   └── Discarded
│
└── Investigation
    ├── Confirmed
    ├── Hypotheses
    ├── Discarded
    └── Next Objectives
```

---

# 10. Atualização do Estado

Cada nova evidência deverá potencialmente atualizar:

```text
HOSTS
SERVICES
IDENTITIES
CREDENTIALS
ACCESS
FINDINGS
ATTACK PATHS
HYPOTHESES
```

Exemplo:

```text
NEW EVIDENCE:

credentials found:
backup_user : ********
```

A skill deve automaticamente considerar:

```text
credential
    ↓
possible services
    ↓
possible hosts
    ↓
possible privileges
    ↓
new attack paths
```

Não deve esperar que o usuário peça explicitamente para correlacionar a credencial.

## Prioridade

**CRÍTICA**

---

# 11. Fase 3 — Evidence Engine

## Objetivo

Classificar corretamente informações durante a investigação.

Estados possíveis:

```text
CONFIRMED
LIKELY
HYPOTHESIS
UNTESTED
DISCARDED
```

A skill nunca deverá promover uma hipótese para fato sem evidência.

Exemplo:

```text
OBSERVATION:
Apache 2.x detected.

WRONG:
The server is vulnerable to CVE-X.

CORRECT:
Version information may justify checking whether the installed build is affected by known vulnerabilities.
```

---

# 12. Confidence Tracking

Quando útil, associar confiança conceitual:

```text
HIGH
MEDIUM
LOW
```

Exemplo:

```text
Hypothesis:
Credentials may be reused over WinRM.

Confidence:
MEDIUM

Required evidence:
Validate whether the account has remote management access.
```

---

# 13. Fase 4 — Hypothesis Engine

## Objetivo

Transformar observações em hipóteses testáveis.

Fluxo:

```text
OBSERVATION
    ↓
INTERPRETATION
    ↓
HYPOTHESIS
    ↓
REQUIRED EVIDENCE
    ↓
TEST
```

Cada hipótese deverá responder:

```text
What suggests this?

What evidence confirms it?

What evidence disproves it?

What is the cheapest useful test?
```

---

# 14. Hypothesis Lifecycle

Estados:

```text
CREATED
    ↓
TESTING
    ↓
CONFIRMED

ou

CREATED
    ↓
TESTING
    ↓
DISCARDED
```

Hipóteses descartadas devem permanecer registradas para evitar repetição.

## Prioridade

**CRÍTICA**

---

# 15. Fase 5 — Information Gain

## Objetivo

Priorizar testes que reduzam mais incerteza.

Princípio conceitual:

```text
TEST VALUE =
Information Gain
× Probability
× Potential Impact
÷ Operational Cost
```

Não é necessário calcular valores numericamente.

A fórmula representa uma heurística.

A skill deve favorecer testes que:

- eliminem várias hipóteses;
- confirmem caminhos importantes;
- sejam baratos;
- sejam rápidos;
- produzam informações reutilizáveis.

---

# 16. Exemplo de Information Gain

Se existem cinco possíveis caminhos e um único teste pode eliminar quatro deles:

```text
TEST A
Cost: Low
Information Gain: High
```

ele deve normalmente ser priorizado sobre:

```text
TEST B
Cost: High
Information Gain: Low
```

## Prioridade

**ALTA**

---

# 17. Fase 6 — Attack Path Engine

## Objetivo

Transformar findings isolados em caminhos completos de ataque.

A unidade principal de raciocínio passa a ser:

```text
ATTACK PATH
```

e não somente:

```text
VULNERABILITY
```

---

# 18. Representação de Attack Paths

Exemplo:

```text
Anonymous SMB
      ↓
Readable Share
      ↓
Configuration Backup
      ↓
Credential
      ↓
Domain User
      ↓
Remote Access
      ↓
SERVER01
      ↓
Privilege Escalation
      ↓
Administrator
```

---

# 19. Estado de Attack Paths

Cada caminho pode possuir estado:

```text
UNTESTED
ACTIVE
BLOCKED
CONFIRMED
COMPLETED
DISCARDED
```

---

# 20. Attack Path Scoring

Avaliar conceitualmente:

```text
Probability
Impact
Information Gain
Operational Cost
Number of Assumptions
Current Evidence
```

Exemplo:

```text
PATH A

Probability: HIGH
Impact: MEDIUM
Cost: LOW
Assumptions: LOW
```

versus:

```text
PATH B

Probability: LOW
Impact: CRITICAL
Cost: HIGH
Assumptions: HIGH
```

PATH A normalmente deve ser investigado primeiro.

## Prioridade

**CRÍTICA**

---

# 21. Attack Path Correlation

A skill deverá procurar relações automaticamente.

Exemplo:

```text
USERNAME
+
PASSWORD
+
SMB
+
WINRM
+
DOMAIN MEMBERSHIP
```

pode formar um caminho.

Outro exemplo:

```text
WEB FILE READ
+
CONFIGURATION FILE
+
DATABASE CREDENTIAL
+
PASSWORD REUSE
+
SSH
```

pode formar outro.

O objetivo é detectar combinações que não seriam críticas isoladamente.

---

# 22. Fase 7 — Decision Engine

## Objetivo

Escolher deliberadamente o próximo objetivo técnico.

A pergunta principal será:

```text
WHAT SHOULD WE LEARN OR ACHIEVE NEXT?
```

e não simplesmente:

```text
WHAT TOOL SHOULD WE RUN NEXT?
```

---

# 23. Next Objective

Cada etapa complexa deverá terminar com:

```text
NEXT OBJECTIVE
```

Exemplos:

```text
Determine whether the discovered credential provides remote access.
```

```text
Determine the exact offset to RIP.
```

```text
Determine whether the current account has an exploitable ACL relationship.
```

```text
Identify a leak capable of recovering the PIE base.
```

O objetivo deve ser específico e verificável.

## Prioridade

**CRÍTICA**

---

# 24. Fase 8 — Operational Modes

Criar modos especializados.

```text
MODE: PENTEST
MODE: CTF
MODE: NETWORK
MODE: WEB
MODE: API
MODE: AD
MODE: RE
MODE: PWN
MODE: MALWARE
MODE: RESEARCH
MODE: REPORT
```

O modo poderá ser:

```text
EXPLICIT
```

ou:

```text
INFERRED
```

pela skill.

---

# 25. MODE: PENTEST

Priorizar:

- evidência;
- impacto;
- reprodutibilidade;
- estabilidade;
- documentação;
- minimização de alterações desnecessárias;
- remediation.

---

# 26. MODE: CTF

Priorizar:

- velocidade;
- pistas;
- caminhos prováveis;
- progressão;
- identificação da intenção do desafio;
- redução rápida do espaço de busca.

---

# 27. MODE: AD

Modelar principalmente:

```text
IDENTITY
→ GROUP
→ ACL
→ SESSION
→ HOST
→ CREDENTIAL
→ PRIVILEGE
```

Tratar o domínio como grafo.

---

# 28. MODE: RE

Fluxo dominante:

```text
INPUT
→ DATA FLOW
→ CONTROL FLOW
→ TRANSFORMATION
→ BEHAVIOR
```

Perguntas principais:

```text
What does this function do?

What input does it consume?

Where does the input flow?

Which operations are security relevant?
```

---

# 29. MODE: PWN

Fluxo dominante:

```text
CRASH
→ ROOT CAUSE
→ CONTROL
→ PRIMITIVE
→ MITIGATIONS
→ STRATEGY
→ PoC
→ RELIABILITY
```

---

# 30. MODE: MALWARE

Separar:

```text
STATIC
```

de:

```text
DYNAMIC
```

e correlacionar:

```text
CODE
→ CAPABILITY
→ OBSERVED BEHAVIOR
```

---

# 31. MODE: REPORT

Interromper exploração e transformar evidências em:

```text
FINDINGS
ATTACK PATHS
IMPACT
ROOT CAUSE
REMEDIATION
```

## Prioridade da implementação de modos

**ALTA**

---

# 32. Fase 9 — Output Contracts

## Objetivo

Criar respostas consistentes conforme o contexto.

Os formatos devem orientar o raciocínio sem tornar toda resposta artificialmente longa.

---

# 33. Output Contract — Investigation

```text
[STATE CHANGE]

Novas informações confirmadas.

[ANALYSIS]

Interpretação das evidências.

[ATTACK PATHS]

Caminhos relevantes.

[NEXT OBJECTIVE]

Objetivo imediato.

[ACTIONS]

Testes necessários.

[EXPECTED]

Como interpretar cada possível resultado.
```

---

# 34. Output Contract — Exploit Development

```text
[TARGET]

Architecture:
OS:
Binary:
Libraries:

[MITIGATIONS]

NX:
PIE:
ASLR:
Canary:
RELRO:

[BUG]

Root cause.

[CONTROL]

Controlled data/registers.

[PRIMITIVES]

Read:
Write:
Leak:
Control Flow:

[CURRENT STRATEGY]

Current exploitation strategy.

[NEXT OBJECTIVE]

Smallest necessary objective.
```

---

# 35. Output Contract — Reverse Engineering

```text
[FUNCTION]

Purpose:

[INPUTS]

Controlled inputs:

[DATA FLOW]

Input propagation:

[CONTROL FLOW]

Relevant branches:

[INTERESTING OPERATIONS]

Security-sensitive operations:

[VULNERABILITY CANDIDATES]

Potential bugs:

[NEXT BREAKPOINT]

Most useful dynamic test:
```

---

# 36. Output Contract — Active Directory

```text
[IDENTITY]

Current identity.

[ACCESS]

Current access.

[RELATIONSHIPS]

Relevant groups/ACLs/sessions.

[ATTACK PATHS]

Candidate paths.

[NEXT OBJECTIVE]

Next privilege or relationship to validate.

[ACTIONS]

Tests required.
```

## Prioridade

**ALTA**

---

# 37. Fase 10 — Conditional Playbooks

## Objetivo

Converter conhecimento técnico em árvores de decisão.

Playbooks não serão listas de comandos.

Formato:

```text
OBSERVATION
    ↓
QUESTION
   / \
 YES  NO
 ↓     ↓
PATH  PATH
```

---

# 38. Network Playbooks

Criar playbooks para:

- port discovery;
- service enumeration;
- SMB;
- LDAP;
- Kerberos;
- SSH;
- FTP;
- SNMP;
- MSSQL;
- MySQL;
- PostgreSQL;
- WinRM;
- RDP;
- DNS;
- NFS.

---

# 39. Web Playbooks

Criar fluxos para:

- authentication;
- authorization;
- IDOR;
- SQLi;
- command injection;
- SSTI;
- SSRF;
- XXE;
- file upload;
- LFI;
- path traversal;
- deserialization;
- JWT;
- OAuth/OIDC;
- GraphQL;
- race conditions;
- business logic.

---

# 40. Linux Playbooks

Criar árvores para:

- sudo;
- SUID;
- SGID;
- capabilities;
- cron;
- writable files;
- PATH;
- libraries;
- credentials;
- services;
- containers;
- NFS;
- sockets;
- kernel.

---

# 41. Windows Playbooks

Criar árvores para:

- token privileges;
- services;
- scheduled tasks;
- registry;
- ACLs;
- credentials;
- PowerShell history;
- local groups;
- service permissions;
- file permissions.

---

# 42. Active Directory Playbooks

Criar fluxos para:

- domain enumeration;
- Kerberoasting;
- AS-REP Roasting;
- ACL abuse;
- delegation;
- sessions;
- trusts;
- GPO;
- service accounts;
- AD CS;
- credential reuse;
- lateral movement.

---

# 43. Reverse Engineering Playbooks

Criar fluxos para:

```text
UNKNOWN BINARY
→ TRIAGE
→ STATIC ANALYSIS
→ DYNAMIC ANALYSIS
→ DATA FLOW
→ ROOT CAUSE
```

---

# 44. Pwn Playbooks

Criar árvores para:

```text
CRASH
→ CONTROL?
→ OFFSET
→ MITIGATIONS
→ PRIMITIVE
→ STRATEGY
```

Especializações:

- stack overflow;
- format string;
- ret2win;
- ret2libc;
- ROP;
- information leak;
- heap;
- use-after-free.

---

# 45. Fuzzing Playbooks

Fluxo:

```text
TARGET
→ INPUT FORMAT
→ HARNESS
→ CORPUS
→ COVERAGE
→ FUZZ
→ CRASH
→ DEDUPLICATION
→ MINIMIZATION
→ ROOT CAUSE
→ EXPLOITABILITY
```

## Prioridade

**ALTA**

---

# 46. Fase 11 — Containers

Adicionar cobertura especializada para:

- Docker;
- container enumeration;
- mounted sockets;
- capabilities;
- namespaces;
- volumes;
- secrets;
- container escape conditions;
- registries.

Modelo:

```text
CONTAINER
→ RUNTIME
→ PRIVILEGES
→ MOUNTS
→ HOST INTERACTION
→ ESCAPE CANDIDATES
```

## Prioridade

**MÉDIA**

---

# 47. Fase 12 — Kubernetes

Adicionar:

- service accounts;
- tokens;
- RBAC;
- pods;
- secrets;
- namespaces;
- workloads;
- node access;
- API server;
- admission configuration;
- cluster roles.

Modelo:

```text
IDENTITY
→ RBAC
→ RESOURCE
→ SECRET
→ WORKLOAD
→ NODE
→ CLUSTER
```

## Prioridade

**MÉDIA**

---

# 48. Fase 13 — Cloud

Adicionar playbooks para:

```text
AWS
Azure / Entra ID
GCP
```

Princípio central:

```text
IDENTITY
→ PERMISSION
→ RESOURCE
→ TRUST
→ CREDENTIAL
→ PRIVILEGE
```

Cobrir:

- IAM;
- roles;
- service accounts;
- secrets;
- storage;
- compute;
- metadata;
- serverless;
- trust relationships.

## Prioridade

**MÉDIA**

---

# 49. Fase 14 — CI/CD e Supply Chain

Adicionar:

- GitHub;
- GitLab;
- runners;
- pipelines;
- secrets;
- artifacts;
- package registries;
- container registries;
- deployment credentials.

Modelo:

```text
SOURCE
→ PIPELINE
→ RUNNER
→ SECRET
→ ARTIFACT
→ DEPLOYMENT
```

## Prioridade

**MÉDIA**

---

# 50. Fase 15 — Source Code Review

Adicionar metodologia:

```text
ENTRYPOINT
→ INPUT
→ TRUST BOUNDARY
→ TRANSFORMATION
→ SINK
```

Procure:

- authentication;
- authorization;
- injection;
- deserialization;
- filesystem;
- cryptography;
- secrets;
- SSRF;
- command execution;
- unsafe memory operations.

Fluxo:

```text
STATIC CANDIDATE
→ ROOT CAUSE
→ REACHABILITY
→ DYNAMIC VALIDATION
→ IMPACT
```

## Prioridade

**MÉDIA**

---

# 51. Fase 16 — Mobile

Primeira prioridade:

```text
ANDROID
```

Depois:

```text
IOS
```

Cobrir:

- application structure;
- local storage;
- IPC;
- deep links;
- WebViews;
- API communication;
- authentication;
- secrets;
- cryptography;
- native libraries.

## Prioridade

**MÉDIA/BAIXA**

---

# 52. Fase 17 — Thick Clients

Adicionar análise de:

- local storage;
- IPC;
- APIs;
- update mechanisms;
- authentication;
- certificate validation;
- native libraries;
- filesystem interaction;
- privilege boundaries.

## Prioridade

**MÉDIA/BAIXA**

---

# 53. Fase 18 — Reporting Engine

## Objetivo

Transformar estado técnico em documentação.

Fluxo:

```text
EVIDENCE
→ FINDING
→ ROOT CAUSE
→ IMPACT
→ ATTACK PATH
→ SEVERITY
→ REMEDIATION
```

---

# 54. Finding Model

Cada finding deverá poder conter:

```text
TITLE

SEVERITY

DESCRIPTION

AFFECTED ASSETS

EVIDENCE

REPRODUCTION

ROOT CAUSE

IMPACT

REMEDIATION

CWE

CVSS

REFERENCES
```

Somente incluir classificações quando justificadas.

---

# 55. Finding versus Attack Path

Manter distinção:

```text
FINDING
```

é uma fraqueza específica.

```text
ATTACK PATH
```

é uma cadeia de condições e findings.

Exemplo:

```text
Finding A: readable backup
Finding B: credential reuse
Finding C: excessive service privilege
```

podem formar:

```text
BACKUP
→ CREDENTIAL
→ REMOTE ACCESS
→ PRIVILEGE ESCALATION
```

O impacto do caminho pode ser maior do que o impacto individual dos findings.

## Prioridade

**MÉDIA/ALTA**

---

# 56. Fase 19 — Test Suite da Skill

## Objetivo

Validar se alterações futuras melhoram ou degradam o comportamento.

Criar aproximadamente:

```text
20–30 CORE SCENARIOS
```

comportamentais.

---

# 57. Estrutura dos Testes

Cada teste deverá conter:

```text
INPUT

EXPECTED OBSERVATIONS

EXPECTED STATE UPDATE

EXPECTED PRIORITY

EXPECTED NEXT OBJECTIVE

UNWANTED BEHAVIOR
```

---

# 58. Exemplo — Network

```text
INPUT:

Nmap:
22/tcp
80/tcp
445/tcp

SMB allows guest access.
Share "backups" is readable.
```

Esperado:

```text
- registrar SMB guest access;
- priorizar análise do share;
- criar hipótese sobre backups/configurações;
- não recomendar exploração aleatória do SSH;
- atualizar attack paths;
- definir próximo objetivo verificável.
```

---

# 59. Exemplo — Pwn

```text
INPUT:

ELF amd64

NX: enabled
PIE: enabled
Canary: disabled

Crash allows RIP control.
```

Esperado:

```text
- registrar mitigations;
- registrar RIP control;
- identificar control-flow primitive;
- não assumir ret2libc imediatamente;
- determinar necessidade de informações sobre leaks/imports/gadgets;
- definir próximo objetivo.
```

---

# 60. Exemplo — Active Directory

```text
INPUT:

User:
svc_backup

Groups:
Backup Operators

Accessible host:
FILE01
```

Esperado:

```text
- correlacionar grupo e host;
- investigar privilégios associados;
- construir possível attack path;
- evitar enumeração aleatória;
- solicitar apenas evidências necessárias.
```

---

# 61. Regression Testing

Toda alteração importante na skill deve ser comparada com os cenários existentes.

Objetivo:

```text
NEW FEATURE
    ↓
RUN TEST CASES
    ↓
COMPARE BEHAVIOR
    ↓
ACCEPT / ADJUST
```

Isso reduz regressões causadas pelo crescimento do prompt.

## Prioridade

**ALTA**

---

# 62. Releases Planejados

A evolução será dividida em releases.

---

# 63. Version 2.0 — Intelligence Engine

Objetivo:

Transformar a skill em copiloto operacional.

Implementar:

- nova arquitetura;
- Core;
- Target State;
- Evidence Engine;
- Hypothesis Engine;
- Information Gain;
- Attack Path Engine;
- Prioritization;
- Decision Engine;
- Operational Modes;
- Output Contracts.

Resultado esperado:

```text
KNOWLEDGE
+
STATE
+
REASONING
+
DECISION
```

---

# 64. Version 2.1 — Operational Playbooks

Implementar playbooks condicionais para:

- Network;
- Web;
- API;
- Linux;
- Windows;
- Active Directory;
- AD CS;
- Pivoting;
- Reverse Engineering;
- Pwn;
- Fuzzing;
- Malware.

Resultado esperado:

```text
OBSERVATION
→ DECISION TREE
→ TEST
→ RESULT
→ NEXT BRANCH
```

---

# 65. Version 2.2 — Extended Environments

Adicionar:

- Docker;
- Kubernetes;
- AWS;
- Azure/Entra ID;
- GCP;
- CI/CD;
- Supply Chain;
- Source Review;
- Mobile;
- Thick Clients.

Resultado esperado:

ampliar a cobertura sem alterar o funcionamento central do Operational Engine.

---

# 66. Version 2.3 — Reporting & Knowledge Integration

Implementar:

- Reporting Engine;
- finding generation;
- attack-path reporting;
- evidence correlation;
- remediation generation;
- CWE mapping;
- CVSS support;
- references;
- executive/technical separation.

---

# 67. Version 2.4 — Validation & Optimization

Implementar:

- test suite;
- regression scenarios;
- prompt simplification;
- deduplication;
- conflict detection;
- token optimization;
- comportamento consistente entre modos.

---

# 68. Ordem Recomendada de Implementação

```text
1. Architecture
        ↓
2. Target State
        ↓
3. Evidence Engine
        ↓
4. Hypothesis Engine
        ↓
5. Information Gain
        ↓
6. Attack Path Engine
        ↓
7. Decision Engine
        ↓
8. Operational Modes
        ↓
9. Output Contracts
        ↓
10. Playbooks
        ↓
11. Extended Environments
        ↓
12. Reporting
        ↓
13. Test Suite
        ↓
14. Optimization
```

---

# 69. O Que Não Fazer Primeiro

Evitar começar adicionando dezenas de novas ferramentas.

Evitar simplesmente aumentar listas de:

- comandos;
- CVEs;
- técnicas;
- scanners;
- exploits;
- frameworks.

A skill atual já possui conhecimento técnico suficiente para a próxima etapa.

O principal gargalo é transformar conhecimento em decisão.

---

# 70. Critério de Sucesso da v2

A v2 estará funcionando corretamente quando, diante de uma investigação longa, conseguir:

```text
REMEMBER
what was discovered

UNDERSTAND
what it means

CORRELATE
different findings

HYPOTHESIZE
possible explanations

PRIORITIZE
possible attack paths

DECIDE
the next objective

TEST
the hypothesis

UPDATE
the operational state
```

sem retornar repetidamente para enumeração genérica.

---

# 71. Exemplo do Comportamento Final

Entrada inicial:

```text
Nmap found:
22
80
445
```

Depois:

```text
SMB guest works.
```

Depois:

```text
Found backup.zip.
```

Depois:

```text
Inside it there's a config containing credentials for svc_web.
```

A skill não deve tratar cada mensagem isoladamente.

Ela deverá construir:

```text
ATTACK PATH

SMB
↓
Guest Access
↓
Readable Backup
↓
Configuration Exposure
↓
svc_web Credential
↓
Credential Validation
↓
Potential Remote/Application Access
```

e responder:

```text
CURRENT STATE

Credential obtained:
svc_web

SOURCE:
SMB backup

NEXT OBJECTIVE:

Determine where svc_web is valid and what privileges it provides.
```

Essa continuidade representa o principal objetivo arquitetural da versão 2.

---

# 72. Visão Final

A evolução desejada é:

```text
v1

Technical Offensive Security Assistant
```

para:

```text
v2

Stateful Offensive Security Copilot
```

capaz de operar segundo:

```text
OBSERVE
↓
UNDERSTAND
↓
CORRELATE
↓
HYPOTHESIZE
↓
PRIORITIZE
↓
TEST
↓
LEARN
↓
ADAPT
```

O objetivo final não é fazer a skill conhecer o maior número possível de ferramentas.

O objetivo é fazê-la escolher, com base nas evidências disponíveis, **qual é a próxima ação técnica que mais reduz a incerteza e mais aproxima a investigação do objetivo**.
