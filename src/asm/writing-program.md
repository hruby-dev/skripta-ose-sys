# Jak napsat program v Assembly

V této kapitole si postupně napíšeme, zkompilujeme a spustíme náš první program v assembly — klasický **Hello, World!** — a následně ho rozšíříme o **jednoduchou smyčku**.

## Co budeme potřebovat

Na Linuxu (apt): `sudo apt install nasm binutils` .

Na Arch Linuxu: `sudo pacman -S nasm binutils`.

Ověřte instalaci:

```bash
nasm --version
ld --version
```

## Struktura programu

Každý NASM program pro Linux se skládá ze tří hlavních sekcí:

- **`.data`** — inicializovaná data (řetězce, konstanty, čísla)
- **`.bss`** — neinicializovaná data (buffery, proměnné bez počáteční hodnoty)
- **`.text`** — samotný kód programu

A pak potřebujeme **vstupní bod** — symbol `_start`, který říká operačnímu systému, kde program začíná.

## Hello, World! — krok za krokem

### Krok 1: Vytvořme si datovou sekci

Začneme textem, který chceme vypsat:

```asm
section .data
    message: db "Hello, World!", 10
    message_len: equ $ - message
```

Co to znamená?

- `db` = *define bytes*, definuje sérii bajtů
- `"Hello, World!"` = samotný text
- `10` = ASCII kód znaku nového řádku (`\n`)
- `equ $ - message` = konstanta rovnající se *aktuální adresa (`$`) mínus začátek message*, tedy délka řetězce. NASM to spočítá při překladu.

### Krok 2: Přidáme textovou sekci a vstupní bod

```asm
section .text
    global _start

_start:
```

- `global _start` říká linkeru, že symbol `_start` je viditelný zvenčí
- `_start:` je návěští (label) — místo, kam operační systém po spuštění skočí

### Krok 3: Zavoláme `write` syscall

Chceme vypsat text na stdout (fd = 1):

```asm
    mov rax, 1              ; syscall číslo 1 = write
    mov rdi, 1              ; fd = 1 = stdout
    mov rsi, message        ; adresa textu
    mov rdx, message_len    ; délka textu
    syscall
```

### Krok 4: Ukončíme program

Bez explicitního ukončení by program spadl. Zavoláme `exit`:

```asm
    mov rax, 60             ; syscall číslo 60 = exit
    mov rdi, 0              ; návratový kód = 0
    syscall
```

### Kompletní program

Uložte si tento soubor jako `hello.asm`:

```asm
section .data
    message: db "Hello, World!", 10
    message_len: equ $ - message

section .text
    global _start

_start:
    ; write(1, message, message_len)
    mov rax, 1              ; syscall: write
    mov rdi, 1              ; fd: stdout
    mov rsi, message        ; buffer
    mov rdx, message_len    ; délka
    syscall

    ; exit(0)
    mov rax, 60             ; syscall: exit
    mov rdi, 0              ; status 0
    syscall
```

### Krok 5: Kompilace a spuštění

Kompilace má dva kroky — nejdřív **assembler** vytvoří objektový soubor, pak **linker** z něj udělá spustitelný program:

```bash
nasm -f elf64 hello.asm -o hello.o
ld hello.o -o hello
./hello
```

Měli byste vidět:

```
Hello, World!
```

**Gratulujeme, právě jste spustili svůj první program v assembly!**

### Co dělají ty příkazy?

- `nasm -f elf64 hello.asm -o hello.o`
  - `-f elf64` = výstupní formát ELF64 (standard na Linuxu 64-bit)
  - `-o hello.o` = jméno výstupního objektového souboru
- `ld hello.o -o hello`
  - linker spojí objektový soubor do finálního spustitelného souboru
- `./hello` = spuštění

## Rozšíření: Jednoduchá smyčka

Teď si program rozšíříme tak, aby vypsal `Hello, World!` **pětkrát** za sebou. Ukážeme si, jak se v assembly dělá smyčka.

### Princip smyčky

V assembly smyčku obvykle uděláte takto:

1. Do nějakého registru (typicky `rcx`) dáte **počáteční hodnotu** (třeba 5)
2. Vytvoříte si **návěští** na začátek smyčky
3. Na konci každé iterace registr **snížíte** a **porovnáte** s nulou
4. Pokud není nula, **skočíte** zpátky na návěští

### Kód se smyčkou

```asm
section .data
    message: db "Hello, World!", 10
    message_len: equ $ - message

section .text
    global _start

_start:
    mov rcx, 5              ; počítadlo = 5 (kolikrát vypsat)

loop_start:
    push rcx                ; uložíme rcx na stack (syscall ho může změnit)

    ; write(1, message, message_len)
    mov rax, 1
    mov rdi, 1
    mov rsi, message
    mov rdx, message_len
    syscall

    pop rcx                 ; obnovíme počítadlo
    dec rcx                 ; rcx = rcx - 1
    jnz loop_start          ; pokud rcx != 0, skoč zpátky

    ; exit(0)
    mov rax, 60
    mov rdi, 0
    syscall
```

### Co je tu nového?

- **`push rcx` / `pop rcx`** — syscall může modifikovat registry (zvláště `rcx` a `r11`), takže si počítadlo před syscallem uložíme na zásobník a po něm vyzvedneme. Tohle je důležité pravidlo!
- **`dec rcx`** — sníží `rcx` o 1. Zároveň automaticky nastaví **zero flag**, pokud je výsledek 0.
- **`jnz loop_start`** — *Jump if Not Zero*. Skočí na návěští, pokud zero flag **není** nastavený (tedy pokud `rcx` ještě není 0).

### Sestavení a spuštění

```bash
nasm -f elf64 hello.asm -o hello.o
ld hello.o -o hello
./hello
```

Výstup:

```
Hello, World!
Hello, World!
Hello, World!
Hello, World!
Hello, World!
```

## Shrnutí

V této kapitole jste se naučili:

- Jak je strukturován assembly program (sekce `.data`, `.text`, symbol `_start`)
- Jak použít `write` syscall pro výpis na obrazovku
- Jak správně ukončit program pomocí `exit`
- Jak zkompilovat a slinkovat program pomocí NASM a ld
- Jak napsat jednoduchou smyčku s počítadlem a podmíněným skokem
- Proč je důležité ukládat registry před syscallem

### Co dál?

Pokud vás assembly zaujalo, můžete se dál podívat na:

- **Čtení vstupu od uživatele** pomocí `read` syscallu
- **Aritmetiku** s více registry a ukazateli
- **Volání funkcí** a jak funguje zásobníkový rámec
- **Práci se soubory** přes `open`, `read`, `write`, `close`
- **System V AMD64 ABI** — konvence pro volání funkcí, zvláště když linkujete s C knihovnami

Pěkným dalším krokem je napsat si vlastní jednoduchou funkci `strlen`, která spočítá délku null-terminated řetězce.
