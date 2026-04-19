# Principy počítačů – Základy

Tato kapitola shrnuje základní principy fungování počítače. Cílem je vytvořit dostatečný kontext pro pochopení dalších témat (reversing, exploitation, assembly apod.).

## Architektura CPU

Procesor je hlavní výpočetní jednotka počítače. Skládá se z několika základních částí:

### ALU (Arithmetic Logic Unit)

ALU provádí:

- aritmetické operace (sčítání, odčítání)
- logické operace (AND, OR, XOR)

### CU (Control Unit)

Řídicí jednotka:

- načítá instrukce z paměti
- dekóduje je
- řídí jejich vykonávání

### Registry

Malé, velmi rychlé paměti uvnitř CPU:

- obsahují aktuální data
- drží adresy a mezivýsledky

## Paměť (RAM vs ROM)

### RAM (Random Access Memory)

- volatilní (po vypnutí se smaže)
- ukládá běžící programy a data

### ROM (Read Only Memory)

- nevolatilní
- obsahuje firmware (např. BIOS)

## Schéma počítače

### Von Neumann architektura

- program i data ve stejné paměti
- jednodušší návrh
- může docházet ke „bottlenecku“ (jedna sběrnice)

### Harvard architektura

- oddělená paměť pro instrukce a data
- umožňuje paralelní přístup
- používá se např. v embedded systémech

## Dvojková soustava

Počítače pracují v binární (dvojkové) soustavě:

\\[
1010_2 = 10_{10}
\\]

Každý bit má hodnotu:

\\[
2^0, 2^1, 2^2, ...
\\]

## Šestnáctková soustava

Hexadecimální (základ 16) se používá pro přehlednější zápis:

\\[
0xFF = 255
\\]

Převod:

\\[
1111_2 = F_{16}
\\]

## Znaménková vs bezznaménková čísla

### Bezznaménková (unsigned)

\\[
0 \text{ až } 2^n - 1
\\]

### Znaménková (signed, two's complement)

Nejvyšší bit určuje znaménko.

Např. pro 8 bitů:

\\[
11111111 = -1
\\]

## Floating point (okrajově)

Reálná čísla jsou reprezentována pomocí IEEE 754:

\\[
\text{value} = (-1)^s \cdot 1.m \cdot 2^e
\\]

Používá se:

- mantisa (m)
- exponent (e)
- znaménko (s)

Pro reversing/exploitation není většinou klíčové, ale je dobré vědět, že reprezentace není přesná.

## Program a instrukce

### Program

Program je posloupnost instrukcí, které CPU vykonává.

### Instrukce

Instrukce je základní operace, například:

- načti data
- přičti
- skoč (jump)

Na nízké úrovni jsou instrukce reprezentovány jako binární kód.

## Programovací jazyky

### Vysokoúrovňové jazyky

Např.:

- C
- Python
- Rust
- Zig

Abstrakce nad hardwarem.

### Nízkourovňové jazyky

- assembly

Blízko hardware, přímá práce s registry a pamětí.

## Shrnutí

- CPU vykonává instrukce pomocí ALU a CU
- data jsou uložena v paměti (RAM)
- programy jsou posloupnosti instrukcí
- počítače pracují s binárními daty
- hex je pouze čitelnější reprezentace

Tyto základy jsou nutné pro pochopení toho, jak programy fungují na nízké úrovni.
