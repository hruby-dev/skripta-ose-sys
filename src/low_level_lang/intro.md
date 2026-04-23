# Low level jazyky

V předchozích kapitolách jsme si ukázali, jak vypadá programování úplně na dně — přímo v assembly. Assembly je sice maximálně blízko hardwaru, ale psát v něm větší aplikace je nepraktické: kód je ukecaný, není přenositelný mezi architekturami a snadno se v něm udělá chyba.

**Nízkoúrovňové jazyky** (low-level languages) jsou kompromisem mezi rychlostí a kontrolou assembly a pohodlím vyšších jazyků. Kompilují se přímo do strojového kódu, umožňují přímou práci s pamětí a nepotřebují běhové prostředí jako JVM nebo Python interpreter. Zároveň ale dávají programátorovi lidsky čitelnou syntaxi a abstrakce, které mu šetří práci.

V této části knihy se podíváme na tři nejvýznamnější zástupce:

## C

**C** je "matka všech jazyků". Vznikl v roce 1972 v Bell Labs a je dodnes páteří operačních systémů, embedded zařízení a knihoven, na kterých stojí prakticky celý ekosystém softwaru. Linuxové jádro, Git, Postgres, Python interpreter — všechno je napsané v C.

Je to **minimalistický, procedurální jazyk** s ruční správou paměti a přímočarým mapováním na hardware. Dává vám maximum kontroly, ale za cenu veškeré odpovědnosti — žádný garbage collector, žádná bounds-checking, žádné bezpečnostní sítě.

## C++

**C++** vznikl jako rozšíření C o objektově orientované programování a postupně se rozrostl do obřího multi-paradigmatického jazyka. Dnes se v něm píší **herní enginy** (Unreal Engine), **prohlížeče** (Chromium), **databáze** (MongoDB), **high-frequency trading systémy** a zatím i velké části Windows.

Přidává k C **třídy**, **šablony** (templates), **výjimky**, **RAII** pro automatickou správu zdrojů a obrovskou standardní knihovnu (STL). Cenou za tu mocnost je složitost — C++ je notoricky rozsáhlý jazyk, kterým si lze v plné šíři vylámat zuby.

## Rust

**Rust** je moderní jazyk od Mozilly (první stabilní verze 2015), který se pokouší řešit desítky let staré bolístky C a C++ — zejména **chyby paměti** (use-after-free, buffer overflow, data races), které stojí za většinou bezpečnostních zranitelností v systémovém softwaru.

Klíčovou inovací je **borrow checker** — kompilátor už při překladu hlídá, kdo co vlastní a po jakou dobu. Pokud napíšete kód, který by mohl způsobit paměťovou chybu, program se zkrátka nezkompiluje. Výsledkem je bezpečnost **bez garbage collectoru** a bez runtime overheadu.

Rust dnes používají Microsoft, Amazon, Cloudflare, Discord a od 2022 se dokonce postupně dostává i do **Linuxového jádra** jako alternativa k C.

---

V dalších kapitolách se na každý z těchto jazyků podíváme podrobněji — jak je nainstalovat, jak zkompilovat Hello World a co je na nich specifického.
