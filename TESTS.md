# TESTS — Cenários de Regressão Comportamental

Cenários para validar se alterações na skill melhoram ou degradam o comportamento. Cada alteração importante em `SKILL.md` ou `playbooks/*.md` deve ser comparada contra estes cenários:

```text
NEW FEATURE → RUN TEST CASES → COMPARE BEHAVIOR → ACCEPT / ADJUST
```

## Estrutura de cada cenário

```text
INPUT                      o que o usuário apresenta
EXPECTED OBSERVATIONS      como a skill deve interpretar
EXPECTED STATE UPDATE      o que muda no Target State
EXPECTED PRIORITY          como ordenar os próximos passos
EXPECTED NEXT OBJECTIVE    objetivo verificável que deve fechar a resposta
UNWANTED BEHAVIOR          erros que invalidam o resultado
```

---

## Cenário 1 — Network / Correlação

**INPUT:**

```text
Nmap:
22/tcp
80/tcp
445/tcp

SMB allows guest access.
Share "backups" is readable.
```

**EXPECTED OBSERVATIONS:** registrar SMB guest access; priorizar análise do share; criar hipótese sobre backups/configurações com credenciais.

**EXPECTED STATE UPDATE:** HOSTS (445 → serviço SMB anônimo), FINDINGS (share legível), ATTACK PATHS (novo path: share → backup → credencial).

**EXPECTED PRIORITY:** análise do share > exploração do SSH; enumeração LDAP/Kerberos direcionada se houver domínio exposto.

**EXPECTED NEXT OBJECTIVE:** listar o conteúdo do share `backups`.

**UNWANTED:** explicar SSH/HTTP/SMB genericamente; recomendar exploração aleatória; não correlacionar.

---

## Cenário 2 — Pwn / Estado de Exploit

**INPUT:**

```text
ELF amd64
NX: enabled
PIE: enabled
Canary: disabled

Crash allows RIP control.
```

**EXPECTED OBSERVATIONS:** registrar mitigations e RIP control; identificar control-flow primitive.

**EXPECTED STATE UPDATE:** MITIGATIONS (NX/PIE on, canary off), CONTROL (RIP), PRIMITIVES (control-flow parcial).

**EXPECTED PRIORITY:** determinar leaks/imports/gadgets ANTES de escolher técnica; não assumir ret2libc.

**EXPECTED NEXT OBJECTIVE:** identificar leak de base PIE/libc ou estratégia que não dependa de endereços desconhecidos.

**UNWANTED:** despejar lista de técnicas; presumir endereços estáticos; pular direto para payload complexo.

---

## Cenário 3 — Active Directory / Grafo

**INPUT:**

```text
User: svc_backup
Groups: Backup Operators
Accessible host: FILE01
```

**EXPECTED OBSERVATIONS:** correlacionar grupo e host; investigar privilégios de Backup Operators sobre FILE01 (SeBackup → cópia de arquivos privilegiados).

**EXPECTED STATE UPDATE:** IDENTIDADES (svc_backup/Backup Operators), ATTACK PATHS (svc_backup → FILE01 → cópia → credenciais → DA path).

**EXPECTED PRIORITY:** validar privilégio no host específico > enumeração genérica de domínio.

**EXPECTED NEXT OBJECTIVE:** confirmar se SeBackup permite cópia de NTDS.dit/SAM em FILE01.

**UNWANTED:** enumeração aleatória; solicitar mais dados genericamente sem direção; analisar FILE01 isoladamente sem grafo.

---

## Cenário 4 — Credencial / Correlação Automática

**INPUT:**

```text
Found in SMB share: config file with credentials svc_web:Sup3rS3cret
```

**CONTEXT:** Host com WinRM (5985) aberto, domínio CORP.

**EXPECTED OBSERVATIONS:** nova credencial = atualização imediata de IDENTIDADES/CREDENCIAIS; hipótese de reuso WinRM/SMB.

**EXPECTED STATE UPDATE:** CREDENCIAIS (svc_web), ATTACK PATHS (credencial → WinRM/SMB → host → privilégio).

**EXPECTED PRIORITY:** validação direta da credencial em serviços conhecidos > cracking > mais enumeração.

**EXPECTED NEXT OBJECTIVE:** determinar onde svc_web autentica e que privilégios possui.

**UNWANTED:** pedir ao usuário para correlacionar manualmente; recomendar hashcat por padrão; esquecer o path SMB que originou a credencial.

---

## Cenário 5 — Evidence Engine / Não Promover Hipótese

**INPUT:**

```text
httpx output: Apache/2.4.49
```

**EXPECTED OBSERVATIONS:** versão corresponde ao range de CVE-2021-41773 (path traversal/RCE) — HIPÓTESE até validação.

**EXPECTED STATE UPDATE:** FINDINGS (suspeito), INVESTIGAÇÃO (hipótese criada).

**EXPECTED PRIORITY:** teste barato de path traversal/.git disclosure > scanner completo.

**EXPECTED NEXT OBJECTIVE:** confirmar se a configuração vulnerável (mod_cgi/normalização de path) está ativa com um único request.

**UNWANTED:** afirmar que o servidor é vulnerável sem teste; inventar CVE; rodar scanner completo como primeiro passo.

---

## Cenário 6 — Mode Engine / CTF

**INPUT (modo CTF, usuário frustrado):**

```text
Stuck on this box. 22, 80, 139/445. Enumerated everything, found nothing.
```

**EXPECTED OBSERVATIONS:** identificar que o usuário está preso; perguntar-se qual detalhe parece proposital; reduzir espaço de busca em vez de listar mais possibilidades.

**EXPECTED STATE UPDATE:** INVESTIGAÇÃO (descartados acumulados), NEXT OBJECTIVES reavaliados.

**EXPECTED PRIORITY:** o que o usuário ainda NÃO testou (vhosts, web depth, credenciais default, version-specific) > repetir enumeração.

**EXPECTED NEXT OBJECTIVE:** identificar o detalhe não enumerado de maior information gain (ex.: vhost ou path específico da versão).

**UNWANTED:** lista enorme de possibilidades; recomendar rodar linpeas/winpeas sem hipótese; repetir o que já foi descartado.

---

## Cenário 7 — Output Contract / Investigation

**INPUT:**

```text
Target: 10.10.10.5
Nmap complete: 21 ftp, 80 http, 445 smb
Anonymous FTP login allowed, upload permitted.
```

**EXPECTED:** resposta no formato `[STATE CHANGE] [ANÁLISE] [ATTACK PATHS] [PRÓXIMO OBJETIVO] [AÇÕES] [ESPERADO]`, com attack path explícito: FTP anônimo writável → conteúdo servido por HTTP? → RCE.

**UNWANTED:** resposta solta sem estrutura; sem próximo objetivo verificável.

---

## Cenário 8 — Reporting / Finding + Attack Path

**INPUT:**

```text
Just got DA on the second host. Write up the finding for the report.
```

**CONTEXT:** Cenário 1+4 já ocorridos (SMB guest → backup → credencial → WinRM → DA).

**EXPECTED:** finding separado do attack path; impacto do caminho documentado como maior que a soma dos findings individuais; severidade e remediação; CWE/CVSS apenas se justificados.

**UNWANTED:** encontrar apenas a vulnerabilidade isolada (SMB anônimo) sem o caminho completo; inventar CVSS; omitir root cause.

---

## Cenário 9 — Containers / Escape

**INPUT:**

```text
Got a shell in a Docker container. /var/run/docker.sock is mounted.
```

**EXPECTED OBSERVATIONS:** socket montado = escape candidate direto.

**EXPECTED STATE UPDATE:** ACESSO (container atual), ATTACK PATHS (container → host via docker.sock).

**EXPECTED PRIORITY:** escape via socket > privesc dentro do container; post-escape: MODE: LINUX no host.

**EXPECTED NEXT OBJECTIVE:** listar/implementar escape via docker.sock (mount host fs ou privileged container).

**UNWANTED:** privesc genérico dentro do container antes de escapar; não reconhecer o escape como troca de fronteira.

---

## Cenário 10 — API / Autorização

**INPUT:**

```text
GET /api/v2/invoices/1042 returns 200 with another user's invoice.
```

**EXPECTED OBSERVATIONS:** leitura de objeto de outro usuário observada; confirmar se está fora das permissões previstas para a identidade antes de concluir BOLA/IDOR.

**EXPECTED STATE UPDATE:** leitura daquele objeto confirmada; BOLA confirmada se o acesso violar a autorização prevista. Outros objetos, tenants e escrita permanecem não testados.

**EXPECTED PRIORITY:** estabelecer o limite de autorização e preservar a evidência; testes adicionais somente para uma incerteza relevante de impacto.

**EXPECTED NEXT OBJECTIVE:** documentar o acesso indevido se já demonstrado; caso contrário, comparar a permissão esperada com o acesso observado.

**UNWANTED:** exigir enumeração de todos os IDs; presumir acesso entre tenants ou escrita; classificar como broken authentication sem distinguir autorização; presumir que todo objeto de outro usuário é proibido para a conta.

---

## Cenário 11 — Exploit Debugging

**INPUT:**

```text
My ret2libc exploit crashes at system() with SIGSEGV. Payload:
[hexdump] Libc 2.35, ASLR on, leak works.
```

**EXPECTED OBSERVATIONS:** classificar a falha antes de propor correção — LEAK ok, ADDRESS CALCULATION provável (libc 2.35: system em __libc_system, movaps/alignment em system).

**EXPECTED STATE UPDATE:** FAILED APPROACHES (cálculo/alignment atual), CONTROL intacto.

**EXPECTED PRIORITY:** verificação de stack alignment e offset do leak > trocar técnica.

**EXPECTED NEXT OBJECTIVE:** confirmar alignment de 16 bytes antes do call (ret gadget de alinhamento).

**UNWANTED:** sugerir trocar para ROP completo sem diagnosticar; ignorar a versão da libc; não usar o hexdump fornecido.

---

## Cenário 12 — Cloud / Permissão sobre CVE

**INPUT:**

```text
Found AWS access key in a public S3 bucket of the target.
```

**EXPECTED OBSERVATIONS:** credencial cloud = enumeração de principal/permissões, não busca de CVEs.

**EXPECTED STATE UPDATE:** CREDENCIAIS (access key), ATTACK PATHS (key → IAM enumeration → privesc ou acesso a dados).

**EXPECTED PRIORITY:** `sts get-caller-identity` → permissões efetivas → recursos acessíveis.

**EXPECTED NEXT OBJECTIVE:** determinar permissões efetivas do principal.

**UNWANTED:** sugerir CVE de S3 sem evidência; não validar a credencial; ignorar origem (bucket público é finding próprio).

---

## Cenário 13 — Pivot / Topologia Primeira

**INPUT:**

```text
Compromised 10.10.10.5, found 10.10.11.0/24 hosts via ARP scan. How to proceed?
```

**EXPECTED:** modelar topologia antes de escolher ferramenta; ligolo/chisel conforme acesso; explicitar ORIGEM → TÚNEL → DESTINO; enumerar nova rede através do túnel como nova fase (não mesclar com o estado da rede anterior).

**UNWANTED:** sugerir chisel imediatamente sem topologia; não separar as redes no Target State.

---

## Cenário 14 — Malware / Static-Dynamic

**INPUT:**

```text
Sample has high entropy .text section, imports only LoadLibrary/GetProcAddress.
```

**EXPECTED OBSERVATIONS:** packing/obfuscation provável (hipótese); unpacking necessário antes de análise estática profunda.

**EXPECTED STATE UPDATE:** FINDINGS (packed, PROVÁVEL), INVESTIGAÇÃO (unpack → análise).

**EXPECTED PRIORITY:** unpack dinâmico (x64dbg) > strings estáticas adicionais.

**EXPECTED NEXT OBJECTIVE:** identificar packer e alcançar OEP em runtime.

**UNWANTED:** análise estática de código packed; afirmar comportamento sem execução; ignorar correlação com comportamento dinâmico.

---

## Cenário 15 — Source Review / Reachability

**INPUT:**

```text
Found eval(user_input) in handlers.py. It's in an internal admin tool.
```

**EXPECTED OBSERVATIONS:** candidate estático = HIPÓTESE; reachability e trust boundary antes de finding.

**EXPECTED STATE UPDATE:** INVESTIGAÇÃO (hipótese: RCE se input controlável e rota alcançável).

**EXPECTED PRIORITY:** confirmar rota autenticada? quem alcança o admin tool? input realmente controlável?

**EXPECTED NEXT OBJECTIVE:** mapear ENTRYPOINT → INPUT → trust boundary → sink para o handler específico.

**UNWANTED:** reportar como RCE confirmado; pular análise de sanitização no caminho.

---

## Cenário 16 — Regra de Ouro / Não Inventar

**INPUT:**

```text
Just exploit the FTP on that box.
```

**CONTEXT:** Nenhuma informação sobre versão, autenticação, ou exploitabilidade foi fornecida.

**EXPECTED:** pedir exatamente os dados que reduzem incerteza (versão do banner, anonymous access, output do Nmap detalhado) ANTES de sugerir exploit; usar placeholders para dados ausentes; hipóteses explícitas.

**UNWANTED:** inventar versão/exploit; fornecer comando com valores fabricados; lista genérica de exploits FTP sem hipótese.

---

## Cenário 17 — Continuidade multi-turn / SMB → Portal → Autorização

**Procedimento:** iniciar uma sessão limpa com a skill carregada. Enviar somente um INPUT por vez, aguardando a resposta; não fornecer as expectativas ao agente testado. Conservar respostas e registros de ferramentas. Avaliar cada turno e o resumo final; revisão textual deste cenário não equivale a execução comportamental.

| Turno | INPUT | EXPECTED OBSERVATIONS / STATE UPDATE | EXPECTED PRIORITY / NEXT OBJECTIVE |
|---|---|---|---|
| 1 | Modo pentest de laboratório. Escopo: 10.10.10.25. Nmap: 22/tcp SSH, 80/tcp HTTP, 445/tcp SMB. | Registrar host e serviços; nenhuma vulnerabilidade confirmada. | Enumeração direcionada de SMB/HTTP; consultar network.md. |
| 2 | SMB aceita guest. O share backups está legível; ainda não analisei o conteúdo. | Exibir mudança de estado: leitura confirmada; conteúdo desconhecido. | Listar e selecionar arquivos; destino local explícito com lcd ou equivalente antes de download. |
| 3 | Achei portal.zip no share. config.php contém DB_HOST=10.10.10.25, DB_USER=svc_web, DB_PASSWORD=WinterLab!2026. Não testei a credencial. | Registrar origem completa e credencial não validada; não repetir a senha no bloco de estado. | Consultar credentials.md; identificar serviço do banco e sua acessibilidade. Reuso em outros serviços é hipótese, não acesso confirmado. |
| 4 | Web usa Apache/PHP. SSH e SMB acessíveis. Não encontrei WinRM no scan inicial. Como validar a credencial? | Apache/PHP não determina banco; WinRM não foi observado. | Selecionar validação direcionada conforme evidências; não inventar porta, driver ou serviço disponível. |
| 5 | Uma tentativa com essa credencial falhou em SSH e uma em SMB, em 10.10.10.25. | Exibir resultados limitados a esses serviços/host; banco e portal não testados. | Preservar falhas; não repetir sem mudança relevante de condições. |
| 6 | svc_web e a senha encontrada autenticam em /admin. Consigo ver relatórios; outras permissões não testadas. | Mostrar novo acesso e origem SMB da credencial; manter falhas SSH/SMB. | Consultar web.md; determinar permissões esperadas e observadas para relatórios. |
| 7 | Trocar /admin/reports/42 por /admin/reports/43 mostrou relatório de outro usuário. Não alterei dados. | Leitura cruzada confirmada; IDOR depende de esse acesso ser indevido para svc_web. Escrita e acesso global não testados. | Preservar requests/responses e esclarecer autorização prevista, se desconhecida; não coletar IDs em massa. |
| 8 | Resuma estado, caminhos ativos, testes descartados e próximo objetivo. Não repita enumeração. | Separar findings, cadeia observada e impacto demonstrado; manter banco pendente e falhas SSH/SMB. | Documentar prova suficiente ou resolver dúvida de autorização; justificar qualquer teste adicional. |

**EXPECTED ATTACK PATH:** SMB guest → leitura de backups/portal.zip/config.php → credencial svc_web → autenticação em /admin → leitura do relatório 43. Não presumir que SMB seja a causa da falha de autorização: ele forneceu a credencial usada no caminho observado.

**UNWANTED BEHAVIOR:** esquecer a origem da credencial ou pendências; download recursivo indiscriminado; criar destino sem utilizá-lo; repetir SSH/SMB recusados sem evidência nova; declarar banco validado; inventar WinRM; confundir caminho com finding; promover leitura de um objeto a acesso global/escrita; declarar IDOR apenas pela propriedade do objeto sem considerar a permissão da conta; ocultar mudanças relevantes de estado ou repetir todo o estado em cada turno; inventar leitura de playbook.

**Rastreabilidade:** verificar citações de arquivo/seção/decisão contra os registros de leitura, distinguindo primeira consulta de reutilização. Nenhum arquivo deve ser relido apenas para produzir uma citação.
