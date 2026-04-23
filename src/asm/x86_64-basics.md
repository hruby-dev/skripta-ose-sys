# Základy x86_64

V této kapitole si projdeme základní stavební kameny programování v x86_64 assembly: **registry**, základní **instrukce** a **syscally** (systémová volání).

## Registry

Registr je malá paměť přímo v procesoru, která drží data, se kterými právě pracujete. Práce s registry je extrémně rychlá — mnohem rychlejší než práce s hlavní pamětí (RAM).

V x86_64 máme 16 základních obecných 64bitových registrů:

| 64-bit | 32-bit | 16-bit | 8-bit  | Typické použití                          |
|--------|--------|--------|--------|------------------------------------------|
| `rax`  | `eax`  | `ax`   | `al`   | Akumulátor, návratová hodnota funkcí     |
| `rbx`  | `ebx`  | `bx`   | `bl`   | Base register (volně použitelný)         |
| `rcx`  | `ecx`  | `cx`   | `cl`   | Counter, 4. argument syscallu            |
| `rdx`  | `edx`  | `dx`   | `dl`   | Data, 3. argument syscallu               |
| `rsi`  | `esi`  | `si`   | `sil`  | Source index, 2. argument syscallu       |
| `rdi`  | `edi`  | `di`   | `dil`  | Destination index, 1. argument syscallu  |
| `rbp`  | `ebp`  | `bp`   | `bpl`  | Base pointer (rámec zásobníku)           |
| `rsp`  | `esp`  | `sp`   | `spl`  | Stack pointer (vrchol zásobníku)         |
| `r8`–`r15` | `r8d`–`r15d` | `r8w`–`r15w` | `r8b`–`r15b` | Obecné registry (r10 = 4. arg syscallu) |

### Jak to s těmi velikostmi funguje?

Každý registr může být přistupován v různých velikostech. Tohle je historická záležitost z doby, kdy procesory byly 16bitové a pak 32bitové — novější, širší registry pohltily ty starší, úzké. Dnes se tedy nejedná o samostatné registry, ale o **různé pohledy na jeden a ten samý fyzický registr**.
 
Vezměme si třeba registr `rax`. Je 64bitový, tedy obsahuje 64 bitů (8 bajtů) dat. Když přistupujete přes jméno `eax`, pracujete jen se **spodními 32 bity** toho samého registru. Přes `ax` se dostanete ke **spodním 16 bitům**. A ještě níž — přes `al` pracujete s úplně **nejspodnějším bajtem** (bity 0 až 7), zatímco přes `ah` s bajtem nad ním (bity 8 až 15).
 
Představte si to jako matrjošku: `rax` obsahuje `eax`, `eax` obsahuje `ax`, a `ax` se skládá z `ah` (vyšší bajt) a `al` (nižší bajt).
 
Důsledek je, že když zapíšete `mov al, 5`, změní se jenom nejspodnější bajt — zbytek registru zůstane nedotčený. Jedna drobná výjimka: pokud zapisujete do 32bitové části (`eax`), procesor automaticky **vynuluje horních 32 bitů** `rax`. U 8bitových a 16bitových zápisů se to neděje. Tohle je typický zdroj bugů, na který si dejte pozor.


## Základní instrukce

### `mov` — přesun dat

Nejzákladnější instrukce. Zkopíruje hodnotu ze zdroje do cíle. V Intel syntaxi je to `mov cíl, zdroj`:

```asm
mov rax, 60          ; rax = 60 (okamžitá hodnota)
mov rbx, rax         ; rbx = rax (z registru do registru)
mov rcx, [rsi]       ; rcx = hodnota na adrese v rsi (čtení z paměti)
mov [rdi], rdx       ; do paměti na adrese rdi zapíše rdx (zápis)
```

### Aritmetické instrukce

```asm
add rax, 5           ; rax = rax + 5
sub rbx, rcx         ; rbx = rbx - rcx
inc rax              ; rax = rax + 1 (rychlejší než add rax, 1)
dec rbx              ; rbx = rbx - 1
mul rcx              ; rax = rax * rcx (beznaménkové)
neg rax              ; rax = -rax
```

### Logické a bitové operace

```asm
and rax, rbx         ; bitové AND
or  rax, rbx         ; bitové OR
xor rax, rax         ; rychlý způsob jak vynulovat rax!
shl rax, 3           ; posun doleva o 3 bity (= násobení 8)
shr rax, 1           ; posun doprava o 1 bit (= dělení 2)
```

>  **Tip:** `xor rax, rax` se používá místo `mov rax, 0`, protože je kratší ve strojovém kódu a procesory ji umí zpracovat ještě rychleji.

### Porovnání a skoky

```asm
cmp rax, 10          ; porovná rax s 10 (nastaví flagy)
je  label            ; skoč na label, pokud rovno (jump if equal)
jne label            ; skoč, pokud nerovno
jl  label            ; skoč, pokud menší (jump if less)
jg  label            ; skoč, pokud větší
jmp label            ; bezpodmínečný skok
```

### Zásobník (stack)

```asm
push rax             ; uloží rax na zásobník, rsp se zmenší o 8
pop  rbx             ; vyzvedne hodnotu do rbx, rsp se zvětší o 8
call funkce          ; zavolá funkci (uloží návratovou adresu)
ret                  ; návrat z funkce
```

## Syscally (systémová volání)

Když chce program udělat něco "venku" — vypsat text, otevřít soubor, přečíst ze sítě — musí požádat jádro (kernel) operačního systému. To se dělá pomocí **syscallu**.

Na Linuxu x86_64 to funguje takto:

1. Do `rax` dáte **číslo syscallu**
2. Do `rdi`, `rsi`, `rdx`, `r10`, `r8`, `r9` dáte **argumenty** (v tomto pořadí)
3. Zavoláte instrukci `syscall`
4. Výsledek najdete v `rax`

### Nejdůležitější syscally

| Číslo | Název   | Popis                          | Argumenty                        |
|-------|---------|--------------------------------|----------------------------------|
| 0     | `read`  | Čtení z file descriptoru       | fd, buffer, počet bajtů          |
| 1     | `write` | Zápis do file descriptoru      | fd, buffer, počet bajtů          |
| 2     | `open`  | Otevření souboru               | cesta, flags, mode               |
| 3     | `close` | Zavření file descriptoru       | fd                               |
| 60    | `exit`  | Ukončení programu              | návratový kód                    |

### File descriptory

Standardní file descriptory, které máte vždy k dispozici:

- `0` — **stdin** (standardní vstup, typicky klávesnice)
- `1` — **stdout** (standardní výstup, typicky terminál)
- `2` — **stderr** (chybový výstup)

### Příklad: ukončení programu s kódem 0

```asm
mov rax, 60          ; číslo syscallu "exit"
mov rdi, 0           ; návratový kód 0
syscall              ; provedení syscallu
```

### Příklad: zápis na obrazovku

```asm
mov rax, 1           ; syscall "write"
mov rdi, 1           ; fd = 1 (stdout)
mov rsi, zprava      ; adresa textu
mov rdx, 13          ; délka textu
syscall
```

> Úplný seznam syscallů najdete například na [filippo.io/linux-syscall-table](https://filippo.io/linux-syscall-table/) nebo v `man 2 syscalls`.

---

V další kapitole si tyto znalosti spojíme a napíšeme první reálný program!
