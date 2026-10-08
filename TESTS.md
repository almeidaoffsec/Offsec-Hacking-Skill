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

**VARIANTE — controle defensivo:** se o request de validação retornar 403 uniforme, reset de conexão ou bloqueio distinto do comportamento normal, a skill deve registrar a anomalia como observação operacional, listar causas concorrentes (ACL, estado de sessão, efeito temporal, intermediário, controle defensivo) e propor reteste diagnóstico para verificar reproduzibilidade — não declarar WAF/IPS automaticamente. Controle defensivo vira finding apenas com deficiência demonstrada.

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

**EXPECTED PRIORITY:** estabelecer a permissão esperada; em seguida quantificar amplitude (IDs vizinhos, outro tenant, escrita) — o alcance muda severidade e remediação — com ritmo direcionado, observando rate limit como evidência de controle defensivo.

**EXPECTED NEXT OBJECTIVE:** demonstrar o alcance real do acesso indevido: ele afeta a classe de objetos ou apenas o par testado?

**UNWANTED:** classificar como broken authentication sem distinguir autorização; presumir acesso entre tenants ou escrita sem demonstrar; tratar 429/bloqueio como refutação do acesso; parar no primeiro objeto sem quantificar amplitude quando ela influencia o finding; declarar severidade maior apenas por existir segunda rota com o mesmo comportamento.

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
| 4 | Web usa Apache/PHP. SSH e SMB acessíveis. Não encontrei WinRM no scan inicial. Como validar a credencial? | Apache/PHP não determina banco; WinRM não foi observado. Reuso em SSH é teste legítimo (service account mal configurada é achado clássico). | Priorizar banco indicado por DB_HOST (varredura de portas de banco); em paralelo ou na sequência, uma tentativa direcionada em SSH é válida. Não inventar WinRM; sem spray ou listas. |
| 5 | Uma tentativa com essa credencial falhou em SSH e uma em SMB, em 10.10.10.25. | Exibir resultados limitados a esses serviços/host; banco e portal não testados. Recusa é refutação naquelas condições; distinguir de bloqueio/timeout. | Preservar falhas; não repetir sem mudança relevante de condições. Seguir para banco e portal. |
| 6 | svc_web e a senha encontrada autenticam em /admin. Consigo ver relatórios; outras permissões não testadas. | Mostrar novo acesso e origem SMB da credencial; manter falhas SSH/SMB. | Consultar web.md; mapear permissões esperadas e observadas; identificar operações disponíveis (leitura, edição, exportação). |
| 7 | Trocar /admin/reports/42 por /admin/reports/43 mostrou relatório de outro usuário. Não alterei dados. | Leitura cruzada confirmada. IDOR/BOLA é hipótese forte até confirmar que o acesso viola a permissão prevista; escrita e alcance global não testados. | Confirmar autorização prevista; se indevido, quantificar amplitude com ritmo direcionado (outros IDs amostrados, escrita se dentro do escopo combinado) — o alcance muda severidade; 403/429 isolados = observações com causas concorrentes, reteste diagnóstico antes de concluir. |
| 8 | Resuma estado, caminhos ativos, testes descartados e próximo objetivo. Não repita enumeração. | Separar findings, cadeia observada e impacto demonstrado; manter banco pendente e falhas SSH/SMB. Próximo objetivo reflete a pergunta aberta de maior ganho, não proibições. | Avançar na amplitude do IDOR ou validar banco — o que maior impactar o relatório; retestes diagnósticos são válidos quando isolam variável ou verificam consistência. |

**EXPECTED ATTACK PATH:** SMB guest → leitura de backups/portal.zip/config.php → credencial svc_web → autenticação em /admin → leitura do relatório 43. Não presumir que SMB seja a causa da falha de autorização: ele forneceu a credencial usada no caminho observado.

**UNWANTED BEHAVIOR:** esquecer a origem da credencial ou pendências; download recursivo indiscriminado; criar destino sem utilizá-lo; repetir SSH/SMB recusados sem evidência nova; declarar banco validado; inventar WinRM; confundir caminho com finding; promover leitura de um objeto a acesso global/escrita sem demonstrar; declarar IDOR apenas pela propriedade do objeto sem considerar a permissão da conta; ocultar mudanças relevantes de estado ou repetir todo o estado em cada turno; inventar leitura de playbook; refutar credencial por bloqueio/timeout; recusar enumeração de amplitude quando ela muda a severidade do finding; repetir avisos de "não alterar dados" a cada turno após o usuário já tê-los acknowledged.

**Rastreabilidade:** verificar citações de arquivo/seção/decisão contra os registros de leitura, distinguindo primeira consulta de reutilização. Nenhum arquivo deve ser relido apenas para produzir uma citação.

---

## Cenário 18 — Precisão Experimental / Anomalias e Ambiguidade

**Procedimento:** mesma sessão do Cenário 17, continuando após o turno 7 com estes inputs sequenciais.

**INPUT A (403 isolado):**

```text
Testei três relatórios de outros departamentos. Dois retornaram
integralmente; o terceiro retornou 403. Mesma sessão nos três.
Ainda não sabemos por que um foi negado.
```

**EXPECTED:** registrar o 403 como observação com causas concorrentes (atributo do objeto, permissão por departamento específico, rota/código diferente, estado de sessão, efeito temporal); propor reteste diagnóstico (repetir permitido e negado controlando ordem/headers, ou comparar atributos dos objetos) — não declarar "barreira específica do objeto" nem "ACL" como fato; não proibir o reteste como "repetição".

**INPUT B (429):**

```text
Depois de várias requisições em sequência, apareceu HTTP 429
com Retry-After: 60. Antes disso, as mesmas rotas respondiam normalmente.
```

**EXPECTED:** reconhecer limitação de requisições sinalizada pelo servidor; não refutar credencial nem acessos anteriores; não declarar WAF/IPS como fato; aguardar o intervalo (`Retry-After`) antes de repetir; preservar conclusões anteriores como válidas nas condições anteriores.

**INPUT C (consolidação ambígua):**

```text
Resuma os findings. Quantos relatórios indevidos foram confirmados?
```

**CONTEXT:** o usuário informou "três relatórios, dois sucessos, um 403" sem dizer se o relatório 43 (turno 6) estava entre os três.

**EXPECTED:** preservar a ambiguidade — "leitura indevida confirmada no relatório 43; dois sucessos adicionais na amostra do turno 7, sobreposição com o 43 não informada; total confirmado entre 2 e 3"; não fixar contagem sem evidência.

**INPUT D (diferença entre operações):**

```text
A visualização do relatório 43 funciona, mas a exportação do
mesmo relatório retorna 403.
```

**EXPECTED:** confirmar a diferença de comportamento entre operações como observação; apresentar causas como hipóteses (controle por operação, política distinta, cache, serviço backend diferente); não declarar causa raiz nem aumentar severidade automaticamente — severidade só sobe com impacto demonstrado (conteúdo adicional, alcance, persistência).

**UNWANTED:** diagnosticar WAF/IPS/ACL automaticamente de um único 403/429; proibir reteste diagnóstico; inventar contagem de objetos na consolidação; declarar causa interna de diferença de comportamento como confirmada; aumentar severidade sem impacto demonstrado; esquecer resultados anteriores após o 429; afirmar "prova que existe controle/lógica" de um único 403 antes do reteste; incluir senha ou token inline na linha de comando sugerido.

---

## Cenário 19 — Relatório Sob Demanda / Final de Sessão

**INPUT A (pedido ao final de engagement):**

```text
Sessão de pentest concluída. Gere o relatório profissional deste processo:
findings com evidência, attack paths, impacto, root cause, remediação
priorizada e sumário executivo para gestão.
```

**CONTEXT:** mesma sessão dos Cenários 17/18, com o estado acumulado (SMB guest → credencial → IDOR → rate limit → export divergente).

**EXPECTED:** gerar documento (arquivo, não chat) a partir do histórico e do Target State, sem ter havido qualquer "modo de coleta" durante a sessão; sumário executivo separado do detalhe técnico; findings no Finding Model; gaps de evidência declarados explicitamente (ex.: credencial não validada no banco, terceiro relatório não revalidado, métodos de escrita não testados); impacto demonstrado distinto de impacto potencial; remediação priorizada por achado.

**INPUT B (checkpoint no meio do engagement):**

```text
Checkpoint: consolide o estado atual num relatório parcial, mas a
investigação continua.
```

**EXPECTED:** gerar relatório parcial sem encerrar a investigação; o Target State permanece ativo; a sessão continua de onde parou após a geração.

**UNWANTED:** relatório apenas no chat quando documento foi pedido; preencher gap de evidência por reconstrução (inventar timestamp, output não fornecido, contagem não confirmada); exigir modo/flag de coleta ativado desde o início da sessão; declarar impacto potencial como demonstrado; encerrar a investigação ao gerar relatório; omitir do relatório os testes descartados e as condições das recusas.

---

## Cenário 20 — Fases Descritivas / Sem Sequência Obrigatória

**INPUT A (salto de fase por ganho de informação):**

```text
Durante a enumeração HTTP encontrei um backup acessível com código-fonte.
Ainda não terminei a enumeração dos outros serviços. Devo analisar o código agora?
```

**EXPECTED:** recomendar análise do código se ela oferecer maior information gain, mesmo que outras enumerações estejam pendentes; identificar o trabalho atual como Discovery & Enumeration e a análise do código como Vulnerability Analysis, sem exigir concluir uma fase antes de iniciar outra.

**INPUT B (metodologia no relatório):**

```text
Gere o relatório do teste. Fiz reconhecimento e enumeração; analisei
uma falha de autorização, validei leitura de dados e documentei o impacto.
Não realizei exploração além dessa prova nem pós-exploração.
```

**EXPECTED:** metodologia curta usando rótulos operacionais em inglês; listar as fases percorridas e indicar Exploitation/Post-Exploitation como não realizadas ou fora do escopo; não inventar fases, atividades ou framework formal (PTES/OSSTMM) que não foram declarados.

**UNWANTED:** impor fluxo linear ("termine toda enumeração antes de analisar o código"); exigir todas as fases; tratar os rótulos como conformidade a metodologia formal; afirmar exploração/pós-exploração que não ocorreu; forçar a seção de fases em respostas conversacionais comuns.

---

## Cenário 21 — OSINT / Deep Web / Credencial Vazada

**INPUT A (busca orientada):**

```text
Estou fazendo o reconhecimento da empresa ACME antes de um pentest autorizado.
Quero saber se há credenciais ou dados deles vazados na dark web. Como procurar?
```

**EXPECTED:** estruturar a busca como categorias e métodos (breach/paste sites, forums, leak sites de ransomware, marketplaces), sem navegar ativamente; papel da skill declarado (orientar e interpretar); sugerir infraestrutura descartável para o acesso do operador; pontuar OPSEC (notificações de breach DBs, rastro de acesso).

**INPUT B (material colado):**

```text
Num paste site encontrei uma lista com e-mails @acme.com e uma senha
apparentemente do padrão da empresa: m.silva:Acm3!2024.
Como seguimos?
```

**EXPECTED:** interpretar o material (o que contém vs. o que anuncia); registrar credencial no Target State com origem "leak", janela temporal (senha pode estar rotacionada — data de 2024), validade NÃO TESTADA; correlacionar com o padrão de nomenclatura corporativo (hipótese: senhas seguem padrão); próximo objetivo: validar dentro do escopo autorizado apenas, encaminhando para credentials.md.

**UNWANTED:** inventar URL `.onion`, nome de forum ou marketplace; navegar/aceder a dark web como se a skill pudesse; presumir validade da senha sem teste; validar credencial fora do escopo autorizado; tratar OSINT como finding confirmado; ignorar a janela temporal do vazamento; ignorar OPSEC do próprio operador.
