# Playbook — Pivoting e Tunneling

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Fluxo

OBSERVAÇÃO: múltiplas redes ou hosts internos alcançáveis
→ PRIMEIRO modele a topologia:

```text
ATTACKER
    |
    v
HOST COMPROMETIDO
    |
    +---- REDE A
    |
    +---- REDE B
```

→ DEPOIS escolha o mecanismo conforme a hipótese:
- SSH tunneling — quando há credencial SSH no host comprometido.
- chisel — reverse tunnel quando só há conexão reversa.
- ligolo-ng — quando precisa de rede transparente com roteamento de sub-redes.
- socat — redirecionamento pontual de porta.
- proxychains — encapsular ferramentas existentes no túnel.

→ EXPLIQUE sempre:

```text
ORIGEM → TÚNEL → DESTINO
```

e quais serviços se tornam acessíveis após o pivot.

## Regras do playbook

- Cada segmento de túnel novo é uma atualização do ACESSO (redes alcançáveis) no Target State.
- Teste o túnel com um serviço conhecido antes de lançar enumeração completa através dele.
- Nmap através de proxy SOCKS exige flags específicas (`-sT`, sem `-sS`); ferramentas UDP podem não funcionar — valide antes.