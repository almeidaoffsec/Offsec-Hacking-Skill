# Playbook — Thick Clients

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Cobertura

- local storage;
- IPC;
- APIs;
- update mechanisms;
- authentication;
- certificate validation;
- native libraries;
- filesystem interaction;
- privilege boundaries.

## Árvores por hipótese

- **CONFIG/CREDENTIALS LOCAIS** → app armazena tokens/creds onde? config legível/gravável por usuário de menor privilégio?
- **CERT VALIDATION** → validação desabilitada/custom → MITM → tráfego de API capturável.
- **UPDATE MECHANISM** → update sem assinatura ou com validação fraca → code execution no contexto do client.
- **IPC** → pipes/COM/named pipes expostos sem ACL → comunicação com processos privilegiados.
- **PRIVILEGE BOUNDARIES** → partes do client rodam elevadas? DLL hijacking em caminhos graváveis (conectar com `playbooks/windows.md`)?
- **NATIVE LIBS** → parsing de formatos em C/C++ → `playbooks/re.md` e `playbooks/pwn.md`.

## Regra do playbook

Thick client frequentemente conecta múltiplos playbooks: storage local, MITM de API, privesc do host e RE de libs nativas. Mantenha o Target State unificado — o client é o ponto de entrada, o backend e o host são os destinos.