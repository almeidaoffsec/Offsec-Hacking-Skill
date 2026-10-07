# Playbook — Windows Privilege Escalation

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Enumeração inicial

OBSERVAÇÃO: shell obtido em Windows
→ Coletar: identidade, grupos, token privileges, serviços, scheduled tasks, ACLs, credenciais armazenadas, registry, PowerShell history, configurações de instalação, permissões de arquivos, sessões existentes.

## Árvores de decisão

- **TOKEN PRIVILEGES** → SeImpersonate/SeAssignPrimaryToken? → potato family (JuicyPotato, GodPotato, etc. dependendo do build). SeBackup/SeRestore? → copiar arquivos privilegiados (SAM/SYSTEM, NTDS.dit via disk shadow). SeLoadDriver? → driver malicioso.
- **SERVIÇOS** → serviço roda como SYSTEM e o binário/caminho é gravável? → replace binário. → caminho com espaços sem quotes? → unquoted service path hijack. → ACL do serviço permite reconfiguração? → mudar binPath (com `sc` ou PowerShell quando permitido).
- **SCHEDULED TASKS** → tarefa como SYSTEM executando script/binário editável? tarefa em diretório gravável?
- **CREDENCIAIS** → DPAPI, credenciais salvas (cmdkey / mimikatz-style), arquivos de config com senhas, PowerShell history, unattend.xml, registry (Winlogon, autologon).
- **ACLs** → arquivos/diretórios do sistema graváveis pelo usuário atual? accesschk para varrer.
- **SESSÕES/GRUPOS** → membro de grupos com privilégio implícito (Backup Operators, Server Operators)?
- **ALWAYS INSTALL ELEVATED** → msiexec como SYSTEM via pacote MSI.

Ferramentas: WinPEAS, PowerUp, Seatbelt, accesschk; para contexto de domínio, SharpHound/BloodHound (ver `playbooks/ad.md`).

## Regra do playbook

Correlacione permissões com caminhos concretos de escalada — um SeImpersonate em um host com serviço HTTP acessível vale mais que dez misconfigurations genéricas.