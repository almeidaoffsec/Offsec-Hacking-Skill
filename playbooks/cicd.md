# Playbook — CI/CD e Supply Chain

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Modelo

```text
SOURCE → PIPELINE → RUNNER → SECRET → ARTIFACT → DEPLOYMENT
```

## Árvores por hipótese

- **REPOSITÓRIO COMPROMETIDO** → workflow/pipeline editável? → execução de código no runner.
- **WORKFLOW TRIGGER** → pull_request target com injection? branch com on:push? GitHub Actions expression injection via issue/PR title/body?
- **SECRETS DO PIPELINE** → secrets expostos em logs? em artifacts? acessíveis em steps fork?
- **RUNNER** → self-hosted em rede sensível? persistência via runner (dados residuais entre jobs)?
- **REGISTRIES** → imagem/container registry gravável? → poisoning de deployment.
- **ARTIFACTS/CACHE** → artifact malicioso consumido por outro job (artifact poisoning)?
- **DEPLOYMENT CREDENTIALS** → credenciais de deploy acessíveis → acesso direto a produção (conectar com `playbooks/cloud.md` quando aplicável).

Plataformas: GitHub Actions, GitLab CI (runners, pipelines, secrets, artifacts, package/container registries).

## Regra do playbook

CI/CD é o caminho de confiança mais curto entre código e produção: trate permissões de workflow como código executável privilegiado. Cada secreto de pipeline é credencial de produção no Target State.