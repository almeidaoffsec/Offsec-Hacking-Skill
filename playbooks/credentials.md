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

- Reuso de credencial entre serviços é teste legítimo de primeira ordem em ambientes mal configurados — service accounts reutilizados entre banco, SSH e WinRM são achado clássico. Uma tentativa direcionada por serviço confirmado como acessível é o padrão; spray e listas não.
- Classifique o resultado da tentativa, não apenas sucesso/falha: "access denied" refuta a credencial naquele serviço e condições; timeout/reset é inconclusivo; 429 com Retry-After é limitação sinalizada — respeite o intervalo antes de repetir.
- Sucesso com fallback possível (ex.: guest já aceito no SMB) só confirma identidade após excluir o fallback; recusa permanece uma recusa nessas condições — não é "menos informativa" por existir fallback no sucesso.
- Não teste caminhos aleatórios quando relações de domínio ou enumeração permitirem priorização.
- Antes de crackear, pergunte: o material funciona diretamente em algum serviço? NetNTLM (v1/v2) é relayable antes de crackeável.
- Credencial nova atualiza automaticamente: IDENTIDADES, ACESSO e ATTACK PATHS do Target State.
- Correlacione com o que já existe: nova credencial + serviço + host conhecido pode completar um path já ativo.