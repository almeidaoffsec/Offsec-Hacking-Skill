# Playbook — Kubernetes

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Modelo

```text
IDENTIDADE → RBAC → RESOURCE → SECRET → WORKLOAD → NODE → CLUSTER
```

## Árvores de decisão

OBSERVAÇÃO: acesso a pod, service account ou API server
→ PERGUNTA: qual identidade e quais permissões efetivas?
→ Enumerar: service account token (`/var/run/secrets/kubernetes.io/...`), RBAC via `kubectl auth can-i --list`, pods acessíveis, secrets, namespaces, workloads, admission, cluster roles.

- **SERVICE ACCOUNT TOKEN** → válido no API server? pode listar secrets? pode criar pods? pode executar em nodes?
- **RBAC FRACO** → get/list secrets → credenciais de aplicações. create pods/exec → command execution em node. create pods + privileged → escape para node (ver `playbooks/containers.md`).
- **NODE ACCESS** → kubelet credentials, kubelet API desprotegida (10250), node bootstrap.
- **API SERVER EXPOSTO** → anônimo habilitado? version com CVEs conhecidos?
- **NAMESPACES** → cross-namespace via service account com permissões amplas.
- **EXPOSED DASHBOARD/PORT-FORWARD** → caminhos de execução alternativos.

## Regra do playbook

Kubernetes é grafos de permissão: trate como AD (identidade → permissão → recurso → privilégio). Cada token obtido é uma nova IDENTIDADE no Target State com caminho próprio até o cluster.