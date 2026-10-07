# Playbook — Mobile (Android primeiro, iOS depois)

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Cobertura

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

## Árvores por hipótese

- **APK/IPA OBTIDO** → decompilar/desempacotar → storage local com tokens/creds? secrets hardcoded? crypto fraca?
- **DEEP LINKS/IPC** → exported components? deep link acessível a apps maliciosos? IPC sem permissão?
- **WEBVIEWS** → JavaScript habilitado + file access? bridges JS-native expostas?
- **API COMMUNICATION** → cert pinning ausente/quebrável? tokens em logs? autorização por objeto no backend (conectar com `playbooks/api.md`)?
- **NATIVE LIBRARIES** → bugs de memória em libs nativas → `playbooks/re.md`.

## Regra do playbook

O backend da app é frequentemente o alvo de maior valor: falhas mobile (pinning, storage) são meios para tokens, e tokens levam a APIs. Correlacione com o Target State da API correspondente.