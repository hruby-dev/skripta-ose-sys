# Co je operační systém

Operační systém (OS) je základní software, který zprostředkovává komunikaci mezi **hardwarem počítače** a **uživatelskými programy**. Jeho hlavním úkolem je spravovat systémové prostředky a poskytovat jednotné rozhraní pro jejich využití.

Bez operačního systému by každý program musel přímo ovládat hardware, což by bylo složité, neefektivní a nebezpečné.

## Co operační systém dělá

Operační systém zajišťuje zejména:

- **správu procesů** (spouštění, plánování, ukončování),
- **správu paměti** (alokace a ochrana paměťového prostoru),
- **práci se soubory** (souborové systémy),
- **vstup a výstup** (komunikace se zařízeními),
- **bezpečnost a izolaci** procesů.

Tyto funkce jsou realizovány jádrem systému.

## Kernel (jádro systému)

**Kernel** je centrální část operačního systému, která běží v privilegovaném režimu (tzv. *kernel mode*). Má plný přístup k hardwaru a zodpovídá za řízení všech klíčových operací.

### Hlavní úlohy kernelu

- přímá komunikace s hardwarem,
- správa procesů a vláken,
- správa paměti,
- obsluha systémových volání,
- řízení zařízení (přes ovladače).

Kernel funguje jako **prostředník**: program požádá o operaci (např. otevření souboru) a kernel ji bezpečně provede.

> Uživatelské programy běží v tzv. *user mode*, kde nemají přímý přístup k hardwaru. Kernel běží odděleně v *kernel mode*.

---

## Moduly kernelu

Moderní operační systémy (např. Linux) umožňují rozšiřovat kernel pomocí **modulů** (*kernel modules*).

Modul je část kódu, kterou lze:

- dynamicky **načíst** do běžícího kernelu,
- používat bez nutnosti restartu systému,
- případně **odebrat** za běhu.

### K čemu se moduly používají

Nejčastěji slouží jako:

- **ovladače zařízení** (např. síťové karty, disky),
- podpora souborových systémů,
- rozšíření funkcionality kernelu.

### Výhody modulárního přístupu

- **flexibilita** — není nutné mít vše přímo v jádře,
- **menší jádro** — načítají se jen potřebné části,
- **snazší vývoj a testování**,
- **bez restartu systému** při změnách.

### Příklad (Linux)

Načtení modulu:

```bash
sudo modprobe <název_modulu>
````

Výpis načtených modulů:

```bash
lsmod
```

Odebrání modulu:

```bash
sudo rmmod <název_modulu>
```

<details>
<summary>Poznámka</summary>

Nesprávně napsaný modul může způsobit pád celého systému, protože běží v kernel mode a má plný přístup k paměti i hardwaru.

</details>


## Shrnutí

* Operační systém spravuje hardware a poskytuje služby programům.
* **Kernel** je jeho nejdůležitější část, která běží s plnými právy.
* Programy komunikují s kernelem pomocí systémových volání.
* **Moduly kernelu** umožňují dynamicky rozšiřovat funkcionalitu systému bez restartu.

> Kernel lze chápat jako „řídící centrum“ systému, zatímco moduly jsou jeho rozšiřující komponenty.