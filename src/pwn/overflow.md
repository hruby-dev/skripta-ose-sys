# Buffer Overflow (koncept)

## Co je buffer overflow?

Buffer overflow (přetečení bufferu) je zranitelnost, ke které dochází, když program zapisuje více dat do paměťového bufferu, než kolik je pro něj alokováno.

### Vizualizace

Představ si buffer o velikosti 8 bytů:

```
[ A A A A A A A A ]
```

Program ale zapíše 12 bytů:

```
[ A A A A A A A A | B B B B ]
```

Tyto extra hodnoty (`B`) přepíšou další data v paměti.

## Proč je to problém?

Paměť programu není izolovaná po „logických blocích“, ale je lineární. To znamená, že:

- vedle bufferu mohou být důležité hodnoty
- může dojít k přepsání proměnných
- v některých případech i návratové adresy funkce

To umožňuje útočníkovi ovlivnit chování programu.

## Typické příklady v C

V praxi buffer overflow často vzniká při práci s funkcemi, které nekontrolují délku vstupu, nebo jsou použity nesprávně.

### `gets`

```c
char buffer[8];
gets(buffer);
```

Funkce `gets` vůbec nekontroluje délku vstupu, a proto je z principu nebezpečná.

### `fgets` se špatnou velikostí

```c
char buffer[8];
fgets(buffer, 64, stdin);
```

`fgets` je sama o sobě bezpečnější, ale pouze tehdy, pokud je předaná správná velikost bufferu. Zde program tvrdí, že může číst až 64 bytů do bufferu o velikosti 8 bytů.

### `strcpy`

```c
char buffer[8];
strcpy(buffer, "AAAAAAAAAAAAAAAA");
```

`strcpy` kopíruje data až do konce řetězce a neověřuje, zda se vejdou do cílového bufferu.

### `strcat`

```c
char buffer[8] = "ABC";
strcat(buffer, "DEFGHIJK");
```

`strcat` připojuje další data na konec existujícího řetězce. Pokud není v bufferu dostatek místa, dojde k přetečení.

### `sprintf`

```c
char buffer[8];
sprintf(buffer, "%s", "AAAAAAAAAAAA");
```

`sprintf` zapisuje formátovaný výstup bez omezení délky. Pokud výstup přesáhne velikost bufferu, dojde k přepisu paměti.

### `read`

```c
char buffer[8];
read(0, buffer, 32);
```

Nízkourovňové funkce jako `read` dávají programátorovi plnou kontrolu nad počtem čtených bytů. Pokud tento počet neodpovídá velikosti bufferu, vzniká overflow.

### `scanf` bez omezení délky

```c
char buffer[8];
scanf("%s", buffer);
```

Formát `%s` čte vstup až do whitespace, ale bez omezení délky. Bez specifikace maximální šířky je tak použití potenciálně nebezpečné.

### `memcpy`

```c
char buffer[8];
char src[32] = {0};
memcpy(buffer, src, 32);
```

`memcpy` kopíruje přesně zadaný počet bytů. Pokud programátor předá větší délku, než jakou má cílový buffer, dojde k přetečení.

Tyto funkce nejsou vždy samy o sobě „špatné“, ale při nesprávném použití velmi snadno vedou ke vzniku zranitelnosti.

## Co lze přepsat?

V závislosti na kontextu může overflow ovlivnit například:

- sousední proměnné
- ukazatele
- struktury
- řídicí data (např. návratová adresa)


## Stack vs Heap

Buffer overflow se může objevit na různých místech:

- **stack overflow** – přetečení lokální proměnné ve funkci
- **heap overflow** – přetečení dynamicky alokované paměti


## Důležité poznámky

- Ne každý overflow je exploitatelný
- Moderní systémy mají ochrany (ASLR, NX, stack canary)
- Přesto jde o jednu z nejdůležitějších tříd zranitelností

## Shrnutí

Buffer overflow je situace, kdy program zapíše více dat, než kolik buffer pojme, a tím přepíše sousední paměť. To může vést k chybám programu nebo k jeho plnému ovládnutí.
