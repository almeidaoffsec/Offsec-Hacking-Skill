# Playbook — Containers e Docker

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Modelo

```text
CONTAINER → RUNTIME → PRIVILEGES → MOUNTS → HOST INTERACTION → ESCAPE CANDIDATES
```

## Árvores de decisão

OBSERVAÇÃO: shell dentro de container
→ PERGUNTA: que separação real existe entre container e host?
→ Enumerar: runtime (docker/podman/lxc), capabilities (`capsh --print`), mounts (`/proc/mounts`, docker socket montado?), namespaces, variáveis de ambiente com secrets, volumes sensíveis, processos visíveis.

- **DOCKER SOCKET MONTADO** → montar host filesystem ou criar container privilegiado: escape direto.
- **CAPACITIES PERIGOSAS** → cap_sys_admin (mount), cap_net_admin, cap_sys_ptrace, cap_dac_read_search: cada uma tem caminho de escape conhecido.
- **PRIVILEGIADO** → cgroup release_agent, /dev expostos, mount de device do host.
- **NAMESPACES COMPARTILHADOS** → hostPID? processos do host visíveis → credenciais de processo.
- **SECRETS EM VOLUMES/ENV** → /run/secrets, env vars de CI/CD, service tokens: entrada direta para cloud/k8s (ver playbooks correspondentes).
- **REGISTRIES** → credenciais de registry acessíveis? imagens legíveis com secrets embutidos (history, layers)?

## Regra do playbook

Container é fronteira, não destino: cada escape candidate atualiza ACESSO (hosts comprometíveis) no Target State e potencialmente abre MODE: LINUX no host (privesc a partir do escape).