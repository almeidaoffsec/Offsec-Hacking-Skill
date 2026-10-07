# Playbook — Source Code Review

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Metodologia

```text
ENTRYPOINT → INPUT → TRUST BOUNDARY → TRANSFORMAÇÃO → SINK
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

## Fluxo de validação

```text
STATIC CANDIDATE → ROOT CAUSE → REACHABILITY → DYNAMIC VALIDATION → IMPACT
```

## Regra do playbook

Candidate estático sem reachability é HIPÓTESE, não finding. Antes de reportar, determine: o input realmente chega ao sink? há sanitização no caminho? a rota é autenticada/autorizada de forma que o atacante consegue alcançá-la?