# Playbook — Reconhecimento, Rede e OSINT

Aplicar junto com CORE e ENGINE de `SKILL.md`. Árvore de decisão, não checklist.

## Reconhecimento de superfície

OBSERVAÇÃO: escopo definido (domínio, organização, IP, app)
→ PERGUNTA: o que já é publicamente exposto?
→ Coletar conforme a hipótese: DNS, subdomínios, vhosts, certificados, ASN, tecnologias, endpoints, metadados, repositórios, arquivos públicos, JavaScript, informações organizacionais.
→ Ferramentas: amass, subfinder, assetfinder, dnsx, httpx, gau, waybackurls, katana, ffuf, gobuster, feroxbuster, nuclei.
→ Só sugira a ferramenta que responde à hipótese atual, não a lista inteira.

OSINT: diferencie informação CONFIRMADA de INFERIDA de DESATUALIZADA. Não construa caminhos de ataque sobre dados históricos sem validação.

## Enumeração de rede

OBSERVAÇÃO: IPs/redes apresentados
→ PERGUNTA: descoberta e enumeração vêm antes de exploração.
→ Ferramentas: nmap, massscan, rustscan, netcat, curl, openssl, enum4linux-ng, smbclient, rpcclient, ldapsearch, snmpwalk.

Ao analisar Nmap, correlacione sempre:

```text
PORTA → SERVIÇO → VERSÃO → CONFIGURAÇÃO → HIPÓTESE → TESTE
```

Exemplo: 445/tcp → SMB → verificar dialect/signing → enumerar shares/domínio → procurar credenciais ou caminhos adicionais.

Não explique cada porta; determine qual serviço oferece maior ganho de enumeração no contexto e proponha o teste seguinte.

## Enumeração por serviço

### HTTP/HTTPS
Tecnologias, headers, redirects, cookies, vhosts, endpoints, APIs, JavaScript, arquivos históricos, diretórios, autenticação.

### SMB
Dialect → signing → shares → acesso guest → usuários/domínio → permissões.

Share legível → liste nomes e tamanhos → selecione arquivos conforme a hipótese (configs e backups, por exemplo) → defina o destino local → baixe seletivamente. Se estiver vazio ou o acesso falhar, registre o resultado antes de mudar de caminho. Extensão é pista, não garantia de conteúdo; evite `recurse ON` + `mget *` como coleta padrão.

Exemplo com share `backups` e `portal.zip` já identificados:

```bash
mkdir -p /tmp/opencode/<TARGET_IP>-backups
smbclient //<TARGET_IP>/backups -N
```

No prompt do `smbclient`:

```text
ls
lcd /tmp/opencode/<TARGET_IP>-backups
get portal.zip
exit
```

Substitua `<TARGET_IP>` nos dois contextos. Criar o diretório não muda o destino do download: `lcd` define o diretório local. Registre host, share, caminho remoto e destino da evidência; inspecione o arquivo selecionado antes de ampliar a coleta. Credenciais encontradas encaminham a investigação para `playbooks/credentials.md`, mantendo sua origem e validade ainda não testada.

### LDAP
Naming contexts → usuários → grupos → computadores → objetos → ACLs → informações de domínio.

### Kerberos
Enumeração de usuários → contas sem pre-auth → SPNs → políticas.

### SSH
Versão → métodos de autenticação → credenciais obtidas antes → chaves encontradas.

### SNMP
Sistema → interfaces → processos → software → configurações expostas.

Outros serviços com fluxo análogo: FTP, DNS, NFS, MSSQL, MySQL, PostgreSQL, WinRM, RDP.

## Regra do playbook

Sempre correlacione informações entre serviços diferentes. Um serviço frequentemente valida hipóteses geradas por outro.
