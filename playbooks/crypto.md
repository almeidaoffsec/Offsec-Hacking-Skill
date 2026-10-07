# Playbook — Criptografia e CTF Crypto

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Triagem

Quando receber material criptográfico, determine: algoritmo provável, encoding, tamanho das chaves, parâmetros conhecidos, estrutura dos dados, nonce/IV, reutilização, possíveis erros de implementação.

Diferencie claramente:

```text
ENCODING · HASHING · ENCRYPTION
```

## Em CTFs

Procure erros de implementação ANTES de tentar quebrar primitivas fortes:

- nonce reuse;
- weak randomness;
- key reuse;
- padding mistakes;
- predictable values;
- implementation flaws;
- custom cryptography.

## Regra do playbook

"Crypto mal implementada" é a hipótese padrão em CTF; "primitiva forte quebrada" é hipótese de último recurso. Cada material recebido entra no Target State com algoritmo, modo, chave/parâmetros e o que falta para decifrar.