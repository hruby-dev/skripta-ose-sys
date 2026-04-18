## Výroková logika

### Co je výroková logika

Výroková logika (též **propoziční logika**) je základní formální systém, který se zabývá studiem pravdivostních hodnot výroků a jejich kombinací pomocí logických operací.

**Výrok** je tvrzení, o němž má smysl rozhodnout, zda je pravdivé nebo nepravdivé. Každému výroku přiřazujeme právě jednu z hodnot:
\\[
0 \quad (\text{false}), \qquad 1 \quad (\text{true}).
\\]

Množinu všech výroků budeme značit \\(P\\).

### Význam v informatice

Výroková logika má zásadní význam v informatice, zejména v těchto oblastech:

- návrh a analýza algoritmů (podmínky, větvení),
- konstrukce a optimalizace digitálních obvodů,
- formální verifikace programů,
- databázové dotazy (např. podmínky ve WHERE),
- umělá inteligence a logické odvozování.

## Logické operace

Základní logické spojky umožňují vytvářet složené výroky.

### Negace (NOT)
Negace obrací výsledek výroku

\\[
\neg p
\\]

| \\(P\\) | \\(\neg P\\) |
| --- | -------- |
| 0   | 1        |
| 1   | 0        |

### Konjunkce (AND)
Konjunkce je pravdivá v případě že jsou oba výroky pravidvé.
\\[
P \land Q
\\]

| \\(P\\) | \\(Q\\) | \\(P \land Q\\) |
| --- | --- | ----------- |
| 0   | 0   | 0           |
| 0   | 1   | 0           |
| 1   | 0   | 0           |
| 1   | 1   | 1           |

### Disjunkce (OR)
Disjunkce je pravdivá v případě že je alespoň jeden z výroků pravdivý.

\\[
P \lor Q
\\]

| \\(P\\) | \\(Q\\) | \\(P \lor Q\\) |
| --- | --- | ---------- |
| 0   | 0   | 0          |
| 0   | 1   | 1          |
| 1   | 0   | 1          |
| 1   | 1   | 1          |

### Implikace (IF–THEN)
Implikace vyjadřuje vzah "if A, then B", kde z pravdivosti prvního výroku vyplývá pravdivost druhého.
\\[
p \rightarrow q
\\]

| (p) | (q) | (p \rightarrow q) |
| --- | --- | ----------------- |
| 0   | 0   | 1                 |
| 0   | 1   | 1                 |
| 1   | 0   | 0                 |
| 1   | 1   | 1                 |


### Ekvivalence (IFF)
Ekvivalence je pravdivá, právě když mají oba výroky stejnou pravdivostní hodnotu.
\\[
P \leftrightarrow Q
\\]

| \\(P\\) | \\(Q\\) | \\(P \leftrightarrow Q\\) |
| --- | --- | --------------------- |
| 0   | 0   | 1                     |
| 0   | 1   | 0                     |
| 1   | 0   | 0                     |
| 1   | 1   | 1                     |

## Složené výroky

Složené výroky vznikají kombinací výroků pomocí logických spojek. Například:
\\[
\neg (P \land Q) \rightarrow R
\\]

Pravdivost takového výroku závisí na hodnotách jednotlivých proměnných a lze ji určit pomocí pravdivostní tabulky.

### Splnitelný výrok

Výrok, který je pravdivý alespoň pro jedno ohodnocení.

## Shrnutí

Výroková logika poskytuje formální nástroje pro práci s pravdivostními hodnotami a jejich kombinacemi a umožňuje přesně definovat význam logických výrazů. Díky jednoduchosti a univerzálnosti tvoří výroková logika základ mnoha oblastí informatiky i matematiky.
