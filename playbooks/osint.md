# Playbook — OSINT e Deep/Dark Web

Aplicar junto com CORE e ENGINE de `SKILL.md`. Árvore de decisão, não checklist.

## 1. OSINT de infraestrutura

OBSERVAÇÃO: escopo definido (domínio, organização, IP)
→ Infraestrutura e superfície técnica seguem `playbooks/network.md` (reconhecimento de superfície).
→ Este playbook cobre o que o network.md não cobre: pessoas, organizações e fontes não óbvias.

## 2. OSINT de pessoas e organizações

OBSERVAÇÃO: nomes, e-mails, usernames, funcionários, marca
→ PERGUNTA: o que essa identidade expõe publicamente e onde ela se repete?
→ Fontes conforme a hipótese:

- **E-mails e usernames:** padrões de nomenclatura corporativa (ex.: `primeiro.ultimo@`), reuso de username entre plataformas, contas abandonadas.
- **Breaches e leaks:** verificar se a identidade aparece em vazamentos conhecidos. Fontes: HaveIBeenPwned, DeHashed, Intelligence X, coleções de credenciais do operador.
- **Exposição organizacional:** vagas de emprego (stack técnica), LinkedIn/currículos (tecnologia e hierarquia), repositórios públicos (commits, e-mails em git logs), documentos com metadados, wayback de páginas corporativas.
- **Correlação de identidades:** mesmo username em plataformas diferentes, avatar reutilizado, padrão de escrita, datas/eventos consistentes.

→ Fluxo:

```text
IDENTIDADE ALVO
→ superfícies onde ela aparece
→ dado público vs. dado utilizável
→ hipótese (reuso, padrão corporativo, credencial vazada)
→ validação dentro do escopo autorizado
```

→ Regras: dado público não é dado utilizável — coleta sem propósito é ruído. Informação de vaga/LinkedIn/HBP envelhece rápido: marque como CONFIRMADA/INFERIDA/DESATUALIZADA antes de construir caminhos sobre ela. Validação de credencial vazada acontece **apenas contra ativos dentro do escopo autorizado**.

## 3. Deep/Dark web

OBSERVAÇÃO: suspeita de vazamento, busca de credenciais expostas, inteligência de ameaça sobre o alvo
→ PAPEL DA SKILL: **orientar e interpretar, não navegar.** A skill não acessa Tor nem forums; ela estrutura a busca, sugere fontes e categorias, e interpreta o que o operador colar.

→ Categorias de fontes (descreva categorias e métodos de busca; não invente instâncias):

- **Breach/paste sites:** onde credenciais vazadas aparecem primeiro (pastebin-like, coleções agregadas). Métodos: busca por domínio do alvo, por e-mail específico, por padrão corporativo.
- **Forums e stashes:** comunidades onde credenciais e acessos são negociados. Localização: diretórios/engines de busca onion (Ahmia e equivalentes), índices mantidos por pesquisadores.
- **Leak sites de ransomware/RaaS:** onde dados roubados são publicados quando a vítima não paga. Monitorar por nome da organização.
- **Marketplaces:** dados, acessos, bases. Estrutura de oferta (o que é vendido revela o que já foi comprometido).

→ REGRA ANTI-ALUCINAÇÃO: **nunca invente URLs `.onion`, nomes de forums, marketplaces ou handle de atores.** Instâncias específicas só existem se (a) fornecidas pelo operador, ou (b) verificáveis por ele na hora. Sem isso, descreva a categoria e o método de busca. Um `.onion` fabricado queima tempo do operador e contamina a investigação.

→ O que a skill interpreta quando o operador cola material de lá:

```text
MATERIAL COLADO (leak, post, oferta)
→ o que realmente contém (não o que anuncia)
→ identidades/credenciais envolvidas
→ janela temporal do vazamento (senha pode já estar rotacionada)
→ credibilidade da fonte (venda inflada é comum)
→ correlação com o Target State atual
```

→ Credencial vazada encontrada = CREDENCIAIS no Target State, origem "leak", validade **NÃO TESTADA** — mesma epistemia de credencial encontrada em backup. Encaminha para `playbooks/credentials.md`.

## 4. OPSEC do próprio OSINT

- Consultar breach databases com o e-mail do alvo pode gerar notificação ao dono da conta (HaveIBeenPowned notifica em alguns casos); avalie se alertar o alvo é aceitável na fase atual.
- Acesso a forums/leak sites de uma infraestrutura corporativa identifica você; use infraestrutura descartável/VPN.
- Não faça login em serviços com credenciais vazadas para "testar" fora do escopo — isso é uso ativo, não OSINT, e pertence à fase de exploração dentro das regras de engajamento.

## Regra do playbook

OSINT produz hipóteses e pontas, não findings. Toda credencial, identidade ou exposição coletada entra no Target State com origem, janela temporal e validade NÃO TESTADA — e só vira finding quando validada dentro do escopo autorizado.