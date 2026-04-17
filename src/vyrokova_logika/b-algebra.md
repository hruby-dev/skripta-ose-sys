# Booleovská algebra

## Co je to Booleovská algebra?

Booleovská algebra je matematická struktura, která pracuje s logickými hodnotami a operacemi nad nimi. Základními prvky jsou typicky dvě hodnoty:

\\[ 0 \quad (false), \qquad 1 \quad (true) \\]

Používá se především v:

- logice
- návrhu digitálních obvodů
- informatice (podmínky, bitové operace)

## Formální definice

Booleovská algebra je uspořádaná šestice:

\\[
(B, +, \cdot, ', 0, 1)
\\]

kde:

- \\( B \\) je množina
- \\( + \\) je operace OR (součet)
- \\( \cdot \\) je operace AND (součin)
- \\( ' \\) je negace (NOT)
- \\( 0 \\) je neutrální prvek pro OR
- \\( 1 \\) je neutrální prvek pro AND

## Operace

### AND (konjunkce)

\\[
1 \cdot 1 = 1, \quad jinak\ 0
\\]

### OR (disjunkce)

\\[
1 + 0 = 1, \quad 0 + 0 = 0
\\]

### NOT (negace)

\\[
0' = 1, \quad 1' = 0
\\]


## Axiomy

Booleovská algebra splňuje následující základní vlastnosti:

### Komutativita
Vyjadřují nezávislost výsledku na pořadí operandů:

\\[
a + b = b + a
\\]
\\[
a \cdot b = b \cdot a
\\]

### Asociativita
Zajišťují, že při opakovaném použití operace nezáleží na způsobu závorkování:
\\[
a + (b + c) = (a + b) + c
\\]
\\[
a \cdot (b \cdot c) = (a \cdot b) \cdot c
\\]

### Distributivita
Umožňují vzájemné „roznášení“ operací:
\\[
a \cdot (b + c) = (a \cdot b) + (a \cdot c)
\\]
\\[
a + (b \cdot c) = (a + b) \cdot (a + c)
\\]

### Identita
Definují neutrální prvky:
\\[
a + 0 = a
\\]
\\[
a \cdot 1 = a
\\]

### Doplněk
Každý prvek má svůj doplněk:
\\[
a + a' = 1
\\]
\\[
a \cdot a' = 0
\\]

## De Morganovy zákony

De Morganovy zákony patří mezi nejdůležitější vztahy v Booleovské algebře, protože umožňují transformovat výrazy obsahující negaci.

\\[
(a \cdot b)' = a' + b'
\\]

\\[
(a + b)' = a' \cdot b'
\\]