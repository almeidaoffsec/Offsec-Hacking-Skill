# Playbook — Pwn e Exploit Development

Aplicar junto com CORE e ENGINE de `SKILL.md`. MODE: PWN.

## Fluxo dominante (nunca pule etapas)

```text
CRASH → ROOT CAUSE → CONTROL → PRIMITIVE → MITIGATIONS → STRATEGY → PoC → DEBUG → RELIABILITY
```

Nunca trate CRASH → SHELL como uma única etapa. Identifique qual primitive foi realmente conquistada antes de escolher a próxima técnica.

## Metodologia para pwn

1. identificar arquivo e arquitetura;
2. verificar mitigations;
3. executar e entender interface;
4. analisar strings/imports;
5. localizar parsing/input;
6. analisar função vulnerável;
7. reproduzir bug;
8. determinar offset/controle;
9. identificar primitive;
10. avaliar mitigations;
11. escolher estratégia;
12. desenvolver PoC;
13. validar localmente;
14. adaptar ao ambiente remoto.

Ao final de cada etapa: qual evidência confirma a hipótese atual?

## Mitigations (verificar antes da estratégia)

Linux/ELF: NX, PIE, ASLR, stack canaries, RELRO, CET quando aplicável.
Windows: DEP, ASLR, CFG, stack cookies, SafeSEH/SEHOP quando aplicável.

## Análise de crashes

1. identificar instrução responsável;
2. registradores relevantes;
3. origem dos valores;
4. existe controle?
5. offset;
6. impacto.

Diferencie CRASH de CONTROLLED CRASH de EXPLOITABLE PRIMITIVE — um crash sozinho não significa exploração.

## Determinação de controle

Descubra o que é controlável: instruction pointer, return address, stack, heap metadata, function pointers, arguments, pointers, indexes, lengths.

Padrões cíclicos para offsets: pwntools cyclic, pattern_create/pattern_offset, gdb/pwndbg/gef.

## Estratégia por hipótese

- **RET2WIN** → há win/gadget de execução no binário? menor custo.
- **RET2LIBC** → NX on + funções importadas? CONTROL FLOW → ENDEREÇOS → BASE → RESOLVER FUNÇÕES → ARGUMENTOS → TRANSFERIR CONTROLE. ASLR/PIE decidem se leak é necessário; não presuma endereços estáticos.
- **ROP** → trate a cadeia como chamadas de função reconstruídas: gadgets, calling convention, stack alignment, argumentos, side effects, stack consumption. ROPgadget, ropper, pwntools, rp++. Prefira gadgets simples; valide cada estágio antes de chains grandes.
- **STACK PIVOT** → controle limitado de stack? mov rsp ou leave;ret?
- **GOT/PLT ABUSE** → RELRO parcial?
- **FORMAT STRING** → input controla o format string? posição dos argumentos? modele READ e WRITE separadamente; não trate como controle de execução automático.
- **INFORMATION LEAK** → leak de stack/heap/base/libc/pointer/canary? LEAK → QUAL ENDEREÇO? → QUAL MÓDULO? → QUAL OFFSET? → QUAL BASE CALCULÁVEL?
- **HEAP** → determine allocator, versão, padrão de allocations, chunks, sequência alloc/free, objeto alvo, primitive. ALLOC → FREE → REALLOC → CORRUPÇÃO → PRIMITIVE. UAF, double free, overlapping chunks, metadata corruption, freelist manipulation, object replacement. Comportamento depende fortemente da versão — não aplique técnicas antigas sem confirmar ambiente.
- **FUNCTION-POINTER OVERWRITE** → vtables, callbacks, exit handlers.

## Estado de exploit development

Durante sessão longa, mantenha: TARGET (arquitetura/sistema/bibliotecas), MITIGATIONS, BUG (root cause), CONTROL (dados/registradores), PRIMITIVES (read/write/control-flow/leak), OFFSETS, ADDRESSES (bases/símbolos), FAILED APPROACHES, NEXT OBJECTIVE (menor objetivo necessário). Isso evita reconstruir o exploit a cada interação.

## Exploits com pwntools

Estrutura: SETUP → CONNECTION → PAYLOAD → LEAK/PARSE → ADDRESS CALCULATION → SECOND STAGE → VALIDATION.

Use pwntools para: ELF parsing, packing, comunicação, cyclic patterns, ROP, símbolos, debugging, execução local/remota. Exponha valores intermediários (offsets, leaks, bases). Evite scripts opacos.

## Reliability

Depois da PoC funcional, investigue dependências: ASLR, timing, heap state, environment, versão de biblioteca, offsets, tamanho do input, conexão, parsing. Transforme FUNCIONA UMA VEZ em COMPORTAMENTO REPRODUZÍVEL.

## Exploit Debugging

Quando o exploit falhar, classifique a falha: TRIGGER? CONTROL? LEAK? ADDRESS CALCULATION? ROP/CONTROL-FLOW? ENVIRONMENT DIFFERENCE? PROTOCOL/PARSING? Solicite apenas os dados necessários para distinguir: registradores no crash, backtrace, mappings, checksec, versão da libc, disassembly da função, output do exploit, hexdump do payload.