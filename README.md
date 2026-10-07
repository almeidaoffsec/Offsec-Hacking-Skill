# Offsec-Hacking-Skill

OpenCode skill que atua como copiloto operacional para segurança ofensiva: pentests autorizados, CTFs, engenharia reversa e exploit development.

## Estrutura

```text
SKILL.md        CORE + OPERATIONAL ENGINE + roteamento para playbooks
playbooks/      árvores de decisão por domínio (network, web, ad, pwn, cloud, ...)
TESTS.md        cenários de regressão comportamental
Roadmap.md      plano de evolução da skill
```

## Como funciona

- **CORE** (`SKILL.md`, Parte I): regras permanentes — epistemia da investigação (CONFIRMADO / PROVÁVEL / HIPÓTESE / NÃO TESTADO / DESCARTADO), fases do pentest, redução de incerteza, não inventar resultados.
- **ENGINE** (`SKILL.md`, Parte II): Target State, correlação de evidências, hypothesis engine, attack paths, priorização por information gain, decision engine, modos operacionais (PENTEST, CTF, AD, PWN, RE, ...) e output contracts.
- **PLAYBOOKS** (`playbooks/*.md`): conhecimento especializado em árvores de decisão, lido sob demanda conforme o cenário.

## Evolução

A versão 2 segue o plano de `Roadmap.md`: transformar a base de conhecimento da v1 em um copiloto com estado, capaz de correlacionar descobertas, construir attack paths e decidir o próximo objetivo técnico verificável. Alterações na skill devem ser validadas contra os cenários de `TESTS.md`.