# Playbook — Active Directory e AD CS

Aplicar junto com CORE e ENGINE de `SKILL.md`. MODE: AD — o domínio é um grafo, não um conjunto de máquinas.

## Modelo central

```text
IDENTIDADE → GRUPO → ACL → SESSÃO → HOST → CREDENCIAL → PRIVILÉGIO
```

Quando receber dados de domínio, procure relações e caminhos, não análise isolada de hosts.

## Fluxo de decisão

OBSERVAÇÃO: usuário/máquina do domínio obtido
→ PERGUNTA: quais relações essa identidade possui?
→ Enumeração: usuários, grupos, computadores, sessões, shares, SPNs, trusts, ACLs, GPO permissions, service accounts.
→ BloodHound/SharpHound para caminhos até DA; ldapsearch/smbclient/NetExec para enumeração direcionada.

## Técnicas — árvore por hipótese

- **KERBEROASTING** → conta com SPN e senha fraca? crackeável offline; priorize service accounts.
- **AS-REP ROASTING** → contas sem pre-auth? crack offline barato.
- **ACL ABUSE** → WriteDacl/GenericWrite/GenericAll sobre usuário ou grupo? → adicionar self a grupo privilegiado, resetar senha de conta com acesso maior. bloodyAD/Impacket para executar.
- **DELEGATION** → unconstrained (TGT em cache, printers/RDP)? constrained (protocol transition, rbcd)? RBCD abuso com computer object controlável.
- **AD CS** → templates com ENROLLEE SUPPLIES SUBJECT / AUTHENTICATED USERS? ESC1-ESC8 conforme config. Certipy para enumerar e abusar.
- **CREDENTIAL REUSE** → senha local igual à de domínio? service account reutilizado entre hosts?
- **TRUSTS** → trust com outro domínio? confusão de confiança (sIDHistory, trust transitivity)?
- **SHARES/GPO** → share legível com configs/scripts com credenciais? GPO editável?
- **SESSÕES** → onde o admin tem sessão? → alvo de lateral movement (ver `playbooks/credentials.md`).

Ferramentas: NetExec, Impacket, BloodHound/SharpHound, Certipy, bloodyAD, ldapsearch, smbclient.

## Regra do playbook

O próximo objetivo é sempre um privilégio ou relação a validar, não "enumerar mais o domínio".