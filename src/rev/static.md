# Statická analýza

Statická analýza je technika reverse engineeringu, při které zkoumáme program bez jeho spuštění. Cílem je pochopit strukturu a logiku programu pouze na základě jeho binární podoby, či analýza jeho statických dat.

Mezi základní techniky statické analýzy patří:

- datová analýza
- dekompilace / disassemblace

## Datová analýza
### Strings

Mnoho programů obsahuje v binární podobě čitelné stringy. Ty mohou zahrnovat například:

- uživatelské zprávy
- chybové hlášky
- hesla nebo klíče

V Linuxu lze tyto řetězce snadno vypsat pomocí nástroje `strings`:

```bash
$ strings program
```

Ukázka výstupu:

```bash
$ strings program
What's the password?
ACCESS GRANTED
Wrong password!
Sup3rStr0ngP4ssw0rd123!
...
```

Z výstupu můžeme identifikovat zajímavé hodnoty. Pokud máme podezření na konkrétní pattern (např. flag v CTF), můžeme použít filtraci:

```bash
$ strings program | grep flag{
```

Další častý scénář je nalezení hesla přímo ve výpisu:

```bash
$ strings program | grep pass
```

Pokud program obsahuje heslo v plain textu, lze jej tímto způsobem snadno odhalit.

### Praktická ukázka

```bash
$ ./program
What's the password? test
Wrong password!

$ ./program
What's the password? Sup3rStr0ngP4ssw0rd123!
ACCESS GRANTED
```

Pomocí `strings` jsme byli schopni odhalit správné heslo bez znalosti zdrojového kódu.

## Dekompilace

Technika `strings` je jednoduchá, ale ne vždy funguje – citlivé hodnoty mohou být skryté nebo generované dynamicky. V takovém případě využíváme dekompilaci.

Dekompilace je proces převodu binárního kódu zpět do podoby vyššího jazyka (typicky pseudokód podobný C). K tomu lze využít nástroje jako **Ghidra**.

### Základní postup v Ghidra

1. Vytvoření projektu (File → New Project)
2. Import binárky (File → Import File)
3. Spuštění analýzy (Analyze)
4. Otevření funkce `main`

Ghidra zobrazí:

- assembly 
- dekompilovaný pseudokód (C-like či Rust-like)

### Ukázka dekompilovaného kódu

```c
if (strcmp(input, "TajneHeslo123!") == 0) {
    puts("ACCESS GRANTED");
} else {
    puts("Wrong password!");
}
```

Z dekompilovaného kódu lze snadno odvodit:

* jak program pracuje
* jak probíhá kontrola vstupu
* jaká je správná hodnota (v tomto případě heslo)

Klíčová je funkce `strcmp`, která vrací 0 v případě shody. Program tedy porovnává vstup uživatele s pevně daným řetězcem.

```bash
$ ./program
What's the password? TajneHeslo123!
ACCESS GRANTED
```

## Kdy použít statickou analýzu?

Statickou analýzu (zejména s využitím dekompilátoru) je vhodné použít ve chvíli, kdy problém nelze vyřešit pouhým nalezením vstupu. Typicky jde o situace, kdy je kontrolní logika implementována složitěji a hodnoty nejsou v binárce přímo uložené v čitelné podobě.

Mezi takové případy patří například:

- flag checkery – vstup je ověřován pomocí více kroků (např. transformace, XOR, hashování, kontrolní součty)
- keygen úlohy – program generuje nebo ověřuje klíč na základě určitého algoritmu
- obfuskovaný kód – hodnoty jsou záměrně skryté nebo rozdělené do více částí
- vícekroková validace – vstup prochází několika podmínkami, smyčkami nebo funkcemi

V těchto scénářích je potřeba pochopit samotnou logiku programu. Dekompilátor umožňuje rekonstruovat strukturu kódu (větvení, smyčky, funkce) a identifikovat, jakým způsobem je vstup zpracováván. Na základě toho lze buď odvodit správný vstup, nebo vytvořit vlastní řešení (např. keygen), které splní požadované podmínky.
