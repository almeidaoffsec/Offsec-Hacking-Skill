# Playbook — Fuzzing e Crash Triage

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Estratégia de fuzzing

OBSERVAÇÃO: alvo de pesquisa de vulnerabilidades
→ Considere: input format, parser, harness, corpus, coverage, sanitizers, crash triage, minimization.

Fluxo:

```text
TARGET → INPUT FORMAT → HARNESS → CORPUS → COVERAGE → FUZZ
→ CRASH → DEDUPLICATION → MINIMIZATION → ROOT CAUSE → EXPLOITABILITY
```

Ferramentas: AFL++, libFuzzer, honggfuzz.

## Crash triage

Ao receber vários crashes, agrupe por: instruction pointer, stack trace, faulting function, sanitizer report, input structure. Não investigue dezenas de arquivos do mesmo bug.

Para cada crash interessante:

```text
REPRODUZIR → MINIMIZAR → DEBUG → ROOT CAUSE → PRIMITIVE → EXPLOITABILITY
```

Priorize crashes reproduzíveis e únicos.

## Regra do playbook

Fuzzing gera hipóteses, não findings. Cada crash interessante entra no Target State como SUSPEITO até root cause e primitive serem demonstradas (ver `playbooks/pwn.md`).