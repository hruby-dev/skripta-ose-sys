# Stack (koncept)

## Co je to stack?

Stack (zásobník) je oblast paměti používaná pro ukládání dočasných dat při běhu programu. Funguje na principu **LIFO (Last In, First Out)** – poslední vložená hodnota je první odebraná.

Používá se zejména pro:

- lokální proměnné funkcí
- návratové adresy
- ukládání registrů


## Push / Pop

Základní operace nad stackem:

* `push` – vloží hodnotu na stack
* `pop` – odebere hodnotu ze stacku

## Stack frame

Každé volání funkce vytváří tzv. **stack frame** – blok paměti obsahující:

- lokální proměnné
- uložený base pointer (`rbp`)
- návratovou adresu


## Důležité registry

Na Linuxu (x86-64):

* `rsp` – stack pointer (ukazuje na vrchol stacku)
* `rbp` – base pointer (začátek stack frame)
* `rip` – instruction pointer (kam se pokračuje v kódu)


## Proč je stack důležitý pro exploitation?

Buffer overflow na stacku může přepsat:

- lokální proměnné
- uložený `rbp`
- **návratovou adresu (return address)**

To je klíčové, protože instrukce `ret` vezme adresu ze stacku a skočí na ni.

## De Bruijn sekvence (cyclic pattern)

Pro nalezení přesného offsetu při overflow se používá **de Bruijn sekvence** (cyclic pattern).

Výhoda:

- žádný podřetězec se neopakuje
- lze přesně určit pozici v bufferu

Generování (pwntools):

```bash
$ cyclic 200
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabe...
```

---

## Crash a nalezení offsetu

Spustíme program v GDB a pošleme pattern:

```bash
gef➤ run
Input: <cyclic pattern>
```

Po pádu programu:

```bash
gef➤ x $rsp
0x7fffffffd708: 0x6161616f
```

Hodnota `0x6161616f` je část našeho patternu.

Zjistíme offset:

```bash
$ cyclic -l 0x6161616f
56
```

Tento výstup znamená že stack offset je 56.

## Stack padding

Abychom například změnili návratovou adresu, musíme vyplnit buffer přesně správným množstvím dat:

```python
payload = b"A" * 56 + p64(addr)
```

* `A * 56` → padding
* `p64(addr)` → adresa v paměti


## Shrnutí

- stack ukládá data funkcí (proměnné, návratové adresy)
- overflow může přepsat návratovou adresu
- `ret` instrukce použije hodnotu ze stacku jako nový `rip`
- de Bruijn sekvence pomáhá najít přesný offset
- stack padding umožňuje přesně zasáhnout cílová data
