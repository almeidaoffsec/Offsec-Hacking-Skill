# Playbook — Linux Privilege Escalation

Aplicar junto com CORE e ENGINE de `SKILL.md`.

## Enumeração inicial

OBSERVAÇÃO: shell obtido em Linux
→ Coletar identidade, sistema e privilégios antes de qualquer hipótese:

```bash
id; whoami; groups
uname -a; cat /etc/os-release
sudo -l
find / -perm -4000 -type f 2>/dev/null
find / -perm -2000 -type f 2>/dev/null
getcap -r / 2>/dev/null
cat /etc/crontab
ps aux
systemctl
```

## Árvores de decisão

- **SUDO -L** → comando com GTFOBins entry? → sim: escalada direta. → não: sudo version vulnerável (CVE-2021-3156 etc.)? env_keep abusável?
- **SUID/SGID** → binário tem GTFOBins path? → sim: usar. → não: é script/binary custom chamando outro binário por PATH relativo? → PATH hijacking.
- **CAPABILITIES** → cap com impacto (cap_setuid, cap_dac_read_search, cap_sys_ptrace)? → validar exploit da capability.
- **CRON** → script rodando como root é editável? o diretório/PATH do cron é gravável? wildcards abusáveis?
- **PROCESSOS** → processo privilegiado com arquivo de config/script gravável? pspy para processos escondidos.
- **ARQUIVOS** → credenciais em configs, backups, history? permissões incorretas em arquivos sensíveis?
- **SERVIÇOS** → serviço editável → reconfigurar e reiniciar.
- **PATH/LIBRARY HIJACKING** → PATH gravável antes do diretório real? LD_PRELOAD/LD_LIBRARY_PATH com biblioteca gravável?
- **MOUNTS/NFS** → mounts no_root_squash? filesystems graváveis usados por processos privilegiados?
- **DOCKER/CONTAINERS** → usuário no grupo docker? socket montado? → ver `playbooks/containers.md`.
- **KERNEL** → apenas quando enumeração direcionada falhar: linux-exploit-suggester contra a versão do kernel.

Ferramentas auxiliares: linPEAS, pspy, linux-exploit-suggester, GTFOBins.

## Regra do playbook

Prefira enumeração direcionada antes de recomendar exploração de kernel. Kernel é a hipótese de maior custo e maior risco de crash — teste barato e local primeiro.