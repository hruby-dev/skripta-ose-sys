# Logické brány

**Logická brána** je základní stavební kámen digitální elektroniky. Je to malý elektronický obvod, který přijímá jeden nebo více **binárních vstupů** (0 nebo 1, tedy "low" nebo "high" napětí) a produkuje jeden binární výstup podle pevně definovaného pravidla.

Všechno, co dnes počítače dělají — od sčítání čísel přes vykreslování 3D grafiky po spouštění operačního systému — se nakonec redukuje na miliardy logických bran, které spolu velmi rychle komunikují. Procesor s 10 miliardami tranzistorů je v podstatě obří síť logických bran.

## Proč binárně?

Digitální elektronika pracuje se dvěma stavy, protože je to **nejodolnější** proti rušení. Signál buď překročí určitou napěťovou hranici (logická 1), nebo ne (logická 0). Analogové hodnoty "mezi" by bylo mnohem těžší spolehlivě přenášet a zpracovávat.

Konvence značení:

- **0** = *false*, *low*, *off* (typicky 0 V)
- **1** = *true*, *high*, *on* (typicky 3,3 V nebo 5 V)

## Základní brány

### NOT (negace / inverter)

Nejjednodušší brána — má jeden vstup a **převrací ho**. Z 0 udělá 1 a naopak.

| A | NOT A |
|---|-------|
| 0 |   1   |
| 1 |   0   |

Zapisuje se jako `¬A`, `!A`, `~A` nebo s pruhem nad písmenem (`A̅`).

### AND (logický součin)

Výstup je 1 **jen když jsou oba vstupy 1**. Jinak je výstup 0.

| A | B | A AND B |
|---|---|---------|
| 0 | 0 |    0    |
| 0 | 1 |    0    |
| 1 | 0 |    0    |
| 1 | 1 |    1    |

Zapisuje se jako `A ∧ B`, `A & B`, `A · B`.

Intuice: "platí A **a zároveň** B".

### OR (logický součet)

Výstup je 1, pokud je **alespoň jeden** vstup 1. Výstup je 0 jen když jsou oba vstupy 0.

| A | B | A OR B |
|---|---|--------|
| 0 | 0 |   0    |
| 0 | 1 |   1    |
| 1 | 0 |   1    |
| 1 | 1 |   1    |

Zapisuje se jako `A ∨ B`, `A | B`, `A + B`.

Intuice: "platí A **nebo** B (nebo obojí)".

### XOR (exclusive OR)

Výstup je 1, pokud jsou vstupy **různé**. Pokud jsou stejné (obě 0 nebo obě 1), výstup je 0.

| A | B | A XOR B |
|---|---|---------|
| 0 | 0 |    0    |
| 0 | 1 |    1    |
| 1 | 0 |    1    |
| 1 | 1 |    0    |

Zapisuje se jako `A ⊕ B`, `A ^ B`.

Intuice: "platí buď A **nebo** B, ale **ne obojí**".

XOR má spoustu praktických využití — od detekce změny bitu, přes kryptografii, až po binární sčítání (viz dál).

### NAND (NOT AND)

Opak AND — výstup je 1 vždy, **kromě** případu, kdy jsou oba vstupy 1.

| A | B | A NAND B |
|---|---|----------|
| 0 | 0 |    1     |
| 0 | 1 |    1     |
| 1 | 0 |    1     |
| 1 | 1 |    0     |

### NOR (NOT OR)

Opak OR — výstup je 1 **jen** když jsou oba vstupy 0.

| A | B | A NOR B |
|---|---|---------|
| 0 | 0 |    1    |
| 0 | 1 |    0    |
| 1 | 0 |    0    |
| 1 | 1 |    0    |

### XNOR (NOT XOR)

Opak XOR — výstup je 1, když jsou vstupy **stejné**.

| A | B | A XNOR B |
|---|---|----------|
| 0 | 0 |    1     |
| 0 | 1 |    0     |
| 1 | 0 |    0     |
| 1 | 1 |    1     |

Užitečná třeba pro porovnávání bitů na rovnost.

## Univerzálnost NAND a NOR

Jedna zajímavá vlastnost digitální logiky: **NAND a NOR jsou takzvaně funkčně úplné (universal)**. Znamená to, že pouze z NAND bran (nebo pouze z NOR bran) jde poskládat **úplně jakoukoliv** logickou funkci.

Ukázka, jak z NAND postavit ostatní brány:

```
NOT A       =  A NAND A
A AND B     =  (A NAND B) NAND (A NAND B)
A OR B      =  (A NAND A) NAND (B NAND B)
```

V praxi je to ekonomicky výhodné — továrna vyrábějící čipy se může soustředit na výrobu jednoho typu brány a poskládat z ní cokoliv. Historicky byly celé rodiny logických obvodů (třeba NAND flash paměť) postavené právě na této myšlence.

## Kombinace bran — příklad polosčítačka

Ukažme si, jak z bran vznikají užitečné obvody. **Polosčítačka** (half adder) sčítá dva jednobitové vstupy a vrací dva výstupy: **součet** (S) a **přenos** (C, carry).

| A | B | S (součet) | C (přenos) |
|---|---|------------|------------|
| 0 | 0 |     0      |     0      |
| 0 | 1 |     1      |     0      |
| 1 | 0 |     1      |     0      |
| 1 | 1 |     0      |     1      |

Koukněte na tabulku pozorně:

- Sloupec **S** se chová jako **XOR** (1 když jsou vstupy různé)
- Sloupec **C** se chová jako **AND** (1 jen když jsou oba 1)

Polosčítačku tedy poskládáme ze **dvou bran**:

```
A ─┬──[ XOR ]── S
   │    ▲
   │    │
B ─┼────┘
   │
   └──[ AND ]── C
        ▲
        │
B ──────┘
```

Když tohle rozšíříme na **úplnou sčítačku** (full adder), která umí přijmout i vstupní přenos z nižšího bitu, a pak jich pospojujeme třeba 64 za sebou, máme **64bitovou sčítačku** — přesně to, co dělá instrukce `add` ve vašem procesoru.

## Brány vs. tranzistory

Jak se brány fyzicky realizují? Uvnitř čipu je každá brána poskládaná z **tranzistorů** — typicky v technologii **CMOS** (Complementary Metal-Oxide-Semiconductor). Jeden tranzistor funguje jako malý spínač, který propustí proud, když na jeho řídící vstup přivedete napětí.

Orientační počty tranzistorů na bránu v CMOS:

- NOT: 2 tranzistory
- NAND / NOR: 4 tranzistory
- AND / OR: 6 tranzistorů (= NAND/NOR + NOT)
- XOR / XNOR: typicky 8–12 tranzistorů

Proto jsou NAND a NOR v čipech **levnější** než AND a OR — a proto je tolik hardwarových návrhů postavených kolem nich.

## Boolova algebra

Logické brány přímo odpovídají operacím **Boolovy algebry** (kterou v 19. století formalizoval matematik George Boole). Pár užitečných identit, které se hodí při zjednodušování obvodů:

```
A · 0  =  0           A + 0  =  A
A · 1  =  A           A + 1  =  1
A · A  =  A           A + A  =  A
A · A̅  =  0           A + A̅  =  1
```

A dvě slavné **De Morganovy zákony**, které umožňují převádět mezi AND a OR přes negaci:

```
¬(A · B)  =  ¬A + ¬B
¬(A + B)  =  ¬A · ¬B
```

Zjednodušování logických výrazů je důležité, protože v reálném návrhu čipu **každá brána navíc znamená více tranzistorů, více plochy a vyšší spotřebu**. Minimalizace boolovských funkcí (Karnaughovy mapy, Quine–McCluskey) je proto základní technika v digitálním designu.

## Shrnutí

- **7 základních bran**: NOT, AND, OR, XOR, NAND, NOR, XNOR
- Každá brána je pravidlo, které mapuje binární vstupy na binární výstup
- Z **NAND** (nebo **NOR**) samotných jde postavit jakýkoliv digitální obvod
- Kombinacemi bran vznikají **sčítačky, multiplexory, registry, paměti** — a nakonec celé **procesory**
- Pravidla pro zjednodušování obvodů popisuje **Boolova algebra**

Každý `mov`, `add` nebo `xor`, který jsme viděli v kapitole o assembly, se uvnitř procesoru rozkládá právě na práci stovek milionů těchto bran. Všechno, co počítač dělá, je ve výsledku jen velmi rychlé přepínání tranzistorů podle logických pravidel.
