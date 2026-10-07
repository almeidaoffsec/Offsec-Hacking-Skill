# Playbook — API Security

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Triagem

OBSERVAÇÃO: alvo é uma API
→ Determinar: protocolo (REST, GraphQL, SOAP, WebSockets, gRPC quando aplicável), autenticação, autorização, versionamento, endpoints, objetos, identificadores, roles, rate limits, schemas, documentação exposta.

Mapeamento central:

```text
IDENTIDADE → ROLE → ENDPOINT → OBJETO → OPERAÇÃO
```

## Procurar especialmente

- **BOLA/IDOR** → trocar ID de objeto entre identidades diferentes.
- **BROKEN AUTHENTICATION** → tokens fracos, ausência de expiração, credenciais default.
- **BROKEN FUNCTION-LEVEL AUTHORIZATION** → endpoint administrativo acessível com identidade comum.
- **MASS ASSIGNMENT** → campos de role/status enviáveis no payload de registro/atualização.
- **EXCESSIVE DATA EXPOSURE** → resposta expõe mais do que a UI usa.
- **INJECTION** → inputs sem sanitização em filtros, ordenação, queries.
- **RATE-LIMIT WEAKNESSES** → ausência, bypass por header/IP, custo de brute force.
- **BUSINESS LOGIC FLAWS** → fluxos que podem ser pulados ou repetidos.

## Regra do playbook

Para cada endpoint, pergunte: qual IDENTIDADE pode chamar? qual OBJETO toca? qual OPERAÇÃO executa? A resposta orienta o teste de autorização — o erro mais comum em APIs é testar autenticação onde o problema é autorização.