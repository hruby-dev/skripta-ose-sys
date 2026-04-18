# Binary Exploitation

## Co je to binary exploitation?

Binary exploitation (zkráceně *pwn*) je oblast kybernetické bezpečnosti, která se zaměřuje na zneužívání chyb v binárních programech (typicky psaných v C/C++). Cílem je získat kontrolu nad chováním programu, a spustit vlastní kód, obejít omezení či získat citlivá data.

Na rozdíl od reverse engineeringu, kde se snažíme program pochopit, u binary exploitation jdeme o krok dál: hledáme zranitelnosti a aktivně je využíváme.

## Jaké chyby se využívají?

Nejčastěji jde o chyby související s pamětí, například:

- buffer overflow
- use-after-free
- double free
- out-of-bounds přístupy

Tyto chyby vznikají často kvůli:

- absenci kontroly délky vstupu
- manuální práci s pamětí
- nedostatečné validaci dat