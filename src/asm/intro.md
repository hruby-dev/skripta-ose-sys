# Co je Assembler

**Assembly** (česky též *jazyk symbolických adres*) je nízkoúrovňový programovací jazyk, který je prakticky **přímým textovým zápisem strojových instrukcí** dané architektury procesoru. Každý řádek kódu v assembly typicky odpovídá jedné instrukci, kterou procesor přímo vykoná.

**Assembler** je potom nástroj (překladač), který tento textový kód přeloží na skutečné strojové instrukce — tedy binární podobu, které rozumí procesor.

## Proč se učit assembly?

I když dnes většinu kódu píšeme ve vyšších jazycích jako C, Rust nebo Python, znalost assembly je stále užitečná pro:

- **Pochopení, jak počítač skutečně pracuje** — co vlastně váš `for` cyklus dělá pod pokličkou
- **Reverse engineering** — analýzu malwaru, crackování, výzkum bezpečnosti
- **Optimalizaci kritického kódu** — například kryptografické knihovny, kodeky
- **Psaní operačních systémů a bootloaderů** — tam, kde vyšší jazyky ještě nefungují
- **Embedded programování** — mikrokontroléry s omezenou pamětí

## Různé architektury = různé Assemblery

Tady je důležité si uvědomit jednu klíčovou věc: **assembly není jeden jazyk**. Každá procesorová architektura má svou vlastní instrukční sadu (ISA — *Instruction Set Architecture*), a tedy i svůj vlastní dialekt assembly.

Mezi nejznámější patří:

### x86 / x86_64
Architektura od Intelu a AMD, kterou najdete ve většině stolních počítačů a notebooků. `x86` je 32bitová verze, `x86_64` (neboli `amd64`) je 64bitová moderní varianta. Má **CISC** architekturu — hodně instrukcí, některé i složité.

### ARM / ARM64 (AArch64)
Dominuje v mobilních zařízeních, používá ji Apple Silicon (M1, M2, M3...), Raspberry Pi a mnoho serverů. Je to **RISC** architektura — méně instrukcí, ale jednodušších a rychlejších.

### RISC-V
Moderní open-source architektura, která se používá ve výzkumu i rostoucím množství komerčních čipů. Také RISC.

### Další
Existují i MIPS, PowerPC, SPARC, AVR (Arduino), a historicky třeba 6502 (NES, Commodore 64) nebo Z80 (ZX Spectrum).

## Syntaxe: Intel vs. AT&T

I pro stejnou architekturu (například x86_64) existují dvě rozdílné syntaxe, jak instrukce zapisovat:

- **Intel syntaxe** — přehlednější, běžná na Windows, používá ji assembler NASM:
  ```asm
  mov rax, 60
  ```

- **AT&T syntaxe** — výchozí na Linuxu u nástrojů jako GAS (GNU Assembler), registry s `%`, operandy opačně:
  ```asm
  movq $60, %rax
  ```

V této knize budeme používat **Intel syntaxi** s assemblerem **NASM**, protože je čitelnější a na Linuxu snadno dostupná.

---

V další kapitole se podíváme na základy x86_64 — registry, instrukce a syscally.
