# Playbook — Engenharia Reversa

Aplicar junto com CORE e ENGINE de `SKILL.md`. MODE: RE.

## Triagem inicial de binários

Antes da análise profunda, determine quando possível: formato, arquitetura, endianness, compilador provável, bibliotecas, símbolos, imports, exports, strings, proteções, packing, obfuscation.

Ferramentas: file, strings, checksec, readelf, objdump, nm, ldd, strace, ltrace; disassemblers: Ghidra, IDA, Binary Ninja, radare2, Cutter; debuggers: gdb, pwndbg, gef, x64dbg.

## Análise estática

Reconstrua:

```text
ENTRYPOINT → INITIALIZATION → INPUT → PARSING → VALIDATION → PROCESSING → SINK
```

Identifique especialmente: funções que recebem input controlável, operações de memória, alocações, cópias, parsing, comparações, transformações, chamadas indiretas, ponteiros de função, estruturas de dados, operações criptográficas, autenticação, tratamento de erros.

Ao receber pseudocódigo de Ghidra/IDA, ajude a: renomear variáveis, inferir tipos, reconstruir structs, identificar argumentos e calling conventions, simplificar expressões, reconstruir loops e condicionais, explicar fluxo de dados, identificar comportamento vulnerável.

Não traduza assembly linha por linha quando for possível reconstruir a lógica de alto nível. Converta gradualmente:

```text
ASSEMBLY → BASIC BLOCKS → CONTROL FLOW → PSEUDOCÓDIGO → COMPORTAMENTO
```

Ao analisar assembly, identifique primeiro: arquitetura, calling convention, prólogo/epílogo, argumentos, variáveis locais, branches, loops, chamadas, acessos à memória.

## Análise dinâmica

Confirme hipóteses estáticas com debugging:

```text
HIPÓTESE ESTÁTICA → BREAKPOINT → INPUT CONTROLADO → OBSERVAR ESTADO → CONFIRMAR/REFUTAR → ATUALIZAR MODELO
```

Considere: breakpoints, watchpoints, registradores, stack, heap, memória mapeada, chamadas, syscalls, argumentos, retornos. Com GDB/pwndbg/gef: registers, stack frames, backtrace, memory mappings, disassembly, conteúdo da stack, estado do heap.

## Data flow

Rastreie dados controlados pelo usuário:

```text
SOURCE → TRANSFORMAÇÕES → MEMORY OPERATIONS → SINK
```

Determine precisamente: qual dado é controlável, quantos bytes, quais restrições, onde termina, quais estruturas podem ser afetadas.

## Patch diffing

OLD VERSION → DIFF → CHANGED FUNCTIONS → SECURITY-RELEVANT CHANGE → ROOT CAUSE → TRIGGER. Ferramentas: BinDiff, Diaphora, Ghidra Version Tracking, source diff quando disponível. Não assuma que toda alteração entre versões é da vulnerabilidade.

## Regra do playbook

Quando o usuário enviar assembly/pseudocódigo/decompilação/registers/stack/hexdump, não descreva o conteúdo — reconstrua o comportamento e termine com o próximo breakpoint útil.