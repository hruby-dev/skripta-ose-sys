# C

**C** je jeden z nejstarších stále používaných programovacích jazyků. Vznikl kolem roku 1972 v Bell Labs, kde ho Dennis Ritchie navrhl jako jazyk pro přepis operačního systému UNIX. Od té doby prošel několika revizemi (K&R C, ANSI C / C89, C99, C11, C17, C23), ale v jádru zůstává stejný: **malý, rychlý, přímočarý jazyk, který vám dává plnou kontrolu nad pamětí a hardwarem**.

C je dodnes páteří systémového softwaru. Linux kernel, Git, SQLite, PostgreSQL, Python interpreter, Redis, nginx — všechno je napsáno v C.

## Instalace

Skoro každý operační systém už má C kompilátor k dispozici, nebo je triviální ho doinstalovat. Nejpoužívanější jsou **GCC** (`gcc`) a **Clang** (`clang`).

### Linux

```bash
# Debian / Ubuntu
sudo apt install gcc

# Arch Linux
sudo pacman -S gcc

# Fedora
sudo dnf install gcc
```

### macOS

```bash
xcode-select --install
```

Tím získáte `clang`, který na macOS funguje jako `gcc`.

### Windows

Podobně jako u C++:

- **MSYS2** s GCC ([msys2.org](https://www.msys2.org/))
- **MinGW-w64**
- **Visual Studio** s kompilátorem MSVC (`cl.exe`)

### Ověření

```bash
gcc --version
```

## Hello, World!

Soubor `hello.c`:

```c
#include <stdio.h>

int main(void) {
    printf("Hello, World!\n");
    return 0;
}
```

### Kompilace a spuštění

```bash
gcc hello.c -o hello
./hello
```

Výstup:

```
Hello, World!
```

### Co se v tom kódu děje?

- `#include <stdio.h>` — zahrnutí hlavičkového souboru standardní I/O knihovny (obsahuje `printf`)
- `int main(void)` — vstupní bod programu, `void` říká, že funkce nebere žádné argumenty
- `printf(...)` — formátovaný výpis na standardní výstup
- `\n` — escape sekvence pro nový řádek
- `return 0;` — návratová hodnota programu (0 = vše v pořádku)

### Užitečné kompilátorové přepínače

Pro seriózní vývoj se vyplatí přidat varování a standard:

```bash
gcc -Wall -Wextra -std=c17 hello.c -o hello
```

- `-Wall -Wextra` — zapne většinu varování, které vám odhalí běžné chyby
- `-std=c17` — řekne kompilátoru, kterou verzi standardu C chcete
- `-g` — přidá debug symboly (pro `gdb`)
- `-O2` — optimalizace pro release build

## Základní datové typy

C je **staticky typovaný** jazyk — každá proměnná má typ, který musíte uvést při deklaraci. Typy v C jsou přímo navázané na hardware, takže jejich velikost v bajtech se může lišit podle platformy (ale jsou zaručené minimální velikosti).

### Celá čísla

| Typ            | Typická velikost | Rozsah (typicky)                     |
|----------------|------------------|--------------------------------------|
| `char`         | 1 B              | -128 až 127 (nebo 0 až 255)          |
| `short`        | 2 B              | -32 768 až 32 767                    |
| `int`          | 4 B              | cca -2,1 mld. až 2,1 mld.            |
| `long`         | 4 nebo 8 B       | závisí na platformě                  |
| `long long`    | 8 B              | cca ±9,2 × 10¹⁸                      |

Každý z nich má i **`unsigned` variantu** (pouze nezáporná čísla, dvojnásobný kladný rozsah):

```c
unsigned int pocet = 100;
signed char teplota = -15;
```

Pokud potřebujete **přesně definované velikosti** bez ohledu na platformu, používejte typy z `<stdint.h>`:

```c
#include <stdint.h>

int8_t   a = -42;        // přesně 8 bitů se znaménkem
uint16_t b = 65000;      // přesně 16 bitů bez znaménka
int32_t  c = 1000000;    // přesně 32 bitů
uint64_t d = 1ULL << 40; // přesně 64 bitů
```

### Desetinná čísla

| Typ           | Velikost | Přesnost                       |
|---------------|----------|--------------------------------|
| `float`       | 4 B      | ~7 desetinných míst            |
| `double`      | 8 B      | ~15 desetinných míst           |
| `long double` | 10–16 B  | větší přesnost (závisí na HW)  |

```c
float  pi_f = 3.14159f;   // suffix f = float literál
double pi_d = 3.141592653589793;
```

### Znaky a řetězce

V C **neexistuje samostatný typ pro řetězec**. Řetězec je jen **pole znaků (`char`) zakončené nulovým bajtem `\0`**.

```c
char znak = 'A';               // jeden znak (vlastně číslo 65)
char pozdrav[] = "Ahoj";       // pole 5 znaků: A, h, o, j, \0
```

Práce s řetězci se dělá přes funkce z `<string.h>`:

```c
#include <string.h>

strlen(pozdrav);        // délka (nepočítá \0)
strcpy(cil, zdroj);     // kopírování (POZOR na přetečení!)
strcmp(a, b);           // porovnání
```

### Logický typ

Historicky měl C jen celá čísla — `0` znamenalo *false*, cokoliv jiného *true*. Od standardu C99 existuje i `_Bool`, s hlavičkou `<stdbool.h>` pak můžete používat známější zápis:

```c
#include <stdbool.h>

bool hotovo = true;
if (!hotovo) { /* ... */ }
```

### `void`

Speciální "žádný typ". Používá se pro:

- funkce, které nic nevracejí: `void log(const char *msg);`
- generické ukazatele: `void *data;` (o tom za chvíli)

## Proměnné, konstanty, operátory

```c
int x = 10;              // deklarace + inicializace
const double PI = 3.14;  // konstanta, nelze měnit

x += 5;                  // x = x + 5
x++;                     // inkrementace (o 1)

int zbytek = 17 % 3;     // modulo = 2
int a = (x > 0) ? 1 : -1; // ternární operátor
```

### Kontrolní struktury

```c
if (x > 0) {
    printf("kladné\n");
} else if (x == 0) {
    printf("nula\n");
} else {
    printf("záporné\n");
}

for (int i = 0; i < 10; i++) { /* ... */ }

int i = 0;
while (i < 10) { i++; }

switch (x) {
    case 1:  puts("jedna"); break;
    case 2:  puts("dva");   break;
    default: puts("něco jiného");
}
```

## Pole

Pole v C má **pevnou velikost** známou při překladu:

```c
int cisla[5] = {10, 20, 30, 40, 50};
printf("%d\n", cisla[2]);   // 30

cisla[0] = 99;              // přepis prvního prvku
```

Pozor: **C vám nehlídá přetečení pole**. Zápis `cisla[10] = 1;` u pole o velikosti 5 se zkompiluje bez problémů a za běhu způsobí tiché poškození paměti nebo segfault. Tohle je jedna z hlavních kategorií bezpečnostních zranitelností v C kódu.

## Pointery (ukazatele)

**Pointer** je proměnná, která **neobsahuje hodnotu samotnou, ale adresu, kde je hodnota uložena v paměti**. Pointery jsou asi nejvíc "vyhlášená" vlastnost C — umožňují přímou manipulaci s pamětí, ale jsou zároveň zdrojem většiny bugů v C programech.

### Základy

```c
int x = 42;
int *p = &x;     // p je pointer na int, ukazuje na adresu x

printf("%d\n", x);   // 42
printf("%d\n", *p);  // 42 — dereference pointeru, vezme hodnotu z adresy
printf("%p\n", (void*)p);  // adresa, kam p ukazuje (něco jako 0x7ffee...)

*p = 100;        // změní hodnotu na adrese, kam p ukazuje
printf("%d\n", x);  // 100 — x se změnilo, protože p na něj ukazoval
```

Dva klíčové operátory:

- `&x` — **adresa** proměnné `x` (address-of operator)
- `*p` — **hodnota** na adrese, kterou drží `p` (dereference)

Čtení deklarace: `int *p` = "p je pointer na int". Hvězdička "patří" k proměnné, ne k typu — proto `int *p, q;` deklaruje jeden pointer a jeden obyčejný int.

### Proč pointery existují?

Hlavní důvody, proč je v C vůbec potřebujete:

**1. Předávání velkých dat do funkcí.** Bez pointerů se parametr kopíruje. Pointer umožňuje funkci pracovat přímo s originálem:

```c
void zdvojnasob(int *n) {
    *n = *n * 2;
}

int x = 5;
zdvojnasob(&x);    // předáme adresu x
printf("%d\n", x); // 10
```

**2. Dynamická alokace paměti.** Kolik paměti bude potřeba, nemusíte vědět při překladu — alokujete ji za běhu:

```c
#include <stdlib.h>

int *pole = malloc(100 * sizeof(int));  // prostor pro 100 intů
if (pole == NULL) { /* ošetření chyby */ }

pole[0] = 42;
// ...
free(pole);   // NUTNO explicitně uvolnit!
```

`malloc` vrací pointer na začátek přidělené paměti (nebo `NULL` při neúspěchu). Po skončení práce **musíte** paměť uvolnit přes `free`, jinak vzniká **memory leak**.

**3. Pole a pointery spolu úzce souvisí.** Jméno pole se při většině použití chová jako pointer na jeho první prvek:

```c
int cisla[5] = {1, 2, 3, 4, 5};
int *p = cisla;       // totéž jako &cisla[0]

printf("%d\n", p[2]);  // 3
printf("%d\n", *(p + 2)); // 3 — pointerová aritmetika
```

**4. Řetězce.** Protože řetězec je pole znaků, pracujeme s ním přes pointer:

```c
const char *jmeno = "Pavel";  // pointer na první znak
```

### Nebezpečí pointerů

Pointery jsou mocné, ale snadno se s nimi udělá chyba. Nejčastější průšvihy:

- **Dereference NULL pointeru** — pád programu (segfault)
- **Dangling pointer** — pointer ukazuje na paměť, která už byla uvolněna
- **Buffer overflow** — zápis za konec pole
- **Memory leak** — alokovaná paměť se nikdy neuvolní
- **Double free** — uvolnění stejné paměti dvakrát

Moderní nástroje jako **Valgrind**, **AddressSanitizer** (`gcc -fsanitize=address`) nebo statické analyzátory vám tyhle chyby pomohou najít.

```c
int *p = NULL;
*p = 10;    //  segfault

int *q = malloc(sizeof(int));
free(q);
*q = 5;     //  use-after-free
free(q);    //  double free
```

### `void *` — univerzální pointer

Pointer na cokoliv. Nejde přímo dereferencovat (kompilátor neví, kolik bajtů číst), ale hodí se pro generické funkce jako `malloc` nebo `memcpy`:

```c
void *buf = malloc(1024);
int *as_ints = (int *)buf;  // přetypování na konkrétní typ
```

## Struktury

Vlastní složený typ, který seskupuje několik hodnot pod jeden název:

```c
struct Bod {
    double x;
    double y;
};

struct Bod b = {3.0, 4.0};
printf("%f %f\n", b.x, b.y);

struct Bod *p = &b;
printf("%f\n", p->x);   // p->x je zkratka za (*p).x
```

S `typedef` si ušetříte psaní `struct`:

```c
typedef struct {
    double x;
    double y;
} Bod;

Bod b = {1.0, 2.0};
```

## Funkce a hlavičkové soubory

Když projekt roste, rozdělujete ho do více souborů. Rozhraní (deklarace) se dává do **hlavičkového souboru** `.h`, implementace do `.c`:

**`math_utils.h`:**
```c
#ifndef MATH_UTILS_H
#define MATH_UTILS_H

int soucet(int a, int b);

#endif
```

**`math_utils.c`:**
```c
#include "math_utils.h"

int soucet(int a, int b) {
    return a + b;
}
```

**`main.c`:**
```c
#include <stdio.h>
#include "math_utils.h"

int main(void) {
    printf("%d\n", soucet(3, 4));
    return 0;
}
```

Kompilace obou souborů najednou:

```bash
gcc main.c math_utils.c -o program
```

Ty `#ifndef ... #define ... #endif` na začátku hlavičkového souboru jsou **include guard** — chrání před tím, aby se obsah hlavičky vložil dvakrát, pokud ji zahrne více souborů.

## Správa paměti: stack vs heap

C rozlišuje dva hlavní způsoby, kde je paměť:

- **Stack (zásobník)** — lokální proměnné funkcí. Alokace je bleskurychlá, paměť se automaticky uvolní na konci funkce. Omezená velikost (typicky pár MB).
- **Heap (halda)** — paměť alokovaná ručně přes `malloc`/`calloc`/`realloc`. Velikost omezená jen množstvím RAM, ale musíte ji ručně uvolnit přes `free`.

```c
void funkce(void) {
    int x = 10;                  // stack — automatické
    int *p = malloc(sizeof(int)); // heap — musíte free()
    *p = 20;

    free(p);
    // x se uklidí samo po návratu
}
```

## Shrnutí

C je malý jazyk — standardní knihovna je přehledná, syntaxe se vejde na pár stránek. Naučit se základy trvá dny, ne roky. Obtížnost C je jinde: **každý řádek kódu nese odpovědnost za to, co dělá s pamětí, a kompilátor vám pomáhá jen omezeně**.

Přesto (nebo právě proto) stojí za to se C naučit. Dá vám:

- **Hluboké pochopení toho, jak počítač pracuje** — pointery, paměť, layout struktur
- **Schopnost číst systémový kód** — jádra, ovladače, knihovny
- **Přenositelnost** — C kompilátor existuje pro úplně každou platformu
- **Respekt k abstrakcím vyšších jazyků** — když jste si sami ručně spravovali paměť, oceníte garbage collector

>  Pokud chcete jít hlouběji, klasická reference je **"The C Programming Language"** od Kernighana a Ritchieho (tzv. "K&R"). Z modernějších knih doporučuju **"Modern C"** od Jense Gustedta.
