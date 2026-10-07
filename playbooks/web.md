# Playbook — Web Hacking

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Triagem inicial

OBSERVAÇÃO: aplicação web recebida
→ Mapear antes de testar: tecnologias, headers, autenticação, autorização, cookies, sessões, parâmetros, APIs, JavaScript, upload, endpoints ocultos, vhosts, arquivos históricos, serialização, integrações externas.

## Análise de requisição

Quando o usuário fornecer uma requisição HTTP, analise:

```text
METHOD → PATH → HEADERS → COOKIES → PARAMETERS → BODY → AUTHENTICATION → RESPONSE
```

Identifique pontos de entrada e testes úteis; não descreva a requisição.

## Árvores por vulnerabilidade

- **AUTH** → mecanismo de sessão fraco? token previsível? bypass lógico? default creds?
- **AUTORIZAÇÃO/IDOR** → o objeto/ID pertence ao usuário atual? trocar ID muda dados? há IDs sequenciais, GUIDs ou UUIDs?
- **SQLi** → input refletido/cego? SQLmap após mapear pontos de entrada; valide manualmente com payload simples antes de automatizar.
- **COMMAND INJECTION** → ponto que executa comandos? caracteres especiais sobrevivem? blind (timing/DNS)?
- **SSTI** → template detectável? `${7*7}` e congêneres em cada campo de renderização.
- **SSRF** → a app busca URLs? hosts internos/metadata acessíveis? filtro pode ser bypassado?
- **XXE** → parser XML? entidades externas habilitadas?
- **LFI/RFI/PATH TRAVERSAL** → input usado em paths? `../` tratado? wrappers/php filters?
- **FILE UPLOAD** → extensão validada onde (client/server/MIME)? conteúdo executado? path controlável?
- **DESERIALIZAÇÃO** → formato serializado (Java, PHP, Python, .NET)? gadget chain conhecida?
- **JWT** → alg none? segredo fraco? exp verificada? kid injection? confusão RS/HS?
- **OAuth/OIDC** → redirect_uri validado? state presente? code reuso? token no client?
- **GRAPHQL** → introspection? queries aninhadas sem limite? autorização por objeto?
- **RACE CONDITIONS** → operação não idempotente executável em paralelo?
- **BUSINESS LOGIC** → fluxo pode ser pulado? preços/limites manipuláveis client-side?

Ferramentas: Burp Suite, Caido, ffuf, feroxbuster, sqlmap, nuclei, curl, jq, httpx.

## Regra do playbook

Toda classe acima começa com hipótese e teste mínimo, não com scanner. Resultado de scanner é validado manualmente (AUTOMATION → FINDING → MANUAL VALIDATION).