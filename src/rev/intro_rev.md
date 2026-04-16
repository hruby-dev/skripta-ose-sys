# Reverse Engineering

## Co je reverse engineering?

Reverse engineering (zpětné inženýrství) je proces analýzy již existujícího programu s cílem porozumět jeho vnitřnímu fungování. V praxi často pracujeme se zkompilovanými binárními soubory bez dostupného zdrojového kódu; pokud je však zdrojový kód k dispozici, lze jej při analýze využít.

V rámci analýzy se snažíme odhalit například:

- funkce programu a jejich chování
- konstanty a statické hodnoty
- strukturu a obsah datových segmentů
- případné ladicí (debug) informace

## Kde se reverse engineering používá?

Reverse engineering se uplatňuje především v oblasti kybernetické bezpečnosti, kde slouží k analýze škodlivého kódu (malware) a k identifikaci zranitelností v programech. Dále se využívá v herním průmyslu (modding, analýza herních mechanik) a v softwarovém vývoji, například při porozumění legacy kódu bez dokumentace.

### Capture the Flag

Významné zastoupení má reverse engineering v CTF (Capture The Flag) soutěžích, kde tvoří jednu ze základních kategorií. Úlohy typicky spočívají v analýze poskytnuté binárky s cílem získat flag, například obejitím autentizační logiky nebo pochopením mechanismu generování klíče a jeho reprodukcí na vzdálené instanci.

## Nástroje

K reverse engineeringu se používá řada specializovaných nástrojů. Základní dělení je následující:

### Dekompilátory (a disassemblery)

Tyto nástroje slouží ke statické analýze binárního kódu. Disassembler převádí strojový kód na instrukce v assembly, zatímco dekompilátor se snaží rekonstruovat kód na vyšší úrovni abstrakce (typicky pseudokód podobný jazyku C).

Zde je seznam často využívaných nástrojů:

- Ghidra
- Binary Ninja
- IDA

### Debugger

Debugger umožňuje dynamickou analýzu programu – připojí se k běžícímu procesu a umožňuje řídit jeho vykonávání (execution), například krokovat instrukce, nastavovat breakpointy a inspektovat paměť a registry.

Známé debuggery:

- GDB (GNU Debugger)
- GEF (GDB Enhanced Features)
- pwndbg
