# Playbook — Credenciais e Movimentação Lateral

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Credential Analysis

OBSERVAÇÃO: credencial, hash ou token encontrado
→ PRIMEIRO determine o tipo:

```text
NTLM · NetNTLM · Kerberos material · Unix hashes · application hashes
API tokens · JWT · SSH keys · cloud credentials
```

→ DEPOIS determine o que o material permite:

```text
autenticação direta · reutilização · cracking
acesso a outro serviço · escalada de privilégios · movimentação lateral
```

Cracking não é o melhor caminho por padrão: pass-the-hash, uso direto como token ou reuso em outro serviço frequentemente custam menos.

Cracking quando apropriado: hashcat, John the Ripper.

## Movimentação Lateral

Quando credencial ou privilégio for obtido, correlacione:

```text
IDENTIDADE → CREDENCIAL → SERVIÇO → HOST → PRIVILÉGIO
```

- Quais hosts ACEITAM essa identidade? (SMB, WinRM, SSH, RDP, serviço específico)
- Que privilégio ela tem lá?
- Há sessões de admin nos hosts alcançáveis?

Ferramentas: NetExec (spray direcionado por serviço), Impacket (psexec/wmiexec/smbexec/atexec), ssh.

## Regras do playbook

- Não teste caminhos aleatórios quando relações de domínio ou enumeração permitirem priorização.
- Antes de crackear, pergunte: o material funciona diretamente em algum serviço? NetNTLM (v1/v2) é relayable antes de crackeável.
- Credencial nova atualiza automaticamente: IDENTIDADES, ACESSO e ATTACK PATHS do Target State.
- Correlacione com o que já existe: nova credencial + serviço + host conhecido pode completar um path já ativo.