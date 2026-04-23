# Procesy

## Co je proces?

### Proces vs Program

**Program** je statický soubor instrukcí uložený na disku — prostě binárka, která nic nedělá, dokud ji někdo nespustí.

**Proces** je naproti tomu **program v běhu**. Když program spustíte, operační systém mu přidělí paměť, registry, otevřené soubory, PID a další zdroje — a vytvoří z něj proces. Jeden program může běžet jako několik nezávislých procesů současně (typicky třeba několik instancí prohlížeče).

Analogie: program je jako recept v kuchařce, proces je vaření podle toho receptu. Stejný recept může vařit více lidí najednou — každý je samostatný "proces".

## Proč potřebujeme procesy

Procesy jsou jedním ze základních abstraktních konceptů operačního systému. Bez nich bychom měli několik zásadních problémů:

- **Multitasking** — díky procesům může na jednom CPU zdánlivě běžet víc programů najednou. OS mezi nimi rychle přepíná a uživatel má pocit, že všechno běží paralelně.
- **Izolace** — každý proces má svůj vlastní paměťový prostor. Jeden proces nemůže jen tak sáhnout do paměti jiného, což zvyšuje bezpečnost i stabilitu. Když jeden spadne, ostatní běží dál.
- **Sdílení zdrojů** — OS spravuje, kdo má kdy přístup k CPU, paměti, disku a dalším zdrojům. Procesy se o ně "spravedlivě" (podle pravidel plánovače) dělí.

## Stavový diagram procesu

Každý proces se během svého života nachází v některém z několika **stavů**. Přechody mezi stavy řídí operační systém. Zjednodušený diagram vypadá takto:

```
                 admitted              scheduler dispatch
     ┌─────┐ ─────────────► ┌───────┐ ─────────────────► ┌─────────┐
     │ new │                │ ready │                    │ running │
     └─────┘                └───────┘ ◄───────────────── └─────────┘
                                ▲         interrupt           │ exit
                                │                             ▼
                                │ I/O or event       ┌────────────┐
                                │ completion         │ terminated │
                                │                    └────────────┘
                            ┌───────┐
                            │ wait  │ ◄── I/O or event wait (z running)
                            └───────┘
```

V dalších sekcích si projdeme každý stav zvlášť.

### new

Proces byl právě vytvořen. Operační systém mu alokuje zdroje a přiděluje **PCB** (Process Control Block — vysvětlíme později).

### ready

Proces je připraven běžet a **čeká ve frontě**, až ho plánovač vybere a přidělí mu CPU. Má všechno co potřebuje — jen mu chybí procesor.

### running

Proces právě **vykonává instrukce na procesoru**. V jednom okamžiku může být na jednom jádru v tomto stavu jen jeden proces.

Ze stavu running proces odchází buď:
- **dobrovolně** — například se rozhodne čekat na I/O, nebo zkrátka skončil
- **nedobrovolně** — vyprší mu přidělené **časové kvantum** a plánovač mu CPU odebere

### wait (blocked)

Proces **čeká na externí data** — typicky na dokončení operace I/O (čtení z disku, síťová odpověď, vstup od uživatele). I kdyby bylo CPU volné, tento proces nemůže běžet, dokud na co čeká nedorazí. Jakmile data dorazí, přesouvá se zpět do stavu *ready*.

### terminated

Proces **dokončil svůj běh**. Jeho zdroje se uvolňují a PCB se ruší (s drobnou výjimkou tzv. *zombie* procesů, které ještě čekají, až si rodičovský proces vyzvedne návratový kód).

## PCB (Process Control Block)

**PCB** je blok dat, který **uchovává všechny informace o procesu**. Operační systém potřebuje vědět, kde se proces nachází, co dělá a co má v registrech, aby ho mohl kdykoliv zastavit a později pokračovat.

Typicky PCB obsahuje:

- **PID** (Process ID) — jednoznačný identifikátor procesu
- **PPID** (Parent Process ID) — PID rodičovského procesu
- **stav procesu** (new, ready, running, …)
- obsah **registrů** a program counter
- informace o **paměti** procesu
- seznam **otevřených souborů**
- a mnoho dalšího…

V **Linuxu** je PCB definován jako struktura `task_struct`, kterou najdete ve zdrojovém kódu jádra:

 [include/linux/sched.h](https://github.com/torvalds/linux/blob/master/include/linux/sched.h)

### PCB — Ukázka

Malou část PCB svého vlastního procesu si můžete zobrazit takto:

```bash
cat /proc/self/status
```

Výstup ukáže informace jako jméno procesu, PID, PPID, stav, využití paměti a další. Je to **jen část** skutečného PCB — jádro toho o každém procesu eviduje mnohem víc.

## Context switch

**Context switch** (přepínání kontextu) je mechanismus, kterým OS přepíná běh mezi procesy.

Průběh v jednoduchosti:

1. Proces **A** běží na CPU.
2. OS se rozhodne přepnout (vyprší kvantum, vyšší prioritu získá jiný proces, A se zablokuje na I/O…).
3. OS **uloží PCB procesu A** — zaznamená aktuální registry, program counter a další stav.
4. OS **načte PCB procesu B** — obnoví registry a stav z doby, kdy B naposledy běžel.
5. Proces **B** pokračuje v běhu, jako by nikdy nebyl přerušen.

Context switch není zadarmo — trvá nějaký čas a procesor během něj nevykonává užitečnou práci aplikace. Příliš časté přepínání tedy snižuje výkon.

## Plánování procesů

Představte si situaci: **máme 3 procesy a jedno CPU**. Kdo poběží první? A v jakém pořadí?

Odpověď poskytuje **plánovač** (scheduler) — součást jádra, která rozhoduje, kterému procesu v daný okamžik přidělit procesor. Existuje několik plánovacích algoritmů, pojďme si projít ty nejzákladnější.

### FCFS (First Come, First Served) — neboli FIFO

Nejjednodušší možný algoritmus: procesy se zpracovávají **v pořadí, v jakém přišly**. Kdo dřív přišel, dřív dostane CPU, a běží, dokud neskončí.

Příklad se třemi procesy A, B, C, z nichž každý poběží stejně dlouho (např. 10 jednotek času):

```
čas:    0         10        20        30
        │    A    │    B    │    C    │
```

### Problém s FCFS

Co když je ale jeden proces mnohem delší než ostatní? FCFS nemá jak ho přerušit.

Představme si, že A potřebuje 100 jednotek času, zatímco B a C jen po 10:

```
čas:    0                           100   110   120
        │            A              │  B  │  C  │
```

Procesy B a C musely zbytečně čekat 100 jednotek, i když by samy o sobě byly hotové v mrknutí oka. Tomuhle jevu se říká **convoy effect** — krátké procesy trčí za jedním dlouhým jako auta za traktorem.

### Round Robin

Round Robin problém řeší zavedením **časového kvanta**. Každý proces dostane pevně dané malé množství času (např. 10 jednotek). Když mu vyprší, OS ho **přeruší, zařadí na konec fronty** a pustí dalšího v pořadí.

Porovnejme si stejné procesy (každý potřebuje 10 jednotek) přes FIFO a Round Robin s kvantem 10:

```
FIFO:                          Round Robin (kvantum = 10):
čas: 0    10   20   30         čas: 0    10   20   30
     │ A  │ B  │ C  │               │ A  │ B  │ C  │
```

V tomto konkrétním případě vyjde výsledek stejně, protože žádný proces nepřekročí své kvantum. Kouzlo Round Robinu se projeví u **smíšených délek** — dlouhý proces nezablokuje krátké, všichni se střídají a nikdo nečeká příliš dlouho.

Nevýhoda: příliš krátké kvantum vede k častým context switchům, což stojí čas; příliš dlouhé se začne chovat jako FCFS. Volba správné velikosti kvanta je kompromis.

---

Existuje samozřejmě mnoho dalších plánovacích algoritmů (SJF, SRTF, Priority Scheduling, Multilevel Feedback Queue, CFS v Linuxu…), ty by ale vydaly na samostatnou kapitolu.
