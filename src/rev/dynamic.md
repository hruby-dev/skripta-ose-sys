# Dynamická analýza

Dynamická analýza je technika reverse engineeringu, při které program spouštíme a sledujeme jeho chování za běhu. Na rozdíl od statické analýzy tak pracujeme s reálným execution flow, pamětí a registry.

Cílem je například:

- sledovat, jak program zpracovává vstup
- identifikovat rozhodovací body (podmínky)
- manipulovat běh programu

## Nástroje

Pro dynamickou analýzu se používají debuggery. V této části budeme pracovat s **GDB** rozšířeným o **GEF (GDB Enhanced Features)**.

GEF přidává:

* lepší výpis registrů
* přehlednější práci s pamětí
* užitečné zkratky pro reversing

## Základy debugování

Spuštění programu v GDB:

```bash
$ gdb ./program
```

Spuštění programu:

```bash
gef➤  run
```

### Breakpointy

Breakpoint zastaví program na určité instrukci nebo funkci:

```bash
gef➤  break main
gef➤  break strcmp
```

### Registry

Zobrazení registrů:

```bash
gef➤  info registers
```

V GEF jsou registry automaticky zobrazeny při každém zastavení programu.

### Výpis funkcí

Při prvotní orientaci v binárce je užitečné zobrazit seznam známých funkcí:

```bash
gef➤  info functions
```

Tento příkaz vypíše symboly, které má debugger k dispozici. V jednoduchých případech tak rychle najdeme například `main`, `strcmp`, `puts`, `exit` nebo další importované funkce, na které lze následně nastavit breakpoint.

## Ukázka: strcmp

Program často používá funkci `strcmp` pro porovnání vstupu:

```c
if (strcmp(vstupUzivatele, "Klic_2026_x7!") == 0) {
    puts("ACCESS GRANTED");
} else {
    puts("Wrong password!");
}
```

Můžeme nastavit breakpoint na `strcmp` a program spustit:

```bash
gef➤  break strcmp
gef➤  run
```

Po zastavení programu zkontrolujeme registry. Na architektuře x86-64 se argumenty funkce předávají přes registry, přičemž:

* první argument je v registru `rdi`
* druhý argument je v registru `rsi`

Ukázkový výstup může vypadat například takto:

```bash
gef➤  info registers
rax            0x0                 0x0
rbx            0x7fffffffe1d8      0x7fffffffe1d8
rcx            0x7ffff7e2e6a0      0x7ffff7e2e6a0 <strcmp>
rdx            0x0                 0x0
rsi            0x555555556004      0x555555556004
rdi            0x7fffffffe0f0      0x7fffffffe0f0
rbp            0x7fffffffe160      0x7fffffffe160
rsp            0x7fffffffe0d8      0x7fffffffe0d8
rip            0x7ffff7e2e6a0      0x7ffff7e2e6a0 <strcmp>
```

Samotné registry nám řeknou, kde se argumenty nacházejí. Obsah řetězců následně přečteme z paměti.

### Čtení stringů z paměti

Obsah paměti lze zobrazit například takto:

```bash
gef➤  x/s $rdi
0x7fffffffe0f0: "test"

gef➤  x/s $rsi
0x555555556004: "Klic_2026_x7!"
```

To nám umožní přímo vidět:

- uživatelský vstup
- očekávanou hodnotu, se kterou se porovnává

Další možností je dump paměti po bytech:

```bash
gef➤  x/20bx $rdi
0x7fffffffe0f0: 0x74 0x65 0x73 0x74 0x00 0x41 0x41 0x41
0x7fffffffe0f8: 0x41 0x41 0x41 0x41 0x41 0x41 0x41 0x41
0x7fffffffe100: 0x00 0x00 0x00 0x00
```

Tento přístup je užitečný zejména tehdy, když data nejsou čistý string, ale například pole bajtů, binární buffer nebo částečně zakódovaná hodnota.

## Jumping

Debugger umožňuje změnit execution flow programu. To znamená, že můžeme ručně přeskočit část kódu a pokračovat od jiné instrukce.

Například po analýze disassemblovaného kódu zjistíme, že adresa `0x401234` odpovídá větvi, která vypisuje `ACCESS GRANTED`. Poté lze provést:

```bash
gef➤  jump *0x401234
```

Tím se změní instrukční pointer (`rip`) a program začne vykonávání od zadané adresy.

Jumping je užitečný například tehdy, když:

- chceme přeskočit neúspěšnou větev programu
- chceme otestovat konkrétní blok kódu bez splnění všech podmínek
- chceme rychle ověřit, co daná větev dělá

Je však potřeba opatrnost: skok do nesprávného místa může narušit stav programu. Pokud cílový blok očekává určité registry, zásobník nebo inicializovaná data, program může spadnout nebo se chovat nepředvídatelně.


## Patching

Patching znamená úpravu instrukcí programu. Lze jej provádět buď dočasně za běhu, nebo trvale přímo v binárce.

### Dočasný patch v debuggeru

Jednoduchý příklad je přepsání instrukce pomocí `NOP` (No Operation), čímž efektivně odstraníme část kódu:

```bash
gef➤  set {char}0x401220 = 0x90
```

Instrukce `0x90` na x86 odpovídá `NOP`. Pokud takto přepíšeme například podmíněný skok nebo část kontroly, program může pokračovat bez validace.

### Praktický význam

Patching se používá například tehdy, když chceme:

- odstranit kontrolu hesla
- obejít podmíněný skok
- změnit chování programu při testování
- ověřit hypotézu o významu konkrétní instrukce
