# Systémová volání

Systémová volání představují základní mechanismus, kterým mohou **uživatelské procesy** požadovat služby od **jádra operačního systému**. Program běžící v uživatelském režimu nemá přímý přístup k privilegovaným operacím, jako je práce se soubory, komunikace se zařízeními nebo vytváření nových procesů. Místo toho využívá rozhraní systémových volání.

## Proč systémová volání existují

Mnoho operací v počítači je **privilegovaných**, což znamená, že je nemůže provádět libovolný uživatelský program přímo. Důvodem je především:

- **bezpečnost** — proces nesmí svévolně přistupovat do paměti jádra,
- **ochrana dat** — proces nesmí nekontrolovaně manipulovat se soubory jiných procesů,
- **stabilita systému** — přímý přístup k hardwaru by mohl způsobit pád systému,
- **řízení prostředků** — operační systém musí rozhodovat, kdo a jak může využívat zařízení, procesor nebo paměť.

Systémová volání tedy tvoří **kontrolované rozhraní** mezi uživatelským programem a jádrem.

## Jak systémové volání funguje

Při provedení systémového volání dojde ke krátkému přechodu z **uživatelského režimu** do **režimu jádra**. Proces předá jádru požadavek, například „otevři tento soubor“ nebo „zapiš tato data na výstup“. Jádro požadavek provede a vrátí výsledek zpět programu.

Z pohledu programátora se systémové volání často jeví jako obyčejná funkce, například `open()`, `read()` nebo `write()`. Ve skutečnosti se však jedná o přechod do chráněné části systému.

## Pozorování systémových volání pomocí `strace`

Na unixových systémech lze systémová volání sledovat pomocí nástroje `strace`. Ten vypisuje, jaká systémová volání program během svého běhu provádí.

Například chceme zobrazit obsah souboru `welcome.txt` pomocí programu `cat`:

```bash
strace cat welcome.txt
```

Ukázka zjednodušeného výstupu:

```bash
execve("/usr/bin/cat", ["cat", "welcome.txt"], 0x7ffc571b8e98)
...
openat(AT_FDCWD, "welcome.txt", O_RDONLY) = 3
read(3, "Hello Class!\n", 131072) = 13
write(1, "Hello Class!\n", 13) = 13
close(3) = 0
exit_group(0)
```

### Interpretace výpisu

Z uvedeného výstupu lze vyčíst několik důležitých kroků:

* `execve(...)`
  spouští program `cat`;

* `openat(...) = 3`
  otevírá soubor `welcome.txt` pro čtení a vrací deskriptor `3`;

* `read(3, ...) = 13`
  čte 13 bajtů ze souboru označeného deskriptorem `3`;

* `write(1, ...) = 13`
  zapisuje těchto 13 bajtů na standardní výstup, tedy do terminálu;

* `close(3) = 0`
  uzavírá otevřený soubor;

* `exit_group(0)`
  korektně ukončuje proces.

> Nástroj `strace` je velmi užitečný při ladění programů, zejména když potřebujeme zjistit, proč program neotevře soubor, proč selže přístup k zařízení nebo jak komunikuje s operačním systémem.


## File descriptor

Při práci se soubory a vstupně-výstupními kanály se používá pojem **file descriptor** (deskriptor souboru).

File descriptor je malé celé číslo, kterým proces identifikuje otevřený soubor, rouru, socket nebo jiné I/O rozhraní. Po úspěšném otevření souboru vrací například systémové volání `open()` právě tento deskriptor.

Ten je následně předáván dalším systémovým voláním, například:

* `read(fd, ...)`
* `write(fd, ...)`
* `close(fd)`

### Typické deskriptory

| Deskriptor  | Název    | Význam                               |
| ----------- | -------- | ------------------------------------ |
| `0`         | `stdin`  | standardní vstup                     |
| `1`         | `stdout` | standardní výstup                    |
| `2`         | `stderr` | standardní chybový výstup            |
| `3` a vyšší | —        | další otevřené soubory nebo zařízení |

Pokud tedy například `open()` vrátí hodnotu `3`, znamená to, že soubor byl úspěšně otevřen a proces s ním dále pracuje pomocí tohoto deskriptoru.


## Čtení souboru

Čtení souboru je typicky tříkrokový proces:

1. **Otevření souboru** pomocí `open()`,
2. **načtení dat** pomocí `read()`,
3. **uzavření souboru** pomocí `close()`.

### Postup

* `open()` otevře soubor a vrátí file descriptor,
* `read()` načte data ze souboru do paměťového bufferu,
* `close()` uvolní související systémové prostředky.

### Příklad v jazyce C

```c
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main(void) {
    char buffer[128];
    int fd = open("welcome.txt", O_RDONLY);
    if (fd == -1) return 1;

    ssize_t n = read(fd, buffer, sizeof(buffer) - 1);
    if (n >= 0) {
        buffer[n] = '\0';
        printf("%s", buffer);
    }

    close(fd);
    return 0;
}
```

### Vysvětlení příkladu

* `open("welcome.txt", O_RDONLY)` otevře soubor pouze pro čtení,
* návratová hodnota se uloží do proměnné `fd`,
* `read()` načte obsah souboru do pole `buffer`,
* na konec načteného textu se přidá znak `'\0'`, aby s daty bylo možné pracovat jako s řetězcem v C,
* `printf()` obsah vypíše,
* `close(fd)` soubor uzavře.

<details>
<summary>Poznámka k bezpečnosti a robustnosti</summary>

V praxi je vhodné kontrolovat i návratovou hodnotu `read()` a případně ošetřit situaci, kdy se načte méně dat, než očekáváme, nebo dojde k chybě. U větších souborů je navíc potřeba čtení opakovat ve smyčce.

</details>


## Zápis do souboru

Zápis do souboru probíhá podobně jako čtení, ale místo `read()` se používá `write()`.

### Typický postup

1. **Otevřít nebo vytvořit soubor** pomocí `open()`,
2. **zapsat data** pomocí `write()`,
3. **uzavřít soubor** pomocí `close()`.

### Příklad v jazyce C

```c
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main(void) {
    const char *text = "Hello Class!\n";
    int fd = open("output.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (fd == -1) return 1;

    write(fd, text, strlen(text));
    close(fd);

    printf("Zapsáno do output.txt: %s", text);
    return 0;
}
```

### Vysvětlení příkladu

Příznaky předané do `open()` mají následující význam:

* `O_WRONLY` — soubor je otevřen pouze pro zápis,
* `O_CREAT` — pokud soubor neexistuje, bude vytvořen,
* `O_TRUNC` — pokud soubor existuje, jeho původní obsah bude zkrácen na nulovou délku.

Hodnota `0644` určuje přístupová práva nově vytvořeného souboru.

> Funkce `write()` zapisuje bajty do souboru, ale stejně jako u `read()` je v reálných programech vhodné kontrolovat její návratovou hodnotu.


## Vybraná systémová volání v Unixu

Sada systémových volání závisí na konkrétním operačním systému, nicméně v unixových systémech se často setkáváme s následujícími:

| Syscall               | Popis                                   | Typické použití                       |
| --------------------- | --------------------------------------- | ------------------------------------- |
| `open` / `openat`     | otevře soubor a vrátí jeho deskriptor   | otevření souboru pro čtení nebo zápis |
| `read`                | čte data ze souboru nebo zařízení       | načtení obsahu souboru                |
| `write`               | zapisuje data do souboru nebo na výstup | výpis textu do terminálu              |
| `close`               | uzavře otevřený deskriptor              | uvolnění prostředků                   |
| `execve`              | spustí nový program                     | spuštění programu `cat`               |
| `exit` / `exit_group` | ukončí proces                           | korektní ukončení programu            |


## Shrnutí

Systémová volání tvoří základní rozhraní mezi uživatelským programem a operačním systémem. Umožňují bezpečně provádět privilegované operace, které program nemůže vykonávat přímo.