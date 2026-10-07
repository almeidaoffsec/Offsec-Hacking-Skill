# Playbook — Cloud (AWS, Azure/Entra ID, GCP)

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Princípio central

```text
IDENTIDADE → PERMISSÃO → RECURSO → TRUST → CREDENCIAL → PRIVILÉGIO
```

Cobertura: IAM, roles, service accounts, secrets, storage, compute, metadata, serverless, trust relationships.

## Árvores por hipótese

- **CREDENCIAL OBTIDA** (access key, token, principal) → qual serviço aceita? que permissões? pode escalar?
  - AWS: `sts get-caller-identity` → enumerate IAM → privesc via roles/policies.
  - Azure/Entra: `az ad signed-in-user show`, tokens de managed identity, App Registration/Service Principal abuse.
  - GCP: service account impersonation, compute metadata.
- **SSRF/INSTANCE METADATA** → IMDS (v1 vs v2), metadata de VM (creds temporárias), managed identity — conecta com `playbooks/web.md` (SSRF).
- **STORAGE PÚBLICO/PERMISSIVO** → buckets/containers legíveis ou graváveis: dados + credenciais embutidas.
- **SECRETS/KEY VAULTS** → permissão de leitura de secrets do key management.
- **SERVERLESS/LAMBDA** → env vars com credenciais, role assumida pelo runtime.
- **PRIVESC DE IAM** → políticas modificáveis, role assumption chains, conditions exploráveis.
- **TRUST RELATIONSHIPS** → cross-account, federated identity, entre tenants.

## Regra do playbook

Cada credencial cloud atualiza o Target State com: provider, principal, permissões enumeradas e trust chains. Privesc em cloud é quase sempre permissão mal configurada, não CVE — enumere permissões antes de buscar vulnerabilidades.